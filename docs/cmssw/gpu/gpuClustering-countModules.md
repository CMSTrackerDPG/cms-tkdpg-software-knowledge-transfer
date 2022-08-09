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

###### `uint16_t const* __restrict__ id` [Input]

This is an array (with length equal to the total number of digis), which
identifies the module id that each digi corresponds to.

This `id` is **NOT** the same with the `DetId`, but it's a GPU-only identifier.

###### `uint32_t* __restrict__ moduleStart` [Output]

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

* Using `atomicInc()`, the value at `moduleStart[0]` is incremented
  by one, i.e: `moduleStart[0] + 1`[^1].
* `loc` will contain the value of `moduleStart[0]`, **before** the `atomicInc()` operation,
  meaning it will be equal to `moduleStart[0]`.
  
This, in effect, counts the total times that the `cond` mentioned above was
evaluated to `True`, which, in effect, corresponds to the **total number of modules
encountered.**

Since the value returned by the `atomicInc()` (stored in `loc`) 
is the `moduleStart[0]` value prior
to its incrementation, it acts, indirectly, as an index to store consecutively
in the `moduleStart` array the indices of the elements where `cond == T`.

Therefore, for each `cond == T` we are:

* Incrementing the `moduleStart[0]` element by 1.
* Storing the index `i` in the next available `moduleStart` array position.

[^1]: The value will be set to `0` **if the value stored  in `moduleStart[0]` exceeds the `nMaxModules` value**, i.e. `3892` for Phase 2.

We fill the `moduleStart` array with starting module indices. Note that we can't make sure that the first module we mark is `A` and then `B`, etc. This code is executed competitively by all of the GPU threads
so the final `moduleStart` array will differ from execution to execution, even if the input
data is the same:

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

!!! quote "The last row of the table above is read as follows:"

	* There is a total of `4` indices stored in this array.
	* One module starts at index `13` (in our example, module `D`)
	* One module starts at index `0` (in our example, module `A`)	
	* One module starts at index `9` (in our example, module `C`)	
	* One module starts at index `5` (in our example, module `B`)
	
The order that the indices are stored in `moduleStart` is determined by the order
which threads reach line `18`.


!!! note "Important note 1"

	In the end, `moduleStart[0]` records the total number of modules
	found in the array.
	
	Then, since we store the **indices** of the array elements where the module
	`id` changes, it means that the `moduleStart` array contains
	at most `nMaxModules + 1` elements (to account for `moduleStart[0]` which
	stores the number of indices contained in the array). 
	
	It follows that the number of elements in `moduleStart` is **less** than
	the total number of digis.
	
!!! note "Important note 2"	

	Not all modules contain the same number of pixels, since some will be invalid.
	
??? note "Example contents of `moduleStart` (after sorting, starting from element 1)"

	In this example, the first element still represents the total number of modules. The
	rest are sorted indices to where the data of each module starts.
	
	`0` does not seem to be included in this array, as it is always implied
	that data starts at 0.

	`1727,8,28,49,59,63,68,81,194,198,206,233,243,270,306,401,427,436,444,453,479,512,528,589,597,612,627,631,639,777,824,857,890,912,955,980,990,1021,1034,1052,1137,1142,1182,1251,1267,1316,1324,1331,1425,1474,1656,1669,1689,1704,1754,1805,1828,1838,1848,1856,1866,2043,2067,2090,2112,2128,2131,2196,2226,2336,2367,2392,2573,2622,2676,3140,3173,3213,3219,3225,3233,3269,3290,3300,3303,3310,3358,3406,3460,3488,3512,3518,3521,3572,3597,3605,3619,3625,3665,3668,3688,3766,3773,3792,3831,3843,3857,3877,3946,4245,4271,4300,4310,4313,4325,4332,4513,4528,4537,4550,4562,4590,4612,4665,4683,4689,4692,4722,4907,4935,4969,4988,5002,5024,5029,5039,5091,5117,5138,5163,5166,5175,5225,5243,5276,5294,5315,5353,5368,5388,5414,5619,5638,5695,5709,5745,5758,5775,5849,5858,5873,5887,5905,5913,5930,5952,6011,6045,6052,6057,6090,6173,6189,6216,6224,6242,6248,6258,6265,6297,6328,6452,6524,6564,6566,6572,6578,6590,6700,6707,6720,6733,6748,6770,6800,6810,6848,6851,7055,7076,7095,7116,7138,7141,7144,7147,7157,7177,7208,7239,7263,7280,7297,7304,7336,7468,7502,7595,7641,7643,7676,7698,7825,7836,7856,7869,7879,7906,7964,8065,8098,8112,8118,8137,8162,8313,8422,8521,8531,8546,8559,8566,8574,8609,8658,8667,8711,8731,8762,8773,8795,8801,8988,9008,9048,9055,9062,9065,9068,9266,9274,9275,9280,9284,9342,9418,9482,9498,9502,9513,9528,9539,9718,9724,9738,9758,9764,9772,9779,9788,9809,9838,9900,9983,10018,10029,10085,10116,10131,10141,10156,10168,10185,10202,10236,10346,10399,10412,10425,10460,10463,10467,10598,10609,10612,10616,10622,10675,10730,10745,10756,10761,10772,10776,10784,10936,10976,11014,11024,11052,11066,11072,11086,11088,11136,11174,11333,11361,11391,11395,11406,11419,11422,11549,11557,11572,11577,11590,11665,11690,11706,11712,11725,11750,11870,11879,11906,11928,11964,11970,11983,12037,12056,12064,12079,12086,12113,12164,12172,12203,12210,12352,12381,12445,12577,12585,12608,12659,12697,12795,12828,12844,12866,12896,12923,12948,12973,12982,12993,13054,13082,13090,13097,13234,13281,13314,13316,13325,13350,13362,13526,13533,13548,13564,13574,13619,13692,13731,13754,13775,13784,13793,13802,13885,13916,13925,13936,13945,13963,13967,13984,14003,14040,14074,14089,14105,14108,14135,14139,14158,14175,14228,14246,14263,14287,14318,14359,14387,14391,14406,14417,14524,14540,14546,14571,14584,14629,14668,14711,14735,14743,14752,14776,14777,14973,15000,15045,15074,15075,15094,15096,15102,15117,15142,15260,15312,15359,15375,15393,15410,15447,15644,15717,15730,15737,15770,15826,15868,15893,15900,15914,16088,16107,16123,16139,16146,16181,16192,16197,16203,16224,16240,16266,16277,16291,16351,16368,16578,16605,16671,16683,16686,16701,16862,16867,16871,16875,16891,16939,16982,17085,17127,17143,17168,17184,17202,17343,17368,17408,17416,17458,17495,17501,17535,17564,17621,17660,17691,17720,17763,17779,17807,17817,17831,17948,17968,17994,18007,18014,18018,18027,18137,18143,18227,18230,18239,18256,18298,18327,18388,18403,18406,18420,18433,18443,18451,18475,18509,18521,18540,18574,18640,18674,18688,18701,18737,18775,18797,18815,18825,18867,18938,18966,18992,19082,19134,19161,19179,19190,19314,19318,19324,19332,19344,19370,19396,19463,19488,19506,19517,19539,19566,19785,19803,19812,19837,19840,19851,19869,19892,19931,19986,20122,20147,20187,20302,20305,20310,20318,20402,20414,20416,20422,20431,20691,20702,20709,20733,20762,20767,20777,20795,20796,20809,20853,20859,20878,20895,20911,20925,20950,21161,21239,21278,21279,21285,21290,21293,21414,21418,21419,21444,21466,21524,21553,21560,21571,21584,21592,21820,21836,21851,21871,21875,21877,21879,21882,21885,21921,21962,21991,22028,22036,22053,22060,22070,22102,22385,22406,22480,22488,22497,22512,22669,22681,22704,22707,22719,22763,22808,22870,22905,22920,22930,22939,23076,23120,23152,23170,23201,23214,23218,23231,23238,23278,23324,23354,23365,23375,23417,23440,23459,23471,23483,23498,23553,23566,23588,23756,23781,23809,23817,23825,23852,23853,24127,24136,24150,24166,24179,24201,24240,24315,24362,24378,24389,24427,24586,24618,24638,24650,24666,24671,24674,24737,24832,24903,24946,25088,25114,25125,25142,25150,25159,25351,25375,25380,25388,25401,25423,25506,25520,25537,25546,25793,25801,25822,25841,25884,25886,25896,25903,25955,25979,26003,26044,26089,26097,26113,26122,26178,26269,26292,26309,26339,26347,26393,26413,26505,26524,26533,26539,26554,26602,26615,26661,26676,26681,26692,26829,26838,26863,26877,26899,26900,26912,26914,26930,26979,27012,27048,27051,27067,27081,27092,27102,27104,27224,27229,27270,27284,27291,27307,27308,27492,27500,27501,27503,27517,27559,27596,27601,27621,27623,27643,27660,27880,27907,27918,27938,27963,27966,27971,27979,28002,28052,28100,28106,28128,28142,28147,28154,28178,28193,28212,28270,28325,28349,28393,28402,28407,28598,28613,28617,28632,28644,28667,28692,28726,28741,28744,28746,28755,28773,28937,28955,28956,29048,29054,29060,29063,29116,29136,29235,29253,29294,29306,29319,29326,29328,29481,29487,29492,29495,29498,29540,29564,29571,29584,29595,29716,29739,29744,29804,29814,29818,29853,29857,29877,29884,29911,29922,29940,29951,29966,30153,30203,30237,30252,30262,30267,30389,30401,30424,30434,30440,30460,30482,30527,30552,30559,30564,30587,30591,30841,30865,30881,30905,30919,30930,30953,31005,31036,31063,31083,31086,31094,31102,31115,31135,31269,31308,31331,31340,31345,31363,31382,31464,31470,31472,31481,31488,31530,31590,31595,31624,31631,31632,31640,31651,31790,31796,31807,31840,31845,31862,31868,31882,31895,31940,31960,31973,32015,32040,32058,32065,32077,32087,32096,32113,32155,32182,32333,32362,32380,32387,32420,32427,32433,32536,32542,32544,32557,32569,32624,32640,32684,32697,32704,32707,32718,32726,32912,32928,32943,32974,32987,33002,33022,33030,33037,33077,33102,33209,33248,33279,33283,33293,33298,33303,33513,33522,33534,33544,33551,33566,33584,33590,33600,33614,33796,33810,33844,33869,33880,33882,33890,33896,33904,33930,33931,33941,33945,33951,33973,34002,34024,34072,34120,34158,34169,34175,34181,34198,34354,34369,34379,34386,34432,34462,34491,34545,34549,34555,34561,34580,34685,34701,34737,34757,34766,34769,34773,34799,34816,34833,34854,34882,34932,34961,34980,35057,35099,35105,35232,35260,35269,35284,35325,35340,35353,35360,35433,35487,35500,35517,35547,35594,35603,35613,35678,35712,35738,35750,35784,35858,35876,35914,35952,36000,36008,36020,36060,36087,36097,36112,36139,36163,36199,36230,36245,36267,36440,36448,36464,36484,36500,36508,36512,36530,36548,36561,36565,36698,36726,36841,36868,36913,36935,36951,36980,37006,37017,37028,37046,37086,37102,37126,37141,37153,37163,37175,37219,37250,37275,37299,37322,37387,37394,37432,37499,37506,37543,37586,37635,37707,37740,37756,37817,37874,37882,37892,37923,37985,37994,38014,38028,38033,38042,38053,38083,38113,38144,38168,38201,38231,38239,38246,38276,38318,38335,38355,38402,38444,38501,38525,38552,38598,38625,38648,38663,38684,38700,38727,38741,38767,38823,38854,38880,38886,38902,38916,38940,38957,39008,39042,39077,39101,39161,39194,39214,39233,39291,39318,39349,39365,39416,39462,39473,39518,39541,39608,39624,39633,39660,39717,39731,39770,39804,39814,39823,39905,39966,39981,40008,40070,40102,40110,40137,40147,40192,40207,40222,40258,40313,40365,40374,40404,40452,40464,40474,40520,40551,40559,40579,40633,40663,40699,40721,40751,40808,40827,40859,40899,40919,40928,40949,40996,41010,41054,41068,41107,41124,41157,41162,41177,41204,41222,41244,41266,41311,41333,41348,41361,41374,41400,41445,41469,41516,41532,41544,41552,41575,41602,41608,41626,41635,41646,41672,41676,41699,41746,41795,41797,41825,41846,41876,41895,41914,41945,41977,42008,42030,42049,42151,42211,42242,42278,42318,42327,42358,42419,42431,42439,42466,42510,42522,42541,42599,42644,42650,42714,42734,42773,42790,42814,42875,42898,42905,42919,42971,43011,43079,43094,43115,43145,43152,43169,43194,43229,43236,43252,43279,43334,43357,43367,43374,43388,43400,43437,43452,43465,43478,43485,43494,43504,43546,43584,43597,43606,43638,43672,43698,43711,43761,43804,43836,43879,43904,43920,43942,43998,44008,44013,44047,44084,44094,44108,44141,44181,44231,44284,44298,44372,44384,44406,44434,44504,44514,44526,44555,44573,44644,44689,44728,44766,44827,44850,44871,44887,44938,44975,45002,45015,45062,45139,45158,45163,45211,45278,45316,45356,45448,45566,45583,45611,45648,45685,45693,45697,45774,45867,45877,45899,45951,46005,46034,46046,46054,46064,46141,46158,46160,46192,46223,46232,46264,46274,46302,46335,46352,46381,46445,46507,46527,46542,46586,46622,46648,46659,46675,46681,46705,46721,46740,46753,46768,46775,46778,46781,46790,46819,46845,46866,46876,46927,46975,46979,46993,47024,47068,47089,47107,47143,47151,47212,47281,47302,47345,47385,47389,47412,47454,47518,47554,47575,47604,47645,47653,47672,47701,47757,47766,47774,47810,47853,47869,47870,47886,47918,48010,48018,48051,48093,48111,48135,48175,48226,48228,48244,48250,48272,48286,48292,48298,48315,48334,48374,48414,48444,48499,48514,48515,48544,48604,48609,48620,48638,48669,48678,48692,48731,48760,48763,48792,48826,48835,48852,48905,48914,48935,48954,48977,49035,49053,49107,49188,49227,49269,49287,49331,49337,49348,49383,49408,49429,49453,49553,49587,49596,49653,49712,49783,49791,49809,49860,49888,49902,49909,49972,50015,50037,50087,50131,50155,50182,50223,50261,50279,50301,50340,50364,50386,50406,50459,50508,50551,50579,50595,50617,50639,50658,50671,50686,50718,50733,50747,50781,50812,50836,50882,50894,50957,51011,51032,51043,51084,51113,51157,51185,51262,51264,51266,51282,51304,51307,51311,51334,51336,51339,51361,51404,51407,51410,51471,51655,51667,51677,51727,51781,51830,51900,51949,52006,52050,52073,52091,52108,52156,52192,52203,52210,52224,52240,52252,52256,52264,52277,52283,52293,52309,52321,52331,52338,52342,52349,52357,52381,52412,52451,52459,52470,52538,52643,52645,52657,52729,52770,52781,52812,52852,52897,52912,52950,52974,52998,53008,53010,53018,53033,53073,53089,53117,53121,53143,53168,53181,53191,53266,53307,53350,53354,53387,53412,53421,53473,53503,53529,53542,53545,53562,53585,53591,53600,53621,53636,53640,53642,53677,53713,53727,53735,53781,53865,53883,53887`




```cuda linenums="18"
auto loc = atomicInc(moduleStart, nMaxModules);
```

