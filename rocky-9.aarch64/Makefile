.PHONY: all
BUILDDIR    := /var/chroots
GITVERS     := $(shell git describe --always)
TAG         ?= $(firstword $(MAKECMDGOALS))
VNFSNAME    := rocky-9.aarch64
CHROOTDIR   := $(BUILDDIR)/$(VNFSNAME)-$(TAG)-$(GITVERS)
KERNEL_VER  := 5.14.0-284.11.1.el9_2.aarch64


all:
	@ echo "Select a make target: generic, mofed, mofed-accel, mofed-accel-lustre, mofed-lustre"

generic: base overlay-packages overlay-slurm cleanup
mofed: base overlay-packages overlay-kernel overlay-containers overlay-slurm overlay-mofed cleanup
mofed-accel: base overlay-packages overlay-kernel overlay-containers overlay-slurm overlay-cuda overlay-mofed overlay-mofed-gpudirect cleanup
mofed-accel-lustre: base overlay-packages overlay-kernel overlay-containers overlay-slurm overlay-cuda overlay-mofed overlay-mofed-gpudirect overlay-mofed-lustre-client cleanup
mofed-lustre: base overlay-packages overlay-kernel overlay-containers overlay-slurm overlay-mofed overlay-mofed-lustre-client cleanup

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

overlay-cuda: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-cuda.def

overlay-mofed: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed.def

overlay-mofed-gpudirect: overlay-mofed overlay-cuda
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-gpudirect.def

overlay-mofed-lustre-client: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-lustre-client.def

cleanup:
	sudo rm -rf $(CHROOTDIR)/environment $(CHROOTDIR)/singularity $(CHROOTDIR)/.coredone $(CHROOTDIR)/.exec $(CHROOTDIR)/.run $(CHROOTDIR)/.shell $(CHROOTDIR)/.singularity.d $(CHROOTDIR)/.test


vnfs:
	sudo wwvnfs -c $(CHROOTDIR) $(VNFSNAME)-$(TAG)

bootstrap:
	sudo wwbootstrap -c $(CHROOTDIR) $(KERNEL_VER)
