######################################
# target
######################################
TARGET = hover

######################################
# building variables
######################################
# debug build?
DEBUG = 1
# optimization
OPT = -Og

# Build path
BUILD_DIR = build

######################################
# source
######################################
# C sources
C_SOURCES =  \
RTE/Device/GD32F130C8/gd32f1x0_adc.c \
RTE/Device/GD32F130C8/gd32f1x0_dma.c \
RTE/Device/GD32F130C8/gd32f1x0_fwdgt.c \
RTE/Device/GD32F130C8/gd32f1x0_gpio.c \
RTE/Device/GD32F130C8/gd32f1x0_i2c.c \
RTE/Device/GD32F130C8/gd32f1x0_misc.c \
RTE/Device/GD32F130C8/gd32f1x0_opa.c \
RTE/Device/GD32F130C8/gd32f1x0_pmu.c \
RTE/Device/GD32F130C8/gd32f1x0_rcu.c \
RTE/Device/GD32F130C8/gd32f1x0_syscfg.c \
RTE/Device/GD32F130C8/gd32f1x0_timer.c \
RTE/Device/GD32F130C8/gd32f1x0_usart.c \
RTE/Device/GD32F130C8/system_gd32f1x0.c \
Src/bldc.c \
Src/comms.c \
Src/commsBluetooth.c \
Src/commsMasterSlave.c \
Src/commsSteering.c \
Src/it.c \
Src/led.c \
Src/main.c \
Src/setup.c \

# ASM sources
ASM_SOURCES = RTE/Device/GD32F130C8/startup_gd32f1x0.s

#######################################
# binaries
#######################################
PREFIX = arm-none-eabi-
CC = $(PREFIX)gcc
AS = $(PREFIX)gcc -x assembler-with-cpp
CP = $(PREFIX)objcopy
AR = $(PREFIX)ar
SZ = $(PREFIX)size
HEX = $(CP) -O ihex
BIN = $(CP) -O binary -S

#######################################
# CFLAGS
#######################################
# cpu
CPU = -mcpu=cortex-m3

# fpu
# NONE for Cortex-M0/M0+/M3

# float-abi


# mcu
MCU = $(CPU) -mthumb $(FPU) $(FLOAT-ABI)

# macros for gcc
# AS defines
AS_DEFS =

# C defines
C_DEFS =  \
#-DUSE_HAL_DRIVER \
#-DSTM32F103xE


# AS includes
AS_INCLUDES =

# C includes
C_INCLUDES =  \
-IInc \
-IRTE/_Target_1 \


# compile gcc flags
ASFLAGS = $(MCU) $(AS_DEFS) $(AS_INCLUDES) $(OPT) -Wall -fdata-sections -ffunction-sections

CFLAGS = $(MCU) $(C_DEFS) $(C_INCLUDES) $(OPT) -Wall -fdata-sections -ffunction-sections -std=gnu11

ifeq ($(DEBUG), 1)
CFLAGS += -g -gdwarf-2
endif


# Generate dependency information
CFLAGS += -MMD -MP -MF"$(@:%.o=%.d)" -MT"$(@:%.o=%.d)"


#######################################
# LDFLAGS
#######################################
# link script
LDSCRIPT = GD32F130C8Tx_FLASH.ld

# libraries
LIBS = -lc -lm -lnosys
LIBDIR =
LDFLAGS = $(MCU) -specs=nano.specs -T$(LDSCRIPT) $(LIBDIR) $(LIBS) -Wl,-Map=$(BUILD_DIR)/$(TARGET).map,--cref -Wl,--gc-sections

# default action: build all
all: $(BUILD_DIR)/$(TARGET).elf $(BUILD_DIR)/$(TARGET).hex $(BUILD_DIR)/$(TARGET).bin


#######################################
# build the application
#######################################
# list of objects
OBJECTS = $(addprefix $(BUILD_DIR)/,$(notdir $(C_SOURCES:.c=.o)))
vpath %.c $(sort $(dir $(C_SOURCES)))
# list of ASM program objects
OBJECTS += $(addprefix $(BUILD_DIR)/,$(notdir $(ASM_SOURCES:.s=.o)))
vpath %.s $(sort $(dir $(ASM_SOURCES)))

$(BUILD_DIR)/%.o: %.c Inc/config.h Makefile | $(BUILD_DIR)
	$(CC) -c $(CFLAGS) -Wa,-a,-ad,-alms=$(BUILD_DIR)/$(notdir $(<:.c=.lst)) $< -o $@

$(BUILD_DIR)/%.o: %.s Inc/config.h Makefile | $(BUILD_DIR)
	$(AS) -c $(CFLAGS) $< -o $@

$(BUILD_DIR)/$(TARGET).elf: $(OBJECTS) Makefile
	$(CC) $(OBJECTS) $(LDFLAGS) -o $@
	$(SZ) $@

$(BUILD_DIR)/%.hex: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(HEX) $< $@

$(BUILD_DIR)/%.bin: $(BUILD_DIR)/%.elf | $(BUILD_DIR)
	$(BIN) $< $@

$(BUILD_DIR):
	mkdir -p $@

format:
	find Src/ Inc/ -iname '*.h' -o -iname '*.c' | xargs clang-format -i
#######################################
# clean up
#######################################
clean:
	-rm -fR .dep $(BUILD_DIR)

flash:
	st-flash --reset write $(BUILD_DIR)/$(TARGET).bin 0x8000000

unlock:
	openocd -f interface/stlink-v2.cfg -f target/stm32f1x.cfg -c init -c "reset halt" -c "stm32f1x unlock 0"

#######################################
# dependencies
#######################################
-include $(shell mkdir .dep 2>/dev/null) $(wildcard .dep/*)

# *** EOF ***
