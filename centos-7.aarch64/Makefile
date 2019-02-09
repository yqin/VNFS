.PHONY: all
BUILDDIR    := /var/chroots
GITVERS     := $(shell git describe --always)
TAG         ?= $(firstword $(MAKECMDGOALS))
VNFSNAME    := centos-7.aarch64
CHROOTDIR   := $(BUILDDIR)/$(VNFSNAME)-$(TAG)-$(GITVERS)
KERNEL_VER  := 4.14.0-49.13.1.el7a.aarch64


all:
	@ echo "Select a make target: generic, mofed, mofed-lustre, mofed-lustre-server"

#generic: base overlay-packages overlay-slurm overlay-warewulf cleanup
generic: base overlay-packages overlay-slurm cleanup
#mofed: base overlay-packages overlay-slurm overlay-warewulf overlay-mofed cleanup
mofed: base overlay-packages overlay-slurm overlay-mofed cleanup
#mofed-accel: base overlay-packages overlay-slurm overlay-warewulf overlay-accel overlay-mofed cleanup
#mofed-accel-lustre: base overlay-packages overlay-slurm overlay-warewulf overlay-accel overlay-mofed overlay-mofed-lustre-client cleanup
#mofed-lustre: base overlay-packages overlay-slurm overlay-warewulf overlay-mofed overlay-mofed-lustre-client cleanup
mofed-lustre: base overlay-packages overlay-slurm overlay-mofed overlay-mofed-lustre-client cleanup
#mofed-lustre-server: overlay-mofed-lustre-server cleanup

base:
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-base.def

overlay-kernel: base
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-kernel.def

overlay-packages: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-packages.def

overlay-slurm: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-slurm.def

overlay-warewulf: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-warewulf.def

overlay-accel: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-accel.def

overlay-mofed: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed.def

overlay-mofed-lustre-client: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-lustre-client.def

overlay-mofed-lustre-server: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-lustre-server.def

cleanup:
	sudo rm -rf $(CHROOTDIR)/environment $(CHROOTDIR)/singularity $(CHROOTDIR)/.coredone $(CHROOTDIR)/.exec $(CHROOTDIR)/.run $(CHROOTDIR)/.shell $(CHROOTDIR)/.singularity.d $(CHROOTDIR)/.test


vnfs:
	sudo wwvnfs -c $(CHROOTDIR) -y $(VNFSNAME)-$(TAG)

bootstrap:
	sudo wwbootstrap -c $(CHROOTDIR) $(KERNEL_VER)
