# BrainNetViewer

This is a **fork** of the [official BrainNet Viewer repository](https://github.com/mingruixia/BrainNet-Viewer).  
It includes fixes and updates for Apple Silicon (arm64, macOS) and other improvements.

## Compiling MEX for Apple Silicon (arm64, macOS) support

By default, BrainNet Viewer relies on SPM’s Intel (`.mexmaci64`) binaries, which do not run natively on Apple Silicon Macs.  
To build Apple Silicon–native binaries (`.mexmaca64`), edit the SPM Makefiles as follows:

Clone SPM12 and go to the source directory:
```bash
git clone https://github.com/spm/spm12
cd spm12/src/
```

Edit `Makefile` and `Makefile.var`:

In `Makefile`, after:
```
verb.mexmaci64:
	$(call verb, "MacOS compilation (Intel 64 bit)")
```
add: 
```
verb.mexmaca64:
	$(call verb, "macOS compilation (ARM 64 bit)")
```

In `Makefile.var`, after: 
```
ifeq (Darwin,$(PLATFORM))
  MEXEXT     = mexmaci64
  MEXBIN    ?= mex
  # https://stackoverflow.com/questions/37362414/
  OMPFLAG    = -fopenmp=libiomp5
endif
```
add:
```
ifeq (arm64,$(PLATFORM))
  MEXEXT     = mexmaca64
  MEXBIN    ?= mex
  # https://stackoverflow.com/questions/37362414/
  OMPFLAG    = -fopenmp=libiomp5
  SOSUF      = dylib
  SONAME     = install_name
endif
```

Then compile with:
```
export PLATFORM=arm64
make distclean 
make && make install
```
See [SPM’s macOS compilation instructions](https://www.fil.ion.ucl.ac.uk/spm/docs/development/compilation/macos/) for full details. 

## Installing compiled MEX files into BrainNet Viewer

After building the SPM12 MEX files (`*.mexmaca64`), copy the required binaries into BrainNet Viewer:

```bash
cp ../spm_existfile.mexmaca64 ~/BrainNet-Viewer/BrainNet_spm12_files/BrainNet_spm_existfile.mexmaca64
cp ../spm_vol.mexmaca64 ~/BrainNet-Viewer/BrainNet_spm12_files/BrainNet_spm_vol.mexmaca64
```