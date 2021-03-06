CPU_ARCH=a53
OUTPUT=../platform_repo/tmp/sw_components/
SYSROOT=../platform_repo/sysroot
all: refresh_hw xrt zocl kernel_config rootfs_config linux sw_comp bootimage
export COMMON_RFS_KRNL_SYSROOT = TRUE

refresh_hw:
	petalinux-config --get-hw-description=${XSA_DIR} --silentconfig

sw_config: xrt zocl kernel_config rootfs_config

xrt:
	petalinux-config -c xrt --silentconfig

zocl:
	petalinux-config -c zocl --silentconfig

kernel_config:
	petalinux-config -c kernel --silentconfig

rootfs_config:
	petalinux-config -c rootfs --silentconfig

linux:
	petalinux-build

ifeq ($(COMMON_RFS_KRNL_SYSROOT), TRUE)
sw_comp:
	mkdir -p $(OUTPUT)
	cp -rf $(XSA_DIR)/src $(OUTPUT)
	mkdir -p ${OUTPUT}/src/boot
	cp -f images/linux/boot.scr ${OUTPUT}/src/${CPU_ARCH}/xrt/image/boot.scr
	cp -f images/linux/system.dtb ${OUTPUT}/src/${CPU_ARCH}/xrt/image/system.dtb
	cp -f images/linux/bl31.elf ${OUTPUT}/src/boot/bl31.elf
	cp -f images/linux/pmufw.elf ${OUTPUT}/src/boot/pmufw.elf
	cp -f images/linux/u-boot.elf ${OUTPUT}/src/boot/u-boot.elf
	cp -f images/linux/zynqmp_fsbl.elf ${OUTPUT}/src/boot/fsbl.elf
else
sw_comp:
	mkdir -p $(OUTPUT)
	cp -rf $(XSA_DIR)/src $(OUTPUT)
	mkdir -p ${OUTPUT}/src/boot
	mkdir -p ${OUTPUT}/src/filesystem
	cp -f images/linux/Image ${OUTPUT}/src/${CPU_ARCH}/xrt/image/Image
	cp -f images/linux/rootfs.tar.gz ${OUTPUT}/src/filesystem/rootfs.tar.gz
	cp -f images/linux/rootfs.ext4 ${OUTPUT}/src/filesystem/rootfs.ext4
	cp -f images/linux/rootfs.cpio.gz.u-boot ${OUTPUT}/src/filesystem/rootfs.cpio.gz.u-boot
	cp -f images/linux/boot.scr ${OUTPUT}/src/${CPU_ARCH}/xrt/image/boot.scr
	cp -f images/linux/system.dtb ${OUTPUT}/src/${CPU_ARCH}/xrt/image/system.dtb
	cp -f images/linux/bl31.elf ${OUTPUT}/src/boot/bl31.elf
	cp -f images/linux/pmufw.elf ${OUTPUT}/src/boot/pmufw.elf
	cp -f images/linux/u-boot.elf ${OUTPUT}/src/boot/u-boot.elf
	cp -f images/linux/zynqmp_fsbl.elf ${OUTPUT}/src/boot/fsbl.elf
endif

bootimage:
	@echo "BOOT image for PR platforms"
	petalinux-package --force --boot --fsbl images/linux/zynqmp_fsbl.elf --fpga project-spec/hw-description/$(PLATFORM).bit --u-boot images/linux/u-boot.elf --pmufw images/linux/pmufw.elf --atf images/linux/bl31.elf
	cp -rf images/linux/BOOT.BIN ${OUTPUT}/src/${CPU_ARCH}/xrt/image/

sysroot:
	mkdir -p ${SYSROOT}
	petalinux-build --sdk
	petalinux-package --sysroot -d ${SYSROOT}

clean:
	petalinux-build -x cleanall
	$(RM) -r images/linux/*

ultraclean: clean
	${RM} -r build ip_cache components

