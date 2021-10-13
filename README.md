# UEFI for Armada 8040 Platforms

This is a source-code collection for building UEFI firmware for SolidRun Armada 8040 based devices,
based on the [System Ready ES](https://developer.arm.com/architectures/system-architectures/arm-systemready/es) compliant [EDK2 Firmware by Semihalf](https://github.com/Semihalf/edk2-platforms/wiki).

Binaries produced from this collection should be functionally equivalent to the Semihalf SH 1.0 Release.

# Compile

Fetch all sources:

    git clone -b develop https://github.com/Josua-SR/armada-8040-uefi.git
    cd armada-8040-uefi
    git submodule update --init --recursive

Configure cross-compiler:

    export GCC5_AARCH64_PREFIX=aarch64-linux-gnu-
    export CROSS_COMPILE=aarch64-linux-gnu-

Setup EDK2 build-environment:

    export PACKAGES_PATH=$PWD/edk2:$PWD/edk2-platforms:$PWD/edk2-non-osi
    export EDK_TOOLS_PATH=$PWD/edk2/BaseTools
    source edk2/edksetup.sh

Build EDK2 Tools (only needed once)

    make -C edk2/BaseTools

Build EDK2 for a target device:

    build -a AARCH64 -b RELEASE -t GCC5 -p Platform/SolidRun/Armada80x0McBin/Armada80x0McBin.dsc -D X64EMU_ENABLE

Build Firmware image with ATF:

    make -C arm-trusted-firmware \
    	PLAT=a80x0_mcbin \
    	MV_DDR_PATH=$PWD/mv-ddr \
    	SCP_BL2=$PWD/binaries-marvell/mrvl_scp_bl2.img \
    	BL33=$PWD/Build/Armada80x0McBin-AARCH64/RELEASE_GCC5/FV/ARMADA_EFI.fd \
    	all fip mrvl_flash

Find the bootable firmware image:

    ls -lh arm-trusted-firmware/build/a80x0_mcbin/release/flash-image.bin
