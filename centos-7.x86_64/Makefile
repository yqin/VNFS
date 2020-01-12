.PHONY: all
BUILDDIR    := /var/chroots
GITVERS     := $(shell git describe --always)
TAG         ?= $(firstword $(MAKECMDGOALS))
VNFSNAME    := centos-7.x86_64
CHROOTDIR   := $(BUILDDIR)/$(VNFSNAME)-$(TAG)-$(GITVERS)
KERNEL_VER  := 3.10.0-1062.9.1.el7.x86_64


all:
	@ echo "Select a make target: generic, mofed, mofed-cuda, mofed-cuda-lustre, mofed-lustre, mofed-lustre-server, ifs, ifs-cuda, ifs-cuda-lustre, ifs-lustre, ifs-lustre-server"

generic: base overlay-packages overlay-slurm overlay-warewulf cleanup
ifs: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-ifs cleanup
ifs-cuda: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-cuda overlay-ifs cleanup
ifs-cuda-lustre: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-cuda overlay-ifs overlay-ifs-lustre-client cleanup
ifs-lustre: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-ifs overlay-ifs-lustre-client cleanup
ifs-lustre-server: base overlay-packages overlay-kernel overlay-ifs overlay-ifs-lustre-server cleanup
mofed: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-mofed cleanup
mofed-cuda: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-cuda overlay-mofed overlay-mofed-gpudirect cleanup
mofed-cuda-lustre: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-cuda overlay-mofed overlay-mofed-gpudirect overlay-mofed-lustre-client cleanup
mofed-lustre: base overlay-packages overlay-kernel overlay-containers overlay-kvm overlay-slurm overlay-warewulf overlay-mofed overlay-mofed-lustre-client cleanup
mofed-lustre-server: base overlay-packages overlay-kernel overlay-mofed overlay-mofed-lustre-server cleanup

base:
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-base.def

overlay-kernel: base
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-kernel.def

overlay-packages: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-packages.def

overlay-containers: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-containers.def

overlay-kvm: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-kvm.def

overlay-slurm: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-slurm.def

overlay-warewulf: overlay-packages
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-warewulf.def

overlay-cuda: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-cuda.def

overlay-ifs: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-ifs.def

overlay-ifs-lustre-client: overlay-ifs
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-ifs-lustre-client.def

overlay-ifs-lustre-server: overlay-ifs
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-ifs-lustre-server.def

overlay-mofed: overlay-kernel
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed.def

overlay-mofed-gpudirect: overlay-mofed overlay-cuda
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-gpudirect.def

overlay-mofed-lustre-client: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-lustre-client.def

overlay-mofed-lustre-server: overlay-mofed
	sudo singularity build -s $(CHROOTDIR) $(VNFSNAME)-mofed-lustre-server.def

cleanup:
	sudo rm -rf $(CHROOTDIR)/environment $(CHROOTDIR)/singularity $(CHROOTDIR)/.coredone $(CHROOTDIR)/.exec $(CHROOTDIR)/.run $(CHROOTDIR)/.shell $(CHROOTDIR)/.singularity.d $(CHROOTDIR)/.test


vnfs:
	sudo wwvnfs -c $(CHROOTDIR) $(VNFSNAME)-$(TAG)

bootstrap:
	sudo wwbootstrap -c $(CHROOTDIR) $(KERNEL_VER)
