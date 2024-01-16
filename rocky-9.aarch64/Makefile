.PHONY: all
BUILDDIR    := /var/chroots
GITVERS     := $(shell git describe --always)
TAG         ?= $(firstword $(MAKECMDGOALS))
VNFSNAME    := rocky-9.aarch64
CHROOTDIR   := $(BUILDDIR)/$(VNFSNAME)-$(TAG)-$(GITVERS)
KERNEL_VER  := 5.14.0-362.13.1.el9_3.aarch64


all:
	@ echo "Select a make target: generic, mofed, mofed-accel, mofed-accel-lustre, mofed-lustre"

generic: base overlay-packages overlay-containers overlay-slurm cleanup
mofed: base overlay-packages overlay-containers overlay-slurm overlay-mofed cleanup
mofed-accel: base overlay-packages overlay-containers overlay-slurm overlay-mofed overlay-cuda cleanup
mofed-lustre: base overlay-packages overlay-containers overlay-slurm overlay-mofed overlay-lustre-client cleanup
mofed-accel-lustre: base overlay-packages overlay-containers overlay-slurm overlay-mofed overlay-cuda overlay-lustre-client cleanup

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

overlay-mofed: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed.def

overlay-cuda: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-cuda.def

overlay-lustre-client: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-lustre-client.def

cleanup:
	sudo rm -rf $(CHROOTDIR)/environment $(CHROOTDIR)/singularity $(CHROOTDIR)/.coredone $(CHROOTDIR)/.exec $(CHROOTDIR)/.run $(CHROOTDIR)/.shell $(CHROOTDIR)/.singularity.d $(CHROOTDIR)/.test


vnfs:
	sudo wwvnfs -c $(CHROOTDIR) $(VNFSNAME)-$(TAG)

bootstrap:
	sudo wwbootstrap -c $(CHROOTDIR) $(KERNEL_VER)
