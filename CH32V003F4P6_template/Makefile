# Edit this for your toolchain path
WCH_RV_TOOLCHAIN_DIR ?= /x/x/x/x/x/MRS_Toolchain_MAC_V191

# Check for names, versions can be change later
PREFIX = $(WCH_RV_TOOLCHAIN_DIR)/xpack-riscv-none-embed-gcc-8.2.0/bin/riscv-none-embed-
WLINK_PATH = $(WCH_RV_TOOLCHAIN_DIR)/wlink-macos-arm64/wlink

BUILD_TARGET = ch32v003-template
BUILD_DIR = build

DEBUG = 0
ARCH = -march=rv32ec -mabi=ilp32e
OPT = -Os
STD = -std=gnu17

C_SOURCES = \
Core/core_riscv.c \
User/ch32v00x_it.c \
User/system_ch32v00x.c \
Debug/debug.c \
Peripheral/src/ch32v00x_adc.c \
Peripheral/src/ch32v00x_dbgmcu.c \
Peripheral/src/ch32v00x_dma.c \
Peripheral/src/ch32v00x_exti.c \
Peripheral/src/ch32v00x_flash.c \
Peripheral/src/ch32v00x_gpio.c \
Peripheral/src/ch32v00x_i2c.c \
Peripheral/src/ch32v00x_iwdg.c \
Peripheral/src/ch32v00x_misc.c \
Peripheral/src/ch32v00x_opa.c \
Peripheral/src/ch32v00x_pwr.c \
Peripheral/src/ch32v00x_rcc.c \
Peripheral/src/ch32v00x_spi.c \
Peripheral/src/ch32v00x_tim.c \
Peripheral/src/ch32v00x_usart.c \
Peripheral/src/ch32v00x_wwdg.c \
User/main.c

ASM_SOURCES = \
Startup/startup_ch32v00x.S

C_INCLUDES = \
-ICore \
-IDebug \
-IPeripheral/inc \
-IUser

CC = $(PREFIX)gcc
AS = $(PREFIX)gcc -x assembler-with-cpp
CP = $(PREFIX)objcopy
SZ = $(PREFIX)size

HEX = $(CP) -O ihex
BIN = $(CP) -O binary -S

CFLAGS = $(ARCH) \
$(OPT) \
$(STD) \
-msmall-data-limit=0 \
-msave-restore \
-fmessage-length=0 \
-fsigned-char \
-ffunction-sections \
-fdata-sections \
-fno-common \
-fstack-usage \
-Wunused -Wuninitialized \
$(C_INCLUDES) \
-MMD -MP -MF"$(@:%.o=%.d)"

ASFLAGS = $(ARCH) $(OPT) -x assembler

LDSCRIPT = Ld/Link.ld
LIBS = -lnosys

LDFLAGS = $(ARCH) \
$(OPT) \
$(STD) \
-msmall-data-limit=0 \
-msave-restore \
-fmessage-length=0 \
-fsigned-char \
-ffunction-sections \
-fdata-sections \
-fno-common \
-fstack-usage \
-Wunused -Wuninitialized \
-T$(LDSCRIPT) \
-nostartfiles \
-Xlinker \
--gc-sections \
-Wl,-Map,$(BUILD_DIR)/${BUILD_TARGET}.map \
--specs=nano.specs \
--specs=nosys.specs \
$(LIBS)

ifeq ($(DEBUG), 1)
CFLAGS += -g
LDFLAGS += -g
endif

OBJECTS = $(addprefix $(BUILD_DIR)/,$(notdir $(C_SOURCES:.c=.o)))
vpath %.c $(sort $(dir $(C_SOURCES)))
OBJECTS += $(addprefix $(BUILD_DIR)/,$(notdir $(ASM_SOURCES:.S=.o)))
vpath %.S $(sort $(dir $(ASM_SOURCES)))

all:$(BUILD_DIR)/$(BUILD_TARGET).elf $(BUILD_DIR)/$(BUILD_TARGET).hex $(BUILD_DIR)/$(BUILD_TARGET).bin

$(BUILD_DIR)/%.o:%.c Makefile | $(BUILD_DIR)
	$(CC) -c $(CFLAGS) $< -o $@

$(BUILD_DIR)/%.o:%.S Makefile | $(BUILD_DIR)
	$(AS) -c $(ASFLAGS) $< -o $@

$(BUILD_DIR)/$(BUILD_TARGET).elf:$(OBJECTS) Makefile
	$(CC) $(LDFLAGS) $(OBJECTS) -o $@
	$(SZ) --format=berkeley $@

$(BUILD_DIR)/%.hex:$(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(HEX) $< $@

$(BUILD_DIR)/%.bin:$(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(BIN) $< $@

$(BUILD_DIR):
	mkdir $@

clean:
	rm -rf $(BUILD_DIR)

flash_program:
	"$(WLINK_PATH)" flash --address 0x08000000 $(BUILD_DIR)/$(BUILD_TARGET).bin

flash_erase:
	"$(WLINK_PATH)" erase

reset:
	"$(WLINK_PATH)" reset

upload_program: all flash_erase flash_program reset