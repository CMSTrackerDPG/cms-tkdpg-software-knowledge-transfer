# Debugging CMSSW

First, build CMSSW with the `-O0 -g` flags (see [Building](build.md)).

Then, assuming you already have a configuration file to run via `cmsRun`,
follow the instructions [here](https://twiki.cern.ch/twiki/bin/view/Main/HomerWolfeCMSSWAndGDB).

## Quick reference

### Set Breakpoints

#### By file line

```gdb
break '/absolute/path/to/file.c':LINE
```

E.g.:

```gdb
break '/data/user/dpapagia/cmssw/CMSSW_13_0_X_2023-01-09-1100/src/RecoPixelVertexing/PixelTriplets/plugins/CAHitNtupletGeneratorOnGPU.cc':293
```

You should see a response like this:

```
Make breakpoint pending on future shared library load? (y or [n]) y
Breakpoint 3 ('/data/user/dpapagia/cmssw/CMSSW_13_0_X_2023-01-09-1100/src/RecoPixelVertexing/PixelTriplets/plugins/CAHitNtupletGeneratorOnGPU.cc':293) pending.
```

#### By function name

E.g.:

```gdb
break TrackingRecHitSoADevice<pixelTopology::Phase1>::TrackingRecHitSoADevice
```

## Print the type of a variable

```gdb
ptype <name of variable in scope>
```

## Print contents of variable

After hitting a breakpoint, you can do something like:

```gdb
p <name of variable in scope>
```

You will see a response like this:

```
$4 = {<cms::cuda::PortableHostCollection<TrackingRecHitSoA<pixelTopology::Phase1>::TrackingRecHitSoALayout<128, false> >> = {buffer_ = {_M_t = {<std::__uniq_ptr_impl<std::byte, cms::cuda::host::impl::HostDeleter>> = {
          _M_t = {<std::_Tuple_impl<0, std::byte*, cms::cuda::host::impl::HostDeleter>> = {<std::_Tuple_impl<1, cms::cuda::host::impl::HostDeleter>> = {<std::_Head_base<1, cms::cuda::host::impl::HostDeleter, false>> = {_M_head_impl = {
                    type_ = cms::cuda::host::impl::MemoryType::kDefault}}, <No data fields>}, <std::_Head_base<0, std::byte*, false>> = {_M_head_impl = 0x7ffef978bc80}, <No data fields>}, <No data fields>}}, <No data fields>}}, layout_ = {static alignment = 128, 
      static alignmentEnforcement = false, static conditionalAlignment = 0, mem_ = 0x7ffef978bc80, elements_ = 8, scalar_ = 1, byteSize_ = 23808, xLocal_ = 0x7ffef978bc80, yLocal_ = 0x7ffef978bd00, xerrLocal_ = 0x7ffef978bd80, yerrLocal_ = 0x7ffef978be00, 
      xGlobal_ = 0x7ffef978be80, yGlobal_ = 0x7ffef978bf00, zGlobal_ = 0x7ffef978bf80, rGlobal_ = 0x7ffef978c000, iphi_ = 0x7ffef978c080, chargeAndStatus_ = 0x7ffef978c100, clusterSizeX_ = 0x7ffef978c180, clusterSizeY_ = 0x7ffef978c200, detectorIndex_ = 0x7ffef978c280, 
      nHits_ = 0x7ffef978c300, offsetBPIX2_ = 0x7ffef978c380, phiBinnerStorage_ = 0x7ffef978c400, hitsModuleStart_ = 0x7ffef978c480, hitsLayerStart_ = 0x7ffef978e200, cpeParams_ = 0x7ffef978e280, averageGeometry_ = 0x7ffef978e300, phiBinner_ = 0x7ffef978f100}, 
    view_ = {<TrackingRecHitSoA<pixelTopology::Phase1>::TrackingRecHitSoALayout<128, false>::ConstViewTemplateFreeParams<128, false, true, false>> = {static alignment = 128, static alignmentEnforcement = false, static conditionalAlignment = 0, static restrictQualify = true, 
        elements_ = 8, xLocalParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978bc80}, yLocalParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978bd00}, xerrLocalParameters_ = {
          static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978bd80}, yerrLocalParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978be00}, xGlobalParameters_ = {static columnType = cms::soa::SoAColumnType::column, 
          addr_ = 0x7ffef978be80}, yGlobalParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978bf00}, zGlobalParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978bf80}, rGlobalParameters_ = {
          static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978c000}, iphiParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978c080}, chargeAndStatusParameters_ = {static columnType = cms::soa::SoAColumnType::column, 
          addr_ = 0x7ffef978c100}, clusterSizeXParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978c180}, clusterSizeYParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978c200}, detectorIndexParameters_ = {
          static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978c280}, nHitsParameters_ = {static columnType = cms::soa::SoAColumnType::scalar, addr_ = 0x7ffef978c300}, offsetBPIX2Parameters_ = {static columnType = cms::soa::SoAColumnType::scalar, 
          addr_ = 0x7ffef978c380}, phiBinnerStorageParameters_ = {static columnType = cms::soa::SoAColumnType::column, addr_ = 0x7ffef978c400}, hitsModuleStartParameters_ = {static columnType = cms::soa::SoAColumnType::scalar, addr_ = 0x7ffef978c480}, hitsLayerStartParameters_ = {
          static columnType = cms::soa::SoAColumnType::scalar, addr_ = 0x7ffef978e200}, cpeParamsParameters_ = {static columnType = cms::soa::SoAColumnType::scalar, addr_ = 0x7ffef978e280}, averageGeometryParameters_ = {static columnType = cms::soa::SoAColumnType::scalar, 
          addr_ = 0x7ffef978e300}, phiBinnerParameters_ = {static columnType = cms::soa::SoAColumnType::scalar, addr_ = 0x7ffef978f100}}, static alignment = 128, static alignmentEnforcement = false, static conditionalAlignment = 0, static restrictQualify = true}}, 
  nHits_ = 3476513472, cpeParams_ = 0x7fffe3d4e2e0 <cms::cuda::getEventCache()::cache>, offsetBPIX2_ = 3822379744, phiBinnerStorage_ = 0x7fffffff0090}
```
