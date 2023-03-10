# This file is included by the global makefile so that you can add your own
# architecture-specific flags and dependencies. Remember to do have actions
# for "archclean" and "archdep" for cleaning up and making dependencies for
# this architecture
#
# This file is subject to the terms and conditions of the GNU General Public
# License.  See the file "COPYING" in the main directory of this archive
# for more details.
#

OBJCOPYFLAGS    := -O binary
LDFLAGS_vmlinux :=
ifeq ($(CONFIG_DYNAMIC_FTRACE),y)
	LDFLAGS_vmlinux := --no-relax
endif
KBUILD_AFLAGS_MODULE += -fPIC
KBUILD_CFLAGS_MODULE += -fPIC

export BITS
ifeq ($(CONFIG_ARCH_RV64I),y)
	BITS := 64
	UTS_MACHINE := riscv64

	KBUILD_CFLAGS += -mabi=lp64
	KBUILD_AFLAGS += -mabi=lp64

	KBUILD_LDFLAGS += -melf64lriscv
else
	BITS := 32
	UTS_MACHINE := riscv32

	KBUILD_CFLAGS += -mabi=ilp32
	KBUILD_AFLAGS += -mabi=ilp32
	KBUILD_LDFLAGS += -melf32lriscv
endif

# ISA string setting
riscv-march-cflags-$(CONFIG_ARCH_RV32I)		:= rv32ima
riscv-march-cflags-$(CONFIG_ARCH_RV64I)		:= rv64ima
riscv-march-cflags-$(CONFIG_RISCV_ISA_C)	:= $(riscv-march-cflags-y)c

riscv-march-aflags-$(CONFIG_ARCH_RV32I)		:= rv32ima
riscv-march-aflags-$(CONFIG_ARCH_RV64I)		:= rv64ima
riscv-march-aflags-$(CONFIG_FPU)		:= $(riscv-march-aflags-y)fd
riscv-march-aflags-$(CONFIG_RISCV_ISA_C)	:= $(riscv-march-aflags-y)c
riscv-march-aflags-$(CONFIG_VECTOR)		:= $(riscv-march-aflags-y)v0p7
riscv-march-aflags-$(CONFIG_RISCV_ISA_THEAD)	:= $(riscv-march-aflags-y)v0p7xthead

KBUILD_CFLAGS += -march=$(riscv-march-cflags-y) -Wa,-march=$(riscv-march-aflags-y)
KBUILD_AFLAGS += -march=$(riscv-march-aflags-y)

KBUILD_CFLAGS += -mno-save-restore
KBUILD_CFLAGS += -DCONFIG_PAGE_OFFSET=$(CONFIG_PAGE_OFFSET)

ifeq ($(CONFIG_CMODEL_MEDLOW),y)
	KBUILD_CFLAGS += -mcmodel=medlow
endif
ifeq ($(CONFIG_CMODEL_MEDANY),y)
	KBUILD_CFLAGS += -mcmodel=medany
endif
ifeq ($(CONFIG_MODULE_SECTIONS),y)
	KBUILD_LDS_MODULE += $(srctree)/arch/riscv/kernel/module.lds
endif
ifeq ($(CONFIG_PERF_EVENTS),y)
        KBUILD_CFLAGS += -fno-omit-frame-pointer
endif

KBUILD_CFLAGS_MODULE += $(call cc-option,-mno-relax)

# GCC versions that support the "-mstrict-align" option default to allowing
# unaligned accesses.  While unaligned accesses are explicitly allowed in the
# RISC-V ISA, they're emulated by machine mode traps on all extant
# architectures.  It's faster to have GCC emit only aligned accesses.
KBUILD_CFLAGS += $(call cc-option,-mstrict-align)

# arch specific predefines for sparse
CHECKFLAGS += -D__riscv -D__riscv_xlen=$(BITS)

# Default target when executing plain make
boot		:= arch/riscv/boot
KBUILD_IMAGE	:= $(boot)/Image.gz

head-y := arch/riscv/kernel/head.o

core-y += arch/riscv/

libs-y += arch/riscv/lib/

PHONY += vdso_install
vdso_install:
	$(Q)$(MAKE) $(build)=arch/riscv/kernel/vdso $@

all: Image.gz

Image: vmlinux
	$(Q)$(MAKE) $(build)=$(boot) $(boot)/$@

Image.%: Image
	$(Q)$(MAKE) $(build)=$(boot) $(boot)/$@

zinstall install:
	$(Q)$(MAKE) $(build)=$(boot) $@
