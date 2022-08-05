# countModules

!!! todo

	* Add more detailed introduction
	* How is the data split? How much works is done per thread?	
	
CUDA kernel which implements the following purposes:

* Initialize the `clusterId` array, to be used in later code
(in the [`findClus`](./gpuClustering-findClus.md) kernel),
* Filter out invalid modules ({==TODO: what does it mean for
a module to be invalid?==})
* Fill the `moduleStart` array, which is an array of indices which
point to the first element of the SoA data which corresponds to each module.
Since data is stored in
a SoA format, the data of all modules is stored in 
**a non-consecutive way, as far as modules are concerned,** in a single
1D array. The `moduleStart` indices are therefore required for
accessing the data of each module. See the
[`Data Structure`](./index.md#data-structure) section for more information.


## Code

??? quote "Kernel code"

	``` cuda linenums="1" title="countModules kernel"
	  template <bool isPhase2>
	  __global__ void countModules(uint16_t const* __restrict__ id,
	                               uint32_t* __restrict__ moduleStart,
	                               int32_t* __restrict__ clusterId,
	                               int numElements) {
	    int first = blockDim.x * blockIdx.x + threadIdx.x;
	    constexpr int nMaxModules = isPhase2 ? phase2PixelTopology::numberOfModules : phase1PixelTopology::numberOfModules;
	    assert(nMaxModules < maxNumModules);
	    for (int i = first; i < numElements; i += gridDim.x * blockDim.x) {
	      clusterId[i] = i;
	      if (invalidModuleId == id[i])
	        continue;
	      auto j = i - 1;
	      while (j >= 0 and id[j] == invalidModuleId)
	        --j;
	      if (j < 0 or id[j] != id[i]) {
	        // boundary...
	        auto loc = atomicInc(moduleStart, nMaxModules);
	        moduleStart[loc + 1] = i;
	      }
	    }
	  }
	```

## Detailed explanation

### 0. Introduction

#### 0.0 Arguments

##### `uint16_t const* __restrict__ id` [Input]

This is an array (with length equal to the total number of digis), which
identifies the module id that each digi corresponds to.

This `id` is **NOT** the same with the `DetId`, but it's a GPU-only identifier.

##### `uint32_t* __restrict__ moduleStart` [Output]

An array of indices that {==????==}???

#### 0.1 Implementation Details

* The `first` variable contains the **global thread id** within
  the kernel.
* `numElements` == `wordCounter` {==???is this the same to the total number of digis???==}
* Each thread is responsible for more than one digis, i.e. if there are less blocks than 
  required to cover all the digis, each thread will also iterate with step equal to
  **number of blocks** * **threads per block**. This is done with the `for` loop.

### 1. Init for clustering

We initialise the `clusterId`s for the `findClus` kernel.

``` cuda linenums="9"
for (int i = first; i < numElements; i += gridDim.x * blockDim.x) {
    clusterId[i] = i;
```

This part of the code that has nothing to do with counting the modules yet.

### 2. Digi order

Let's say we have a snippet from our `id` array.

Instead of having numbers for the `id` we'll use letters, `A`, `B`, `C` and `D`, and mark
**invalid** module ids with ❌.

<table>
    <tr>
        <th><code>id</code></th><td>A</td><td>A</td><td>❌</td><td>❌</td><td>A</td><td>B</td><td>❌</td><td>B</td><td>B</td><td>C</td><td>C</td><td>❌</td><td>❌</td><td>D</td>
    </tr>
</table>

!!! warning "Digis ordered by modules"

    It is a prerequisite and we know that digis belonging to one module will appear 
	**consecutive** in our buffer. They might be separated by **invalid digis/hits**.

### 3. Look for boundary elements

Let's use our example digi array from the previous point.

In the first row we'll show `id` and in the second column the `threadIdx.x`.

<table>
    <tr>
        <th><code>id</code></th><td>A</td><td>A</td><td>❌</td><td>❌</td><td>A</td><td>B</td><td>❌</td><td>B</td><td>B</td><td>C</td><td>C</td><td>❌</td><td>❌</td><td>D</td>
    </tr>
    <tr>
        <th><code>threadIdx.x</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
</table>

Let's execute some of our code:

```cuda linenums="11"
if (invalidModuleId == id[i])
  continue;
auto j = i - 1;
```

!!! note

	`i` is the unique index of each digi.
	
	`j` is the unique index of the previous (in regard to `i`) **valid** digi.

<table>
    <tr>
        <th><code>id</code></th><td>A</td><td>A</td><td>❌</td><td>❌</td><td>A</td><td>B</td><td>❌</td><td>B</td><td>B</td><td>C</td><td>C</td><td>❌</td><td>❌</td><td>D</td>
    </tr>
    <tr>
        <th><code>threadIdx.x</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
    <tr>
        <th><code>i</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
    <tr>
        <th><code>j</code></th><td>-1</td><td>0</td><td>❌</td><td>❌</td><td>3</td><td>4</td><td>❌</td><td>6</td><td>7</td><td>8</td><td>9</td><td>❌</td><td>❌</td><td>12</td>
    </tr>
</table>

Next:

``` cuda linenums="14"
while (j >= 0 and id[j] == invalidModuleId)
  --j;
```

This means that we keep going back, checking previous digis, **until we stop finding
digis which belong to invalid modules**. In the end, **`j` will hold the index
of `i`th digi's closest valid backward neighbour incremented by 1:**

<table>
    <tr>
        <th><code>id</code></th><td>A</td><td>A</td><td>❌</td><td>❌</td><td>A</td><td>B</td><td>❌</td><td>B</td><td>B</td><td>C</td><td>C</td><td>❌</td><td>❌</td><td>D</td>
    </tr>
    <tr>
        <th><code>threadIdx.x</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
    <tr>
        <th><code>i</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
    <tr>
        <th><code>j</code> before</th><td>-1</td><td>0</td><td>❌</td><td>❌</td><td>3</td><td>4</td><td>❌</td><td>6</td><td>7</td><td>8</td><td>9</td><td>❌</td><td>❌</td><td>12</td>
    </tr>
    <tr>
        <td>while</td><td></td><td></td><td></td><td></td><td>↓</td><td></td><td></td><td>↓</td><td></td><td></td><td></td><td></td><td></td><td>↓</td>
    </tr>
    <tr>
        <th><code>j</code> after</th><td>-1</td><td>0</td><td>❌</td><td>❌</td><td>1</td><td>4</td><td>❌</td><td>5</td><td>7</td><td>8</td><td>9</td><td>❌</td><td>❌</td><td>10</td>
    </tr>
</table>

It's now time to check at which index the `id` value changes (i.e.: the data of the next module
begins):

```cuda linenums="16"
if (j < 0 or id[j] != id[i]) {
  // boundary...
  auto loc = atomicInc(moduleStart, nMaxModules);
  moduleStart[loc + 1] = i;
}
```

Let's set `cond = (j < 0 or id[j] != id[i])`. Check when this will be true (`T` is true, `F` is false, ❌ is not evaluated because that thread terminated early due to `id` being equal to `invalidModuleId`):

<table>
    <tr>
        <th><code>id</code></th><td>A</td><td>A</td><td>❌</td><td>❌</td><td>A</td><td>B</td><td>❌</td><td>B</td><td>B</td><td>C</td><td>C</td><td>❌</td><td>❌</td><td>D</td>
    </tr>
    <tr>
        <th><code>threadIdx.x</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
    <tr>
        <th><code>i</code></th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td><td>7</td><td>8</td><td>9</td><td>10</td><td>11</td><td>12</td><td>13</td>
    </tr>
    <tr>
        <th><code>j</code> after</th><td>-1</td><td>0</td><td>❌</td><td>❌</td><td>1</td><td>4</td><td>❌</td><td>5</td><td>7</td><td>8</td><td>9</td><td>❌</td><td>❌</td><td>10</td>
    </tr>
    <tr>
        <th><code>cond</code></th><td>T</td><td>F</td><td>❌</td><td>❌</td><td>F</td><td>T</td><td>❌</td><td>F</td><td>F</td><td>T</td><td>F</td><td>❌</td><td>❌</td><td>T</td>
    </tr>
</table>

Now, let's look at the `id` and `cond`, getting rid of `False` `cond` and
invalid `id`s to better see what is happening:

<table>
    <tr>
        <th><code>id</code></th><td>A</td><td>A</td><td>❌</td><td>❌</td><td>A</td><td>B</td><td>❌</td><td>B</td><td>B</td><td>C</td><td>C</td><td>❌</td><td>❌</td><td>D</td>
    </tr>
    <tr>
        <th><code>cond</code></th><td>T</td><td></td><td></td><td></td><td></td><td>T</td><td></td><td></td><td></td><td>T</td><td></td><td></td><td></td><td>T</td>
    </tr>
</table>

{==Wherever the `cond` is `T`, the data of a new module begins==}.

### 4. set `moduleStart` for each module

``` cuda linenums="18"
auto loc = atomicInc(moduleStart, nMaxModules);
moduleStart[loc + 1] = i;
```

??? note "[`atomicInc`](https://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#atomicinc) documentation"

	```cuda
	unsigned int atomicInc(unsigned int* address,
                       unsigned int val);
	```
	reads the 32-bit word `old` located at the address `address` in global
	or shared memory, computes `((old >= val) ? 0 : (old+1))`, and stores
	the result back to memory at the same address. These three operations
	are performed in one atomic transaction. The function returns `old`. 
	
After execution of the lines above, the following takes place:

* Using `atomicInc()`, the value at `moduleStart[0]` is altered to either `0`
  or `moduleStart[0] + 1`. The value will be `0` **only if the value stored
  in `moduleStart[0]` exceeds the `nMaxModules` value**, i.e. `3892` for Phase 2.
* `loc` will contain the value of `moduleStart[0]`, after the `atomicInc()` operation,
  meaning it will also be either `0` or `moduleStart[0] + 1`.

We fill the `moduleStart` array with starting module indices. Note that we can't make sure that the first module we mark is `A` and then `B`, etc. This code is executed competitively so we might have different `moduleStart` array each execution:

<table>
    <tr>
        <th>pos</th><td>0</td><td>1</td><td>2</td><td>3</td><td>4</td><td>5</td><td>6</td>
    </tr>
    <tr>
        <th><code>moduleStart</code> (execution i)</th><td>4</td><td>0</td><td>5</td><td>9</td><td>13</td><td>0</td><td>0</td>
    </tr>
    <tr>
        <th><code>moduleStart</code> (execution ii)</th><td>4</td><td>0</td><td>9</td><td>13</td><td>5</td><td>0</td><td>0</td>
    </tr>
    <tr>
        <th><code>moduleStart</code> (execution iii)</th><td>4</td><td>13</td><td>0</td><td>9</td><td>5</td><td>0</td><td>0</td>
    </tr>
</table>

The order will be determined by in what order each thread reaches the line `18`.

```cuda linenums="18"
auto loc = atomicInc(moduleStart, nMaxModules);
```

!!! note "Important note"

	In the end, `moduleStart[0]` records the total number of modules
	found in the array.
