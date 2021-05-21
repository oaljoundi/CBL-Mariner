# Building Mariner

It is possible to build custom CBL-Mariner images from source. For this you
will need to first prepare your sustem with the tools necessary to build
mariner and then to start the compilation process. The result of the building
process is a bootable VHDX or ISO image.

## Before building CBL-Mariner

* Note that this demo tutorial is maintained on
  [GitHub](https://github.com/microsoft/CBL-MarinerDemo). **Check GitHub for
  the most up-to-date tutorial**
* Please also check out [this
  tutorial](https://github.com/microsoft/CBL-Mariner/blob/1.0/toolkit/docs/quick_start/quickstart.md)
  for the following: Cloning, Build & Boot an Image, Building an ISO Image
* Mariner downloads can be found under tab "Download Images".

Before starting this tutorial, you will need to setup your development machine.
These instructions were tested on an x86_64 based machine using Ubuntu 18.04.
Note that this demo tutorial is maintained on
[GitHub](https://github.com/microsoft/CBL-MarinerDemo). 

## Installing Build Tools

These tools are required for building both the toolkit and the images built from the toolkit. These are the same prerequisites needed for building CBL-Mariner.

    # Add a backports repo in order to install the necessary version of Go.
    sudo add-apt-repository ppa:longsleep/golang-backports
    sudo apt-get update

    # Install required dependencies.
    sudo apt -y install make tar wget curl rpm qemu-utils golang-1.15-go genisoimage python-minimal bison gawk

    # Recommended but not required: `pigz` for faster compression operations.
    sudo apt -y install pigz

    # Fix go 1.15 link 
    sudo ln -vsf /usr/lib/go-1.15/bin/go /usr/bin/go

    # Install Docker 
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo usermod -aG docker $USER

You will need to log out and lock back in for user changes to take effect.

## Building The Toolkit

To build the CBL-MarinerDemo repository you will need the same toolkit and makefile from the CBL-Mariner repository. So, first clone CBL-Mariner and build the toolkit.

    git clone https://github.com/microsoft/CBL-Mariner.git
    pushd CBL-Mariner/toolkit
    git checkout 1.0-stable
    sudo make go-tools REBUILD_TOOLS=y
    popd

## Download Demo And Copy Toolkit

Now clone the CBL-MarinerDemo repo and copy the toolkit to the CBL-MarinerDemo
repository.

    git clone https://github.com/microsoft/CBL-MarinerDemo.git
    pushd CBL-MarinerDemo
    cp ../CBL-Mariner/out/toolkit-*.tar.gz ./
    tar -xzvf tookit-*.tar.gz

The toolkit folder now contains the makefile, support scripts and the go tools
compiled from the section. The toolkit will preserve the previously compiled
tool binaries, however the toolkit is also able to rebuild them if desired.
(Not recommended: set REBUILD_TOOLS=y to use locally rebuilt tool binaries
during a build).

## Building VHD or VHDX

Choose an image to build by invoking one of the following build commands from
the CBL-Mariner/toolkit folder.

    sudo make image TOOL_BINS_DIR=../tools CONFIG_FILE=../imageconfigs/demo_vhd.json 
    sudo make image TOOL_BINS_DIR=../tools CONFIG_FILE=../imageconfigs/demo_vhdx.json

The first time make image is invoked, the toolkit downloads the necessary
toolchain packages from the CBL-Mariner repository at packages.microsoft.com.
These toolchain packages are the standard set needed to build any local
packages contained in the CBL-MarinerDemo repo. Once the toolchain is ready,
make automatically proceeds to build any local packages. In this case, the
[Hello
World](https://github.com/microsoft/CBL-MarinerDemo/blob/main/SPECS/hello_world_demo/hello_world_demo.spec)
and
[OS-Subrelease](https://github.com/microsoft/CBL-MarinerDemo/blob/main/SPECS/os-subrelease/os-subrelease.spec)
packages will be compiled. After all local packages are built, make will
assemble the packages to build an image. The resulting binaries (images and
rpms) are placed in the CBL-MarinerDemo/out folder

    VHDX:       `CBL-MarinerDemo/out/images/demo_vhdx/`
    VHD:        `CBL-MarinerDemo/out/images/demo_vhd/`
    PACKAGES:   `CBL-MarinerDemo/out/RPMS/x86_64/`

## Building an ISO

In the previous section we learned how to create a simple VHD(X) image. In this
section we will turn our attention to creating a bootable ISO image for
installing CBL-Mariner to either a physical machine or virtual hard drive.

Let's jump right in. Run the following command to build the demo ISO:

    cd CBL-MarinerDemo/toolkit
    sudo make iso TOOL_BINS_DIR=../tools CONFIG_FILE=../imageconfigs/demo_iso.json

**Copy ISO Image to Your VM Host Machine**

Copy your binary image(s) to your VM Host Machine using your preferred
technique.

**Create VHD(X) Virtual Machine with Hyper-V**

1. From Hyper-V Select Action->New->Virtual Machine.
2. Provide a name for your VM and press Next >.
3. Select Generation 1 (VHD) or Generation 2 (VHDX), then press Next >.
4. Change Memory size if desired, then press Next >.
5. Select a virtual switch, then press Next >.
6. Select Create a virtual hard disk, choose a location for your VHD(X) and set your desired disk Size. Then press Next >.
7. Select Install an operating system from a bootable image file and browse to your Demo ISO.
8. Press Finish.

**[Gen2/VHDX Only] Fix Boot Options**
1. Right click your virtual machine from Hyper-V Manager
2. Select Settings...
3. Select Security and disable Enable Secure Boot.
4. Select Firmware and adjust the boot order so DVD is first and Hard Drive is second.
5. Select Apply to apply all changes.

**Booting the ISO**
1. Right click your VM and select Connect....
2. Select Start.
3. Follow the Installer Prompts to Install your image
4. When installation completes, select restart to reboot the machine. The installation ISO will be automatically ejected.
5. When prompted sign in to your CBL-Mariner system using the user name and password provisioned through the Installer.

## Use Hyper-V to Boot Your Demo Image
Copy your demo VHD or VHDX image to your Windows Machine and boot it with
Hyper-V.

**Create VHD(X) Virtual Machine with Hyper-V**

1. From Hyper-V Select Action->New->Virtual Machine.
2. Provide a name for your VM and press Next >.
3. For VHD select _Generation 1_. For VHDX select _Generation 2_, then press Next >.
4. Change Memory size if desired, then press Next >.
5. Select a virtual switch, then press Next >.
6. Select Use an existing virtual hard disk, then browse and select your VHD(X) file.
7. Press Finish.

**[Gen2/VHDX Only] Fix Boot Options**

1. Right click your virtual machine from Hyper-V Manager
2. Select Settings....
3. Select Security and **disable** Enable Secure Boot.

**Boot and Sign-In to Your VHD(X) Image**

[Mariner VHD/VHDX User Provisioning](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/175/Mariner-VHD-and-VHDX-User-Provisioning)

1. Right click your VM and select Connect....
2. Select Start.
3. Wait for CBL-Mariner to boot to the login prompt, then sign in with:

	root
	p@ssw0rd

**Verify your Derivate Packages are Installed** From the command line run the helloworld program

	root@demo [~]# helloworld
	Hello World Sample!

Now show the contents of the os-subrelease file

	root@demo [~]# cat /etc/os-subrelease
	BUILDER_NAME=My Builder Name
	BUILD_DATE="YYYY-MM-DDTHH:MM:SSZ"
	ID=my-product-id
	VERSION_ID=my-version-id
	NAME="My Product Name"
	VERSION="my-version-id"

Congratulations you've built and launched your first CBL-Mariner derivative image!

## Changing the Demo Image Kernel

In some situations you may want to build and test variations of the default
CBL-Mariner Kernel. Because the kernel is also a package, the process is
similar to adding a new package as discussed in the previous section.

To begin, copy the complete contents of the CBL-Mariner kernel spec folder into
your clone of the CBL-MarinerDemo repo. The following assumes you have already
cloned CBL-Mariner and the CBL-MarinerDemo demo repo and both are nested under
a git folder:

    user@machine:~/git$ cp -r CBL-Mariner/SPECS/kernel/ CBL-MarinerDemo/SPECS/kernel/ 

Next, we will need to download a source tarball from github that matches the
kernel version in the kernel.spec file.

    # Switch to the kernel folder
    $ cd CBL-MarinerDemo/SPECS/kernel/ 

    # Determine the kernel version you are using (yours may vary)
    $ grep Version: kernel.spec
    Version:        5.4.91

    # Download the associated tar.gz file.  Be sure to substitute your version number in the URL here
    $ wget  https://github.com/microsoft/WSL2-Linux-Kernel/archive/linux-msft-5.4.91.tar.gz

Now make your modifications to the one or both of the config files. For AMD64 modify the config file. For AARCH64, modify the config_aarch64 file.

By default the CONFIG_MAGIC_SYSRQ setting is disabled. For this tutorial we will enable it. Using your favorite editor open the config file. Find the CONFIG_MAGIC_SYSRQ setting, then make the adjustments as shown here:

    # Before
    # CONFIG_MAGIC_SYSRQ is not set

    # After
    CONFIG_MAGIC_SYSRQ=y
    CONFIG_MAGIC_SYSRQ_DEFAULT_ENABLE=0x1
    CONFIG_MAGIC_SYSRQ_SERIAL=y

Note that the kernel spec file, from the CBL-Mariner repo, requires implicitly
enabled settings to be explicitly set. In this case enabling CONFIG_MAGIC_SYSRQ
is insufficient because CONFIG_MAGIC_SYSRQ_DEFAULT_ENABLE and
CONFIG_MAGIC_SYSRQ_SERIAL are implicitly enabled. If they were missing,
compilation of the kernel would fail. In general, when an error of this nature
occurs, the build log file for the kernel will indicate what needs to be
changed. For example, if we **only** set CONFIG_MAGIC_SYSRQ=y, the build would
eventually fail with the build output shown here:

    time="2021-02-05T11:16:15-08:00" level=debug msg="Magic SysRq key (MAGIC_SYSRQ) [Y/n/?] y"
    time="2021-02-05T11:16:15-08:00" level=debug
    time="2021-02-05T11:16:15-08:00" level=debug msg="Error in reading or end of file."
    time="2021-02-05T11:16:15-08:00" level=debug msg="  Enable magic SysRq key functions by default (MAGIC_SYSRQ_DEFAULT_ENABLE) [0x1] (NEW) "
    time="2021-02-05T11:16:15-08:00" level=debug
    time="2021-02-05T11:16:15-08:00" level=debug msg="Error in reading or end of file."
    time="2021-02-05T11:16:15-08:00" level=debug msg="  Enable magic SysRq key over serial (MAGIC_SYSRQ_SERIAL) [Y/n/?] (NEW) "
    .
    .
    .
    time="2021-02-05T11:16:15-08:00" level=debug msg="+ cat config_diff"
    time="2021-02-05T11:16:15-08:00" level=debug msg="--- new_config\t2021-02-05 19:16:15.316175432 +0000"
    time="2021-02-05T11:16:15-08:00" level=debug msg="+++ current_config\t2021-02-05 19:16:09.440117553 +0000"
    time="2021-02-05T11:16:15-08:00" level=debug msg="@@ -6484,8 +6484,6 @@"
    time="2021-02-05T11:16:15-08:00" level=debug msg=" # end of Compile-time checks and compiler options"
    time="2021-02-05T11:16:15-08:00" level=debug msg=" "
    time="2021-02-05T11:16:15-08:00" level=debug msg=" CONFIG_MAGIC_SYSRQ=y"
    time="2021-02-05T11:16:15-08:00" level=debug msg="-CONFIG_MAGIC_SYSRQ_DEFAULT_ENABLE=0x1"
    time="2021-02-05T11:16:15-08:00" level=debug msg="-CONFIG_MAGIC_SYSRQ_SERIAL=y"
    time="2021-02-05T11:16:15-08:00" level=debug msg=" CONFIG_DEBUG_KERNEL=y"
    time="2021-02-05T11:16:15-08:00" level=debug msg=" CONFIG_DEBUG_MISC=y"
    time="2021-02-05T11:16:15-08:00" level=debug msg=" "

After editing your config file, save it and compute a new sha256sum.

    $ sha256sum config
    f6c3c5eb536f7c7778c3aaa45984de9bf6c58d2a7e5dfd74ace203faabf090a6  config

Now, using your favorite editor update the config file hash(es) in the kernel.signatures.json.

One last step before building. When there is a conflict, the build system will make a best-effort attempt at prioritizing the local version of a package over the version on packages.microsoft.com. However, to ensure we can differentiate our new custom kernel from the default kernel, and to guarantee the local version will be consumed, bump the release number in the kernel release spec. In this case use your favorite editor and change the release number to 100 as shown below and save the file.

    Summary:        Linux Kernel
    Name:           kernel
    Version:        5.4.91
    Release:        100%{?dist}               <------------------ set this value to 100 (for example)
    License:        GPLv2
    Vendor:         Microsoft Corporation
    Distribution:   Mariner

After saving your file, rebuild your demo image. The kernel will take some time to build.

    cd CBL-MarinerDemo/toolkit
    sudo make clean
    sudo make image CONFIG_FILE=../imageconfigs/demo_vhd.json

After the build completes, boot your image and log in. Next, verify that you have your modified kernel and that you can trigger a sysrq function.

    # Verify your kernel's version and release number (this may vary)
    root@demo [~]# uname -r
    5.4.91-100.cm1

    # Verify that sysrq functionality is enabled in the kernel.  
    # There are several ways to do this, but we'll directly write the
    # reboot command to /proc/sysrq-trigger 
    root@demo [~]# echo b > /proc/sysrq-trigger

# Building Mariner Packages

Mariner comes with a wide range of pre-built packages. Using the standardized
RPM package system, it is also possible to add your own custom packages with
ease. 

## Available Packages

Available packages can be found
[here (x86_64)](https://packages.microsoft.com/cbl-mariner/1.0/prod/base/x86_64/rpms/)
and
[here (x86_64)](https://packages.microsoft.com/cbl-mariner/1.0/prod/update/x86_64/rpms/).

Similar packages on different distributions may have different names. If you
are looking for a particular package but can't seem to find it in this list,
please post your question in the Mariner OS support [teams
channel.](https://teams.microsoft.com/dl/launcher/launcher.html?url=%2F_%23%2Fl%2Fteam%2F19%3Aa3a8a3987aa74b478870a2f246811e2d%40thread.tacv2%2Fconversations%3FgroupId%3D5ef827e3-78d9-4463-b0fe-23c2727b4342%26tenantId%3D72f988bf-86f1-41af-91ab-2d7cd011db47&type=team&deeplinkId=1fb76e65-0acc-4c51-a96f-4e99f8c4739b&directDl=true&msLaunch=true&enableMobilePage=true&suppressPrompt=true) 

## Installing kexec 

To install the kexec command package, [click
here.](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/393/Using-Kexec-on-Mariner) 

You can pip install **az-cli**. The following commands are tested and work in
the Mariner docker container and VM:

    tdnf install -y python3-setuptools python3-pip
    pip3 install –-upgrade pip
    pip3 install az.cli

This allows you to run 'az login' and other commands as usual.
 
## Requesting Packages

You may add and build packages locally by adding them to your derivative
repository.  However, you may also make package requests with this [
form.](https://microsoft.visualstudio.com/OS/_workitems/create/Task?templateId=a923b03c-2520-4b4b-916b-2f99a1b30072&ownerId=104f5eef-e50a-4baa-9474-7fe2ec5bcd69)

# Adding Packages to your Image

Please go to "Packages" under "Quickstart" to learn how to add packages to your
Mariner Image. This section contains:

1. Package Lists
2. Adding Pre-built Packages to an Image
3. Adding New Packages to an Image

## Defining Package Lists

In the previous sections, we learned how to build a specific image or iso by
passing a CONFIG_FILE argument to make. Each CONFIG_FILE specifies how the
image should be built and what contents should be added to it. In this section
we will focus on how the image content is defined.

The complete package set of an image is defined in the "PackageLists" array of
each image's configuration file. For example, the demo_vhd.json file includes
these package lists:

	"PackageLists": [
		"demo_package_lists/core-packages.json",
		"demo_package_lists/demo-packages.json"
	],

Each package list defines the set of packages to include in the final image. In
this example, there are two, so the resuling demo VHD contains the union of the
two package lists. While it is possible to combine both package lists into a
single JSON file, the separation adds clarity by grouping related content. In
this case, packages originating from packages.microsoft.com are in the
core-packages set, and packages built from the local repository are specified
in the demo-packages set.

The first package list, core-packages.json, includes a superset-package called
[core-packages-base-image](https://github.com/microsoft/CBL-Mariner/blob/1.0/SPECS/core-packages/core-packages.spec).
Core-packages-base-image is common to most derivatives as it contains the
common set of packages used in Mariner Core. This bundling is a convenience. It
is possible to list each package individually instead. The second package,
initramfs, is used for booting CBL-Mariner in either a virtualized or physical
hardware environment. Not every image needs it, so it's not included in the
**core-packages-base-image** superset. Instead, it's specified separately.

	{
		"packages": [
			 "core-packages-base-image",
			 "initramfs"
		],
	}

The second package list, demo-packages.json, contains the Hello World and
os-subrelease packages that are unique to the CBL-MarinerDemo repository:

	{
		"packages": [
			"hello_world_demo",
			"os-subrelease"
		]
	}

## Adding Pre-built Packages to an Image

Mariner also supports adding pre-built packages to the image. This is done
through the `core-packages.json` file. 

The Zip package is not included in the demo image by default. Because Zip is
already released for CBL-Mariner lets add it to your demo image. Open the
[core-packages.json](https://github.com/microsoft/CBL-MarinerDemo/blob/main/imageconfigs/demo_package_lists/core-packages.json)
file with your favorite editor, Add zip to the packages array before initramfs.
While it's possible to add zip after initramfs, it is currently recommended to
insert new packages before initramfs due to a performance quirk in the build
system.

	{
		"packages": [
			"core-packages-base-image",
			"zip",                        <----- add zip here
			"initramfs"
		],
	}

Save the file. For this tutorial we will continue building the VHD image, but
you may rebuild the image of your choice because the ISO, VHD and VHDX all
share the same core package list file.

    cd CBL-MarinerDemo/toolkit
    sudo make image TOOL_BINS_DIR=../tools CONFIG_FILE=../imageconfigs/demo_vhd.json

Boot the image and verify that the latest version of zip is now provided:

    root@demo [~]# zip
    Copyright (c) 1990-2008 Info-ZIP - Type 'zip -"L"' for software license.
    Zip 3.0 (July 5th 2008). Usage:
    ...
    root@demo [~]# dnf info -y zip
    Installed Packages
    Name        : zip
    Version     : 3.0   <---\
	Release     : 5.cm1 <---|--- Your Version+Release will be greater than or equal to this version
    
By default the _latest_ version of any package specified in a package list will
be included in your image. It is important to note that each time you rebuild
your image it may differ from your previous build as the packages on
packages.microsoft.com are periodically updated to resolve security
vulernabilities. This behavior may or may not be desired, but you can always be
assured that the most recent build is also the most up to date with respect to
CVE's.

If you want to guarantee that your next build will be reproduced the same way
at a later time, CBL-Mariner provides some support for this. Each time an image
is built, a summary file is generated that lists the explicit packages included
in the build. The default location of this file is at:
CBL-MarinerDemo/build/pkg_artifacts/graph_external_deps.json. To capture your
build's explicit contents and reproduce the build later, it's important to save
this file for later use. See [Reproducing a Build
](https://github.com/microsoft/CBL-Mariner/blob/1.0/toolkit/docs/building/building.md#reproducing-a-build)
in the CBL-Mariner git repository for advanced details.

The next section also describes a technique for pinning specific package versions.

## Adding Specific Pre-Built Package Version

Occassionally you may need to install a very specific version of a package in
your image at build time, rather than the latest version. CBL-Mariner supports
this capability.

This time lets add Unzip version 6.0-16 to our demo image. To do this, you must specify the full name and architecture of your preferred package.

	{
		"packages": [
			"core-packages-base-image",
			"zip",
			"unzip-6.0-16.cm1.x86_64",   <---- add specific unzip version
			"initramfs"
		],
	}

Save the file and rebuild your image.

    cd CBL-MarinerDemo/toolkit
    sudo make image TOOL_BINS_DIR=../tools CONFIG_FILE=../imageconfigs/demo_vhd.json

Boot the image and verify that unzip in now provided, _and_ it is the -16
version.

    root@demo [~]# dnf info -y unzip
    Installed Packages
    Name        : unzip
    Version     : 6.0
    Release     : 16.cm1
    ...
    Available Packages
    Name        : unzip
    Version     : 6.0
    Release     : 18.cm1  <--- this version may vary
    ...

## Adding New Packages to an Image  

Packages are defined by RPM SPEC files. At its core, a SPEC file contains the
instructions for building and installing a package. Most SPEC files contain a
pointer to one or more compressed source files, pointers to patch files, and
the name, version and licensing information associated with the package. SPEC
files also contain references to build and runtime dependencies. The goal of
this tutorial is to show the process for adding a spec file to the demo repo,
not to delve into the details of creating a spec file. For detailed information
on SPEC file syntax and features refer to the [RPM Packaging
Guide](https://rpm-packaging-guide.github.io/) or search the web as needed.

To add a new package to the CBL-MarinerDemo repo you must take the following
actions:

* Acquire the compressed source file (the tarball) you want to build
* Create a signature meta-data file (a SHA-256 hash of the tarball)
* Create a .spec file.

For this tutorial we will add the "gnuchess" package to your CBL-MarinerDemo
image.

First, download the source code for gnuchess 6.2.7 here. And save it in a new
CBL-MarinerDemo/SPECS/gnuchess folder. Also, download and save the [game data
file](http://ftp.gnu.org/pub/gnu/chess/book_1.01.pgn.gz) to the gnuchess
folder.

Next, create the spec file for gnuchess. This may be created from scratch, but
in many cases it's easiest to leverage an open source version as a template.
Since the focus of this tutorial is to demonstrate how to quickly add a new
package, we will obtain an existing spec file [Fedora source rpm for
gnuchess](https://src.fedoraproject.org/rpms/gnuchess/blob/master/f/gnuchess.spec).

Clone the Fedora gnuchess repo and copy the spec and patch files into your
gnuchess folder:

	cd CBL-MarinerDemo/SPECS/gnuchess
	git clone https://src.fedoraproject.org/rpms/gnuchess.git /tmp/gnuchess
	cp /tmp/gnuchess/gnuchess.spec .

Now calculate the SHA-256 hashed for gnuchess-6.2.7.tar.gz and the
book_1.01.pgn.gz file The SHA-256 sum is used by the build system as an
integrity check to ensure that the tarballs associated with a SPEC file are the
expected one.

Calculate the new checksum:

	$ cd CBL-MarinerDemo/SPECS/gnuchess
	$ sha256sum gnuchess-6.2.7.tar.gz
	e536675a61abe82e61b919f6b786755441d9fcd4c21e1c82fb9e5340dd229846  gnuchess-6.2.7.tar.gz
	$ sha256sum book_1.01.pgn.gz
	35df43a342c73e6624e8dbfed78d588c2085208168c3cd3300295e3c57981be0  book_1.01.pgn.gz

Using your favorite editor create and save a gnuchess.signatures.json file with
the following content.

	{
	 "Signatures": {
	  "gnuchess-6.2.7.tar.gz": "e536675a61abe82e61b919f6b786755441d9fcd4c21e1c82fb9e5340dd229846",
	  "book_1.01.pgn.gz": "35df43a342c73e6624e8dbfed78d588c2085208168c3cd3300295e3c57981be0"
	 }
	}

At this point your CBL-MarinerDemo/SPECS/gnuchess folder should alook similar to this:

	~/CBL-MarinerDemo/SPECS/gnuchess$ ls -la
	total 816
	drwxr-xr-x 2 jon jon   4096 Jan 22 14:23 .
	drwxr-xr-x 5 jon jon   4096 Jan 22 13:43 ..
	-rw-r--r-- 1 jon jon    338 Jan 22 14:23 gnuchess-5.06-bookpath.patch
	-rw-r--r-- 1 jon jon 802863 Jan 22 13:44 gnuchess-6.2.7.tar.gz
	-rw-r--r-- 1 jon jon    117 Jan 22 13:51 gnuchess.signatures.json
	-rw-r--r-- 1 jon jon   9965 Jan 22 14:23 gnuchess.spec

At this point we need to modify the gnuchess.spec file slightly to build
properly for CBL-Mariner by:

* Bumping the release number
* Selecting the non-precompiled book
* Patching the BuildRequires for c++ to use the CBL-Mariner package name
* Updating the changelog and professionally show grattitude to Fedora.

Your spec file should appear similar to this:

	Summary: The GNU chess program
	Name: gnuchess
	Version: 6.2.7
	Release: 4%{?dist}     <------------------------------------------ increment this value
	License: GPLv3+
	URL: ftp://ftp.gnu.org/pub/gnu/chess/
	Source: ftp://ftp.gnu.org/pub/gnu/chess/%{name}-%{version}.tar.gz
	Source1: http://ftp.gnu.org/pub/gnu/chess/book_1.01.pgn.gz <------ uncomment this line

	# use precompiled book.dat:
	#Source1: book_1.02.dat.gz  <------------------------------------- comment out this line
	#Patch0: gnuchess-5.06-bookpath.patch
	Provides: chessprogram
	BuildRequires:  gcc  <-------------------------------------------- set this to gcc (or remove)
	BuildRequires: flex, gcc
	BuildRequires: make

Also, modify the changelog by adding a new entry similar to the one below.

	%changelog

	*Thu Jan 21 2021 Your Name Here <your_email_here> - 6.2.7-4      
	- First version of gnuchess for my image. Spec file imported from Fedora.

	* Sat Aug 01 2020 Fedora Release Engineering <releng@fedoraproject.org> - 6.2.7-3
	- Second attempt - Rebuilt for
	  https://fedoraproject.org/wiki/Fedora_33_Mass_Rebuild

At this point, we can use a shortcut to verify that the gnu chess package
compiles by issuing the following command. It will build any packages not
already built, but not build the image itself.

    $ cd CBL-MarinerDemo/toolkit
    $ sudo make build-packages TOOL_BINS_DIR=../tools CONFIG_FILE=

If the build fails, inspect the build output for clues and repair any issues.
The default location for build logs is in the
_CBL-MarinerDemo/build/logs/pkggen/rpmbuilding/ folder_. There should be one
log for each package.

Finally, we need to add gnuchess to the demo-packages.json file.

	{
	   "packages": [
		 "gnuchess",
		 "hello_world_demo",
		 "os-subrelease"
	   ]
	}

Boot your image, log in and verify that gnuchess is now available:

    root@demo [~]# gnuchess
    GNU Chess 6.2.7
    Copyright (C) 2020 Free Software Foundation, Inc.
    License GPLv3+ GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.
    White (1) :

# Building Your Own Product

While Mariner itself is a Linux based operating system that can be used as is,
Mariner is primarily intended to serve as a baseline OS that can be tailored
for use in your own product. When creating a Mariner based product, your
choices range from using Mariner as is, to extending Mariner with your own
packages and components, to even replacing Mariner components with your
preferred versions. In the latter two cases you are essentially creating
"Derivative" Mariner Images from the "Base" Mariner image. There is a strong
separation between the Base Mariner Repository where we maintain a highly
curated set of packages and tools and the highly customizable world of your
Derivative Repository. 

[Create a Product Repository ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/46/Create-a-Product-Repository)

[Create a Product Configuration ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/48/Create-a-Product-Configuration)

[Add a package (SPEC File) to your Product Repository ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/49/Add-a-new-package-to-your-derivative)

[Configure a CI pipeline for packages repository updates ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/247/Configuring-a-CI-pipeline-for-packages-repository-updates)

[Add an unattended install file to your ISO (Kickstart Files) ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/95/Configure-Installation-from-.ISO)

## Platform Specific Installs 

For platform specific installs, please use the links below.  

**Brainbox**

To install Mariner on brainbox follow this guide:
[Installing the Brainbox ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/102/Installing-the-Brainbox)
 
**Raspberry PI 3**

To install Mariner on Raspberry Pi 3 follow this guide:
[Raspberry PI 3 ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/93/Raspberry-PI-3)

**PE100**

To install Mariner on PE100 follow this guide:
[Installing PE100](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/87/Installing-PE100) 

**i.MX8MQ EVK image**

To install Mariner on iMX8MQ follow this guide:
[Installing i.MX8MQ EVK image ](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/62/Installing-i.MX8MQ-EVK-image)
 
# Onboarding to Sovereign and Airgapped Clouds

The below applies to the following clouds: Sovereign (Mooncake, Fairxfax/USGov,
Blackforest) and Airgapped (USSec, USNat) 

## Support 

_Applies to Standard VHD Image and Container Image. Does not apply to AKS
Container host, which is supported by AKS team._

Much like SBI (Ubuntu hardened OS owned by Linux Systems Group), the Mariner
engineering team does not have cleared engineers that can respond to OS related
IcMs in airgapped clouds.  We will follow a similar model where the Azure
Service using Mariner egresses information out of airgapped environment, and
then works with the Mariner engineering team to help troubleshoot. It’s up to
the team inside the airgapped environment to determine how issues will be
communicated in and out of this environment. The Mariner team can only
correspond with Azure Service team members outside of the airgapped
environment. 

In the future, we are looking into staffing cleared engineers that can respond
to OS related IcMs and CVEs within the airgapped environment. 

## Compliance

It’s the responsibility of the **service** running Mariner to complete the
necessary sovereign/airgapped onboarding steps, as well as to remain compliant
in these clouds. Each service will have different requirements. Some resources
that can help you get started: 

* [Sovereign Cloud
  Onboarding](https://msazure.visualstudio.com/One/_wiki/wikis/One.wiki/8650/Sovereign-Cloud-Onboarding) 
* [Airgapped in USSec, USNat,
  USGov](https://microsoft.sharepoint.com/teams/JEDI-Shiproom)


## Core Build  

The Mariner Core Build is available in the following formats and is available
for selfhost on a VM or WSL: 

* ISO 
* VHD 
* VHDX 
* SDK 

## Github

To access Mariner on GitHub, [click here](https://github.com/microsoft/CBL-Mariner).  

* [Cloning, Build & Boot an Image, Building an ISO Image](https://github.com/microsoft/CBL-Mariner/blob/1.0/toolkit/docs/quick_start/quickstart.md) 
* [Mariner demo tutorial](https://github.com/microsoft/CBL-MarinerDemo).

## Containers 

For container releases, [click
here](https://eng.ms/docs/products/mariner-linux/gettingstarted/containersandaks/marinercontainerimage)
to see the most recent Mariner container releases on ACR 

## Self-hosting 

To self-host and contribute, [click
here](https://microsoft.sharepoint-df.com/teams/Mariner/SitePages/Self-Host%20and%20Contribute.aspx).  
 
# Package Management Overview

The Mariner OS uses the "Tiny Dandified" (TDNF) package manager. TDNF is a C
based successor of the DNF package manager, which itself is the successor to
Fedora’s YUM package manager. TDNF is included in the base Mariner image by
default. 

When installing a package on your system, TDNF connects to one or more RPM
repositories in the cloud. IF a package is unavailable in one repository, a
subsequent repository is checked. Repositories, and the order in which those
repositories are scanned, are specified in configuration files that reside in
your Mariner image. The TDNF configuration file /etc/tdnf/tdnf.conf contains
configuration information about how TDNF should handle caching and other local
functions. It also contains a pointer to the repo configuration directory.
Owing to it's YUM heritage, the default is to point to /etc/yum.repos.d/ which
contains the list of repo configuration files. 

## RPM Repositories 

By default, all Mariner images are partially configured  to connect with the
curated Mariner Repository. These repositories are
**mariner-official-base.repo** and **mariner-official-update.repo**. The _Base
Repository_ always maintains a static list of RPM's built at the time of
release. The _Update Repository_ always maintains a forward rolling list of
Security Patched RPM's updated over time. We will not introduce new RPM's or
functionality here. 

This repository holds all RPM's that are built from the Mariner Repository.
There are currently over 1700 packages available. Please note that this list of
packages is actually a "super-set" of the packages installed on the Minimal
Mariner Image. That is, we build and manage more packages than we install in
the default image. 

## Access to TDNF Server 

The TDNF RPM Server uses HTTPS to support file transfer. A self-signed root
cert is included in each Mariner Image so that the client can trust the server.
From the standpoint of TDNF, this is completely transparent and there is no
action for you. 

However, given the current confidentiality of our technology, the server must
also authenticate the client when the client is NOT running on corpnet. The
goal of this is to strike a balance between granting access to the TDNF server
outside of corpnet and to prevent non-authorized entities from gaining access
to our Mariner technology until we can release publicly. To access the TDNF
repository from a running copy of your Mariner build you will need a signed
user cert added to your image. Due to the management overhead of issuing unique
user certs for each employee utilizing Mariner, we are instead, issuing certs
for each repo or "Mariner Derivative". 

Please note that this certificate is also needed as part of your build process
because the derivative build process utilizes TDNF to pull the RPM's or source
needed to build your image. 

* [Obtain your Team
Cert](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/43/Obtain-a-Team-Cert) 
* [Provisioning Certs to Your Mariner Image
](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/39/Provisioning-Certs-to-Mariner-VM-or-Image)
* [Using TDNF
](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/97/Using-TDNF)

# RPM Publishing 

Mariner RPMs are published to
[packages.microsoft.com](https://packages.microsoft.com)

These packages are used in the build process during the derivative image
builds.  These packages are also consumed by the device run-time when
attempting to install additional packages/updating the existing packages (via
tdnf or dnf). 

## CDPX vs ADO 

Currently the packages are created by the build pipeline. Mariner and a few
derivative products have moved to CDPX to build the packages for AMD64
Architecture. For AARCH64, the packages are still built in the ADO Pipeline. 

## Signed RPM vs Unsigned RPMs 

We have enabled signing the RPMs inside CDPX and ADO Pipeline. To utilize this,
every team should have their own PGP Keys on boarded on the ESRP back-end
working with ESRP team. At this moment, Mariner and MSK8 teams have their PGP
Keys available on the ESRP Server and can create signed packages. 

## Automated Publishing Pipelines 

We have publishing pipelines that would automatically publish the RPM packages
to packages.microsoft.com for the following Products: 

* Mariner 
* Mariner Diagnostics 
* Pilot Fish Products

# Mariner Container Images

Mariner container images are available on ACR and can be used locally or on
AKS. A new container image is published monthly. The 1.0 tag moves forward to
the most recently released version in the registry, and is therefore always the
latest published version of a container.

>[!IMPORTANT] Qualys container scanning is being enabled on Mariner containers,
>but is not yet available. Please check in with MarinerQA@microsoft.com before
>running production workloads on Mariner containers.

## Testing Locally via Docker

Tag| Version | URL
---------|----------|---------
 1.0 | 1.0.20210127  | [cblmariner.azurecr.io/base/core:1.0](https://cblmariner.azurecr.io/base/core:1.0)
 1.0.20210127 | 1.0.20210127  | [cblmariner.azurecr.io/base/core:1.0.20210127](cblmariner.azurecr.io/base/core:1.0.20210127)

To run a docker instance with mariner, run: 

    docker run -it cblmariner.azurecr.io/base/core:1.0 /bin/bash

To install aspnetcore and dotnetcore on your container, run: 

    RUN tdnf install -y aspnetcore-runtime-3.1

## k8 Containers
k8 containers are optimized for AKS workloads, and are currently under development. 

## Distroless Containers
Mariner has distroless container images for major language stacks. 
The 3 primary distroless containers are available in ACR and are labelled:  “minimal”, “base” and “base-debug” for AMD64 and ARM64.  If you are familiar with the google distroless containers these are equivalent to their “static”, “base” and “base-debug” containers, respectively.   
 
* cblmariner.azurecr.io/distroless/minimal:1.0
* cblmariner.azurecr.io/distroless/base:1.0
* cblmariner.azurecr.io/distroless/base-debug:1.0
 
**Minimal** is the smallest distroless container. It includes the following Mariner packages and is primarily intended for use with golang or other statically linked applications:
* Filesystem (folder layout)
* mariner-release (CBL-Mariner version info)
* ca-certificates-static
 
The **Base** container adds the following Mariner packages to the **Minimal** container and is primarily intended for C based applications.
* iana-etc (networking data)
* tzdata
* glibc
* openssl-libs
* openssl
 
**Base-debug** adds the following packages and provides a lightweight debugging environment. 
* Busybox
* uclibc
 
## Tagging Policy 

The Mariner team will tag every base container image upload with two tags: 

* base/core:MajorVersion 
* base/core:FullVersion 

The format of the tags is the following: 

* Major Version: X.X, where X.X is the major version number of the release (Example: 1.0). 
* Full Version: MajorVersion.YYYYMMDD, where YYYYMMDD is the date the image was built (Example: 1.0.20210127). 

This will result in a base/core:MajorVersion tag that always represents the latest base container image release for a given major release, and a growing list of base/core:FullVersion tags that preserve a tag for each base container image that was pushed. 
Once a new major version of Mariner is released, base container image updates for the prior release will continue to be published until the prior release is end of life. 

# Mariner as the AKS Container Host
Mariner as an officially supported AKS container host will GA for internal use in July. We have entered private preview, and are having some early adopters test it out now. Please join the Mariner-announcements DL for updates!  

Check out the demo [here!](https://microsoft.sharepoint.com/:v:/t/Mariner/ERUxl9KnsqNAqTEJSVOO1ZYB0qcOPG-21k5L5NSSoyJxvw?e=yV0jw6)


## Private Preview
If your Azure subscription is enrolled in the private preview you can use the following steps to deploy a Mariner based AKS cluster.
## Prerequisites
1) Install the [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli)
2) Install kubectl through az-cli (`az aks install-cli`) or follow the [upstream instructions](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/)


>[!IMPORTANT]
>* Mariner as an AKS container host is currently only available in Azure canary regions. It will be available in other regions in the coming weeks.
>* Kubernetes 1.19 is the minimum supported version, you may not specify a K8s version lower than 1.19.
>* Currently FIPS and GPU feature flags are not supported on CBLMariner but are in-plan to be enabled in the future.
>* Currently Mariner agent pools are not allowed to run on NC series VM sizes because GPU acceleration is not yet enabled.

## Deploying a cluster with a Mariner agent pool
The parameter that enables deployment of Mariner is the new [osSKU argument](https://docs.microsoft.com/en-us/azure/templates/microsoft.containerservice/managedclusters?tabs=json#managedclusteragentpoolprofile-object) in agentPoolProfiles. Setting osSKU=CBLMariner will tell AKS to provision your container host with a Mariner image. Currently this parameter is not yet exposed through the az-cli commands but it is available for ARM template deployments.

To add CBLMariner to an existing ARM template you need to add `"osSKU": "CBLMariner"` and `"mode": "System"` to `agentPoolProfiles` and set the apiVersion to 2021-03-01 or newer (`"apiVersion": "2021-03-01"`)

The below example deployment uses the ARM template marineraksarm.yml. Create this file on your system and fill it with the contents of [MarinerAKSPreviewYAML](./MarinerAKSPreviewYAML.md)

### Deploying AKS Mariner cluster with an ARM template
```
az group create --name cblmarinertestrg --location centraluseuap

az deployment group create --resource-group cblmarinertestrg --template-file marineraksarm.yml --parameters clusterName=testcblmarinercluster dnsPrefix=cblmarineraks1 linuxAdminUsername=azureuser sshRSAPublicKey='<contents of your id_rsa.pub>'

az aks get-credentials --resource-group cblmarinertestrg --name testcblmarinercluster

kubectl get pods --all-namespaces
```

## Container Insights
Container Insights](https://docs.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-analyze) allows you to monitor your AKS clusters. Click [here](https://docs.microsoft.com/en-us/azure/azure-monitor/containers/container-insights-enable-existing-clusters) to get started. 

### Networking issues on CBLMariner clusters with AzureCNI networking

This has been fixed in an upcoming image release.  In the meanwhile if you want
to use an AzureCNI based Mariner cluster please run the following after
deploying the cluster.

Identify the name and resource group for the vmss that contains your Mariner
container host VMs and fill them into command below. The resource group will be
of the form “MC_resourcegroup_nodepoolname_region”.

	az vmss list-instances --name aks-nodepool1-34612685-vmss --resource-group MC_hebebermaks_azurenet2_eastus --query "[].id" --output tsv | \
	az vmss run-command invoke --command-id RunShellScript --scripts \
	"echo -e 'net.ipv4.ip_forward = 1\nnet.ipv4.conf.all.forwarding = 1\nnet.bridge.bridge-nf-call-iptables = 1' | \
	sudo tee /etc/sysctl.d/99-mariner-containerd.conf;
	sudo sysctl -p /etc/sysctl.d/99-mariner-containerd.conf" --ids @-

Shortly after the vmss command completes you should see that coredns has
started successfully: `kubectl get pods --all-namespaces -o wide`

You may need to restart the pods in the kube-system namespace: `kubectl -n
kube-system rollout restart deploy`

## Preview Support

If you run into any issues with the Mariner AKS container host preview please
send an email to [Tony Xu and Henry Beberman](mailto:tonyxu@microsoft.com;hebeberm@microsoft.com?subject=AKSCBLMariner%20Preview%20Support%20Request).

## Retention Policy 

All images tagged with base/core:FullVersion will be hosted until at least the
major version end of life. Advance notice will be sent out via email prior to
removal. 

## Additional Documentation 

[Mariner Container Servicing](https://microsoft.sharepoint.com/:w:/t/Mariner/EXhVQgbem6VEgYPfUNnB27sB4GdPCtON97RsrvpnncUbhQ?e=NhXcYW)  

# New Updates

Each month we will be posting new updates on Mariner! Please check back monthly
or
[subscribe](https://idwebelements/GroupManagement.aspx?Group=marinerannouncements&Operation=join)
to this newsletter . Archived newsletters can be found
[here](https://microsoft.sharepoint.com/teams/Mariner/Shared%20Documents/Forms/AllItems.aspx?RootFolder=%2Fteams%2FMariner%2FShared%20Documents%2FGeneral%2FStatus%20updates%2FNewsletter&FolderCTID=0x012000793E844D28D7534CB6D9A2948283C9FF).  

To stay up-to-date with Mariner CVEs and Security Advisories, please join the
DL "Mariner-Security"

## Mariner AKS container host OS

We’re very excited to announce that the Mariner-AKS container host entered
private preview, with Office365’s team as the first adopter, and expanding to
other teams soon.  We’re aiming for GA to public clouds in July, and
availability in air-gapped and sovereign clouds afterwards. Currently, the
Mariner container host is internal use only. For more information about the
Mariner based Container host, please see click
[here](https://eng.ms/docs/products/mariner-linux/gettingstarted/containersandaks/aksnodeimage)
. This work is made possible thanks to close collaboration with the AKS team.
The demo can be found
[here.](https://microsoft.sharepoint.com/:v:/t/Mariner/ERUxl9KnsqNAqTEJSVOO1ZYB0qcOPG-21k5L5NSSoyJxvw?e=yV0jw6) 

## Secure boot

SecureBoot is now available and enabled by default in the ISO installation
media. This work is the culmination of several months of collaboration and
development with the upstream Grub project. This work ensures only signed
kernels and kernel modules can be executed, providing additional security for
Mariner users. 

## Certifications
Mariner is on track to have OpenSSL and Kernel FIPS140 certification by July 2022.  

There are 3 major steps for FIPS certification:
1.	[Implementation under test](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/modules-in-process/IUT-List) = contract with an external lab __Complete__
2.	Module in progress = external lab has submitted all evidence and analyses to the national lab NIST **July 2021**
3.	Certified implementation = NIST has completed certification **July 2022**

In addition to FIPS, the team is taking initial steps towards other certifications and security standards, including:
* STIGs: Mariner is at 82% alignment with RHEL8’s STIGs. It is possible that we can get DISA approval to be STIG-compliant in the short term and pursue a separate Mariner STIG in the long term.
* Azure Compliance for FedRAMP, CIS, etc.: Azure Compliance includes audits for various security certifications, including FedRAMP. We are onboarding to Azure Compliance by completing the requirements for Mariner as a platform.

_Note: Mariner is currently not funded for these areas. We are actively seeking resources for this as we go into Nickel planning._

## Amazon Linux package parity 

The Mariner team is making good progress with enabling packages to meet Amazon
Linux 2 package parity. To date we have 62% of all packages being built for. 

![z](images/z.jpg)

## Azure IoT Hub Device Provisioning 

This page contains instructions to create an Azure IoT Hub and Device
Provisioning Service using Azure Portal. [Click
here](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/75/Enabling-Azure-IoT-Hub-Device-Provisioning-Service)
to see instructions.   

# Resources and Help

## General Questions 

For general questions, please email MarinerQA@microsoft.com 
 
## Help with Mariner 

If you are self-starting on Mariner and have technical questions or run into issues, you can post your questions in the Mariner OS support [teams channel](https://teams.microsoft.com/dl/launcher/launcher.html?url=%2F_%23%2Fl%2Fteam%2F19%3Aa3a8a3987aa74b478870a2f246811e2d%40thread.tacv2%2Fconversations%3FgroupId%3D5ef827e3-78d9-4463-b0fe-23c2727b4342%26tenantId%3D72f988bf-86f1-41af-91ab-2d7cd011db47&type=team&deeplinkId=c9891dde-4cbc-48fd-81a7-b337f1bc03dc&directDl=true&msLaunch=true&enableMobilePage=true&suppressPrompt=true).   

Bugs should be filed directly in ADO. The Area Path should be OS\Core\STACK\IoT\Mariner.


## Staying up-to-date with announcements 

For Mariner release announcements and other updates,
[subscribe](https://idwebelements/GroupManagement.aspx?Group=marinerannouncements&Operation=join)
to this newsletter . Archived newsletters can be found
[here](https://microsoft.sharepoint.com/teams/Mariner/Shared%20Documents/Forms/AllItems.aspx?RootFolder=%2Fteams%2FMariner%2FShared%20Documents%2FGeneral%2FStatus%20updates%2FNewsletter&FolderCTID=0x012000793E844D28D7534CB6D9A2948283C9FF).
To stay up-to-date with Mariner CVEs and Security Advisories, please join the
DL "Mariner-Security"
 
## Azure Watson Debugging

To learn how to configure a Mariner image for debugging crashdumps in Azure
Watson, [click
here.](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/297/Azure-Watson-Debugging) 

To learn how to generate a crash dump, [click
here.](https://dev.azure.com/mariner-org/mariner/_wiki/wikis/mariner.wiki/346/Kernel-Crash-Dumps)   
 
## Filing Bugs

Use the Github "Issues" tab in the main Mariner Git repository to file issues
and get help related to Mariner.

Bugs can also be filed directly in ADO. The Area Path should be
OS\Core\STACK\IoT\Mariner. 
 
## Troubleshooting Overview 

This section provides help with Mariner troubleshooting. 

If you require support not found in this section, please post your questions in
the Mariner OS support [teams
channel.](https://teams.microsoft.com/dl/launcher/launcher.html?url=%2F_%23%2Fl%2Fteam%2F19%3Aa3a8a3987aa74b478870a2f246811e2d%40thread.tacv2%2Fconversations%3FgroupId%3D5ef827e3-78d9-4463-b0fe-23c2727b4342%26tenantId%3D72f988bf-86f1-41af-91ab-2d7cd011db47&type=team&deeplinkId=1fb76e65-0acc-4c51-a96f-4e99f8c4739b&directDl=true&msLaunch=true&enableMobilePage=true&suppressPrompt=true) 

As we receive questions in the teams channel, we will create new
troubleshooting documentation, as needed.

# Frequently Asked Questions

**How does Mariner relate to other Linux efforts within Microsoft?**

There are other Linux related efforts within Azure Edge & Platform and
Microsoft that are in use by various teams. For 1st party Azure services,
**Mariner is the recommended option**. If you've tried out Mariner and it
doesn't meet your service's needs, the LSGPMTeam@microsoft.com team can
recommend other options.

* [Secure Base Image (SBI):](https://osgwiki.com/wiki/LSG/SBI) SBI is an Ubuntu
  derived image that is packaged with AzSecPack to offer minimum Azure security
  compliance. SBI is available as a VM and offers FIPS compliance to 1st party
  services.  
* Overlake Linux targets an embedded SoC on the Azure host. Overlake is a
  purpose built solution to support network offloading in the Azure datacenter
  freeing the primary processors for use by paying customers. Just by the
  nature of constrained and secure environment, it is highly optimized to serve
  the special needs of that scenario. This distribution is being built by the
  Edge OS team. 
* [Azure
  Sphere](https://azure.microsoft.com/en-us/blog/azure-sphere-s-customized-linux-based-os/):
  Azure Sphere Linux OS is a purpose built distribution that target the MCU
  class of device that is used in Azure sphere chip. This is built and
  maintained by the Azure sphere team. 
* [CBL-D:](https://microsoft.sharepoint.com/teams/AzLinux54/SitePages/CBL-D.aspx?originalPath=aHR0cHM6Ly9taWNyb3NvZnQuc2hhcmVwb2ludC5jb20vOnU6L3QvQXpMaW51eDU0L0VldnQ2YXZpdE9kSmlELUU5b21SNXZRQnBraEItOG1RZm9vWDB4ZTUzM3NWYWc_cnRpbWU9QldXZk11NjMyRWc)
  Common Base Linux – Delridge (CBL-D) is a Debian-based Linux distribution
  built by the Azure Linux team and currently targets VM and container images
  for 1st party services.  

**How does CentOS being deprecated affect Mariner?** 
Mariner is not a downstream of CentOS, so the deprecation does not affect you
as a user.  We are RPM based, so a lot of the tooling like dnf that works on
CentOS works for us. In addition, a fair number of package names are similar,
which lessens time to migrate from CentOS to Mariner.  

**When will Mariner be released to 3rd-party customers?**

Mariner is limited for use in 1st-party Azure services and targeted Edge
scenarios. Mariner will remain an ingredient in another product and will not be
marketed as a separate product.  

**Will we release source for Mariner?** 

Yes. Mariner is designed to be an open source project with a rich internal and
external community that contributes to its success. You can find the public
Mariner source at [http://aka.ms/cbl-mariner](http://aka.ms/cbl-mariner). 

**Is Mariner being positioned as a competing offer to the existing in-market
Linux distributions?**

No, the focus for Mariner is to solve Microsoft specific needs e.g. end-to-end
solution that can be supported by Microsoft. Microsoft is committed to ensuring
a healthy ecosystem for our Linux distribution vendors.

**How do I access Mariner?**

Mariner is available through direct download and will soon be available in
Azure Marketplace. In the meantime, check out "Getting Started" -> "Building
Mariner Products" for using Mariner on Azure. The Mariner **container** image
is currently available in ACR. 

**What is Mariner's release cycle?**

Mariner plans for an annual release, with each release supported for 18 months.
Mariner will leverage the LTS kernel and will be regularly updated as new
stable packages are released. Images will be updated monthly with CVE fixes. 

**How do package updates work?**

Patches are automatically available, with a customer-initiated update.
Dnf-automatic is available and can be optionally added. For Mariner as the
container host on AKS, automatic updates will be also be optional. 

**Some packages (CNCF, K8s) have a more aggressive release cycle, and we don’t
want to be up to a year behind.  Do you have any plans for more frequent
upgrades?**
 
For CNCF packages we adopt newer packages like K8s with higher cadence and
won’t delay it for annual releases. We will however hold major compiler
upgrades or deprecating language stacks like Python 2.7x for major releases.

**We’d like to stay reasonably current with all packages in the distro, not
just those which have security updates or a specific function we want.**
 
We will make a pragmatic tradeoff between staying on older version + patch
versus moving to the newer version with patch. In most instances, we have saved
a ton of labor by moving to latest minor version of packages where the fix
still applies. Case in point is the kernel, where we are now moving from LTS
5.4 to LTS 5.10 rather than endlessly patching 5.4. 

**What is the SLA for updates?**

For critical vulnerabilities, the team is working to achieve a 72 business hour
SLA from the time a remediation is identified for a vulnerability in one of the
supported packages within Mariner. For a full breakdown of SLAs, please view
the "Security and CVEs" tab under "Features". 

As we learn more, Mariner may implement different SLAs and release schedules
for patches and package updates depending on the severity of the issues
included in the updates. Deployment of these updates to devices once an update
is available will be determined by the customer. 

**Mariner Core does not support a package I need.  How do I add a new package?**

You may add and build packages locally by adding them to your derivative
repository.  However, you may also make a request to include your package with
this
[form.](https://microsoft.visualstudio.com/OS/_workitems/create/Task?templateId=a923b03c-2520-4b4b-916b-2f99a1b30072&ownerId=104f5eef-e50a-4baa-9474-7fe2ec5bcd69)   

**Can you tell more about the OSTree, what its about?**

[OSTree](https://ostreedev.github.io/ostree/introduction/) is a way of doing
atomic upgrades of filesystems on a system. 

**What's the difference between Mariner's own RPM specfiles and common RPM
specfiles?**

We own and write the spec files versus forking them (say, from CentOS).

**Does Mariner support bare metal?**

Mariner works on bare metal and we are working on enabling this support more
broadly in Cobalt semester. If you have specific drivers/firmware to support
you can create your own ISOs/VHDs with those added.

**Is Mariner fully integrated with 1Branch / Component Governance?**
 
Yes, this is one of the key value propositions of using Mariner. Every product
we ship from the Azure org has to the 1Branch/Component Governance clean from
the outset.

**What is the size of Mariner?** 

The Mariner core image is about 350MB, uncompressed.  
 
**I'm currently on Ubuntu. Is there any migration tooling available?**

This is something we will be investigating in Nickel. 

**Will Mariner be available in sovereign clouds, airgapped, USNat, USSec?**

Yes, Mariner can be deployed to these clouds through Azure Marketplace and
Microsoft Container Registry (coming soon). It is the responsibility of each
service running Mariner to ensure they take the necessary sovereign/USSec/USNat
onboarding steps, and that their service remains compliant in these clouds.

**What is the root filesystem?**

Mariner uses Ext4 filesystem by default.

**Have you tested things like ansible?**

Ansible is built and published.

**Will az cli support be added soon?** 

Yes. In the meantime, you can pip install az-cli. The following commands are
tested and work in the Mariner docker container and VM: 

    tdnf install -y python3-setuptools python3-pip 
    pip3 install -–upgrade pip 
    pip3 install az.cli 

From there you can run ‘az login’ and other commands as expected 

**Do we have ways to export a kernel dump (especially after a kernel oops) to
storage, for later debugging? (i.e. using kexec)**

Azure Watson provides this functionality, and is enabled on Mariner.

**Will Python 3 be included in the key packages? I'm thinking for any native
ML...TF, TFLite, PyTorch etc.**

The package specs are open source on GitHub,
[CBL-Mariner/SPECS/python3](https://github.com/microsoft/CBL-Mariner/tree/1.0/SPECS/python3). 

**What kind of IoT hardware does Mariner for IoT run on? What's the leanest
device that can support it?**

* iMX8
* Raspberry-Pi
* x64

**Will Mariner run on the Windows Subsystem for Linux?**

You can use Mariner on WSL.  

**Any plans for supporting mariner with AKS-Engine?**

While we did some investigations for this, we do not have any plans at this
time to productize this. We are currently focused on Mariner AKS container
host. Please reach out to us if you have a request 

# Closing Remarks

Thanks for reading this document and we hope that you find Mariner a useful
addition to your IT strategy.

