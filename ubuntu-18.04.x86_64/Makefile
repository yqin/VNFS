.PHONY: all
BUILDDIR    := /var/chroots
GITVERS     := $(shell git describe --always)
TAG         ?= $(firstword $(MAKECMDGOALS))
VNFSNAME    := ubuntu-18.04.x86_64
CHROOTDIR   := $(BUILDDIR)/$(VNFSNAME)-$(TAG)-$(GITVERS)
KERNEL_VER  := 4.15.0-70-generic


all:
	@ echo "Select a make target: generic, mofed, mofed-cuda, mofed-cuda-lustre, mofed-lustre"

generic: base overlay-packages overlay-slurm overlay-warewulf cleanup
container: base overlay-packages overlay-containers overlay-slurm overlay-warewulf cleanup
mofed: base overlay-packages overlay-containers overlay-slurm overlay-warewulf overlay-mofed cleanup
mofed-cuda: base overlay-packages overlay-containers overlay-slurm overlay-warewulf overlay-cuda overlay-mofed overlay-mofed-gpudirect cleanup
mofed-cuda-lustre: base overlay-packages overlay-containers overlay-slurm overlay-warewulf overlay-cuda overlay-mofed overlay-mofed-lustre-client cleanup
# mofed-cuda-lustre: base overlay-packages overlay-containers overlay-slurm overlay-warewulf overlay-cuda overlay-mofed overlay-mofed-gpudirect overlay-mofed-lustre-client cleanup
mofed-lustre: base overlay-packages overlay-containers overlay-slurm overlay-warewulf overlay-mofed overlay-mofed-lustre-client cleanup

base:
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-base.def

overlay-kernel: base
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-kernel.def

overlay-packages: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-packages.def

overlay-containers: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-containers.def

overlay-slurm: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-slurm.def

overlay-warewulf: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-warewulf.def

overlay-cuda: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-cuda.def

overlay-mofed: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed.def

overlay-mofed-lustre-client: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-lustre-client.def

overlay-mofed-gpudirect: overlay-mofed overlay-cuda
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-gpudirect.def

cleanup:
	sudo rm -rf $(CHROOTDIR)/environment $(CHROOTDIR)/singularity $(CHROOTDIR)/.coredone $(CHROOTDIR)/.exec $(CHROOTDIR)/.run $(CHROOTDIR)/.shell $(CHROOTDIR)/.singularity.d $(CHROOTDIR)/.test


vnfs:
	sudo wwvnfs -c $(CHROOTDIR) -y $(VNFSNAME)-$(TAG)

bootstrap:
	sudo wwbootstrap -c $(CHROOTDIR) $(KERNEL_VER)
