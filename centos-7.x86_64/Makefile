.PHONY: all
BUILDDIR    := /var/chroots
GITVERS     := $(shell git describe --always)
TAG         ?= $(firstword $(MAKECMDGOALS))
VNFSNAME    := centos-7.x86_64
CHROOTDIR   := $(BUILDDIR)/$(VNFSNAME)-$(TAG)-$(GITVERS)
KERNEL_VER  := 3.10.0-957.12.1.el7.x86_64


all:
	@ echo "Select a make target: generic, mofed, mofed-accel, mofed-accel-lustre, mofed-lustre, mofed-lustre-server, ifs, ifs-accel, ifs-accel-lustre, ifs-lustre, ifs-lustre-server"

generic: base overlay-packages overlay-slurm overlay-warewulf cleanup
ifs: base overlay-packages overlay-slurm overlay-warewulf overlay-ifs cleanup
ifs-accel: base overlay-packages overlay-slurm overlay-warewulf overlay-accel overlay-ifs cleanup
ifs-accel-lustre: base overlay-packages overlay-slurm overlay-warewulf overlay-accel overlay-ifs overlay-ifs-lustre-client cleanup
ifs-lustre: base overlay-packages overlay-slurm overlay-warewulf overlay-ifs overlay-ifs-lustre-client cleanup
ifs-lustre-server: overlay-ifs-lustre-server cleanup
mofed: base overlay-packages overlay-slurm overlay-warewulf overlay-mofed cleanup
mofed-accel: base overlay-packages overlay-slurm overlay-warewulf overlay-accel overlay-mofed cleanup
mofed-accel-lustre: base overlay-packages overlay-slurm overlay-warewulf overlay-accel overlay-mofed overlay-mofed-lustre-client cleanup
mofed-lustre: base overlay-packages overlay-slurm overlay-warewulf overlay-mofed overlay-mofed-lustre-client cleanup
mofed-lustre-server: overlay-mofed-lustre-server cleanup

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

overlay-ifs: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-ifs.def

overlay-ifs-lustre-client: overlay-ifs
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-ifs-lustre-client.def

overlay-ifs-lustre-server: overlay-ifs
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-ifs-lustre-server.def

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
