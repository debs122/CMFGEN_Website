# Installation

## Dependencies

CMFGEN needs:

- **GNU Make** and a Fortran/C toolchain (`gfortran`, `gcc`)
- **BLAS** and **LAPACK**
- **PGPLOT**, with X11
- **csh**

### Installing on Ubuntu

If you are installing CMFGEN on Ubuntu (e.g. on a personal computer), first
install the toolchain:

```bash
sudo apt install build-essential gfortran csh
```

Then install BLAS/LAPACK via Intel MKL:

```bash
wget -O- https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS.PUB \
  | gpg --dearmor \
  | sudo tee /usr/share/keyrings/oneapi-archive-keyring.gpg >/dev/null

echo "deb [signed-by=/usr/share/keyrings/oneapi-archive-keyring.gpg] https://apt.repos.intel.com/oneapi all main" \
  | sudo tee /etc/apt/sources.list.d/oneAPI.list

sudo apt update
sudo apt install intel-oneapi-mkl-devel
```

MKL needs its environment loaded in any shell that compiles or runs CMFGEN:

```bash
source /opt/intel/oneapi/setvars.sh
```

Add that line to your `~/.bashrc` so you don't have to remember it every
time.

To install PGPLOT, simply run:

```bash
sudo apt install pgplot5
```

This pulls in X11 as a dependency, so no separate step is needed there.

### Installing on a cluster

Check for existing modules first:

```bash
module avail mkl
module avail pgplot
```

If both exist, `module load` them before compiling, and skip the `apt`
steps above. If they don't exist and you don't have root, MKL and PGPLOT
need to be built from source in your home directory — a longer process,
not covered here.

## Makefile options

!!! warning
    What follows was tested on the CMFGEN version of 18 June 2023. There's
    no guarantee it will work unmodified on other versions. But after all, if you are reading this after 2026, you will probably ask some AI to fix the makefiles for you. Maybe this document will help your AI agent/overlord.


**1. Makefile definitions.** Edit `Makefile_definitions` in the CMFGEN
source root. Remove everything that's already there — it is mostly dead
code — and replace it with:

```make
AR_OPTION = Uruv

INSTALL_DIR=/path/to/CMFGEN_source/

MOD_DIR=$(INSTALL_DIR)/mod/
LIB_DIR=$(INSTALL_DIR)/lib/
EXE_DIR=$(INSTALL_DIR)/exe/

F90 = gfortran -fopenmp -O3 -fallow-argument-mismatch
F90nomp = gfortran -O3 -fallow-argument-mismatch

FG    = -ffixed-line-length-0 -fno-backslash -fd-lines-as-comments -I$(MOD_DIR) -J$(MOD_DIR)
FH    = -ffixed-line-length-0 -fno-backslash -fd-lines-as-comments -I$(MOD_DIR) -J$(MOD_DIR)
FD    = -g  -ffixed-line-length-0 -fno-backslash -fd-lines-as-comments -I$(MOD_DIR) -J$(MOD_DIR)
FFREE = -fno-backslash -I$(MOD_DIR) -J$(MOD_DIR)
FFRED = -fno-backslash -g -I$(MOD_DIR) -J$(MOD_DIR)

BLAS=-lblas
LAPACK=-llapack
LOCLIB=

PGLIB=-lpgplot
X11LIB=-lX11
```

**2. Installation directory.** `INSTALL_DIR` must point to the directory
that contains the Makefile itself — i.e. the root of the CMFGEN source
tree.

**3. Two patches to subdirectory makefiles.**

In `subs/Makefile`, add:

```make
$(LIB)(set_kind_module.o): ../tools/set_kind_module.f
	$(F90) -c $(OPTION) $<
	ar $(AR_OPTION) $(LIB) set_kind_module.o
```

`set_kind_module.f` actually lives in `tools/`, not `subs/`, so
`subs/Makefile` has no rule to build it without this addition.

In `newsubs/Makefile`, add these two lines right after the `include` line:

```make
FC=$(F90)
FFLAGS=$(FG)
```

Without this, `newsubs` falls back to a default Fortran compiler that
won't find the `.mod` files the rest of the build has already produced.

## Compilation

From the CMFGEN source root:

```bash
make &> HOPE
```

Check the appropriately named `HOPE` file for errors. If there are none,
the executables are in `exe/`.

## Environment setup

If you use `csh`, the file `/path/to/CMFGEN_source/com/aliases_for_cmfgen.sh`
sets aliases for all the CMFGEN commands. If you're using `bash`, that file
won't work for you — instead, create a new file named
`aliases_for_cmfgen_bash.sh` inside `/path/to/CMFGEN_source/com/`:

```bash
alias astxt="$cmfdist/com/assign_txt_files.sh"

alias cmfgen="$cmfdist/exe/cmfgen.exe"
alias cmf_flux="$cmfdist/exe/cmf_flux.exe"
alias dispgen="$cmfdist/exe/dispgen.exe"
alias plt_spec="$cmfdist/exe/plt_spec.exe"
alias plt_scr="$cmfdist/exe/plt_scr.exe"
alias plt_jh="$cmfdist/exe/plt_jh.exe"

alias append_dc="$cmfdist/exe/append_dc.exe"
alias rewrite_dc="$cmfdist/exe/rewrite_dc.exe"
alias n_col_merge="$cmfdist/exe/n_col_merge.exe"
alias n_pair_merge="$cmfdist/exe/n_pair_merge.exe"

alias do_ng="$cmfdist/exe/do_ng.exe"

alias clean="$cmfdist/com/clean.sh"
alias out2in="$cmfdist/com/out2in.sh"
alias for2f="$cmfdist/com/for_to_f.sh"
alias inc2inc="$cmfdist/com/inc2inc.sh"

alias for_dif="$cmfdist/com/for_dif.sh"
alias INC_dif="$cmfdist/com/INC_dif.sh"

alias cpmod="$cmfdist/com/cpmod.sh"

alias rmrrr='rm -vf *PRRR'
alias rmin='rm -vf *_IN'
alias dfort='rm -vf fort.*'
alias dsve='rm -vf *.sve'
alias dlog='rm -vf *.log'
alias dscratch='rm -vf CSCRATCH* BCSCRATCH* DSCRATCH*'

alias rmlinks="find * -type l -maxdepth 0 -exec rm -vf {} ';'"
alias rm_all_links="find * -type l -maxdepth 10 -exec rm -vf {} ';'"
```

Add the following to `~/.bashrc` (or `~/.zshrc`), with the path set to
your CMFGEN source root:

```bash
export cmfdist=/path/to/CMFGEN_source
source /path/to/CMFGEN_source/com/aliases_for_cmfgen_bash.sh
```

Finally, reload your shell:

```bash
source ~/.bashrc
```
