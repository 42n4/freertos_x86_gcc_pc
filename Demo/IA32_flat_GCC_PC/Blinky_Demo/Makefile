PROJECT         :=  Blinky_Demo
SOURCE_ROOT     :=  ../../..
BUILD_DIR       :=  build

INCLUDE_DIR     :=  -I$(SOURCE_ROOT)/Source/include \
                    -I$(SOURCE_ROOT)/Source/portable/GCC/IA32_flat \
                    -I$(SOURCE_ROOT)/Demo/Common/include \
                    -I$(SOURCE_ROOT)/Demo/IA32_flat_GCC_PC/Support_Files \
                    -I$(SOURCE_ROOT)/Demo/IA32_flat_GCC_PC/$(PROJECT)
SOURCE_DIR      :=  $(SOURCE_ROOT)/Source \
                    $(SOURCE_ROOT)/Demo/IA32_flat_GCC_PC/Support_Files \
                    $(SOURCE_ROOT)/Demo/IA32_flat_GCC_PC/$(PROJECT)
SOURCE_EXCLUDE  :=  $(SOURCE_ROOT)/Demo/IA32_flat_GCC_PC/Support_Files/libxen

OBJECT_ROOT     :=  $(BUILD_DIR)/obj
BUILD           :=  $(BUILD_DIR)/$(PROJECT).elf
MAP             :=  $(BUILD_DIR)/$(PROJECT).map
LDS             :=  $(SOURCE_ROOT)/Demo/IA32_flat_GCC_PC/elf_ia32_efi.lds

find             =  $(filter-out $(SOURCE_EXCLUDE),$(wildcard $1/$2)) \
                    $(foreach dir,$(filter-out $(SOURCE_EXCLUDE),$(wildcard $1/*)),$(call find,$(dir),$2))
SOURCE_C        :=  $(foreach dir,$(SOURCE_DIR),$(call find,$(dir),*.c))
SOURCE_S        :=  $(foreach dir,$(SOURCE_DIR),$(call find,$(dir),*.S))
OBJECT          :=  $(patsubst $(SOURCE_ROOT)%,$(OBJECT_ROOT)%,$(patsubst %.c,%.o,$(SOURCE_C))) \
                    $(patsubst $(SOURCE_ROOT)%,$(OBJECT_ROOT)%,$(patsubst %.S,%.o,$(SOURCE_S)))

LD              :=  i686-unknown-linux-gnueabi-gcc
LDFLAGS         :=  -nostdlib -Xlinker -T -Xlinker $(LDS) -Xlinker -Map=$(MAP) -Xlinker --gc-sections
LDADD           :=  -lgcc
CC              :=  i686-unknown-linux-gnueabi-gcc
CFLAGS          :=  $(INCLUDE_DIR) -D'__XEN_INTERFACE_VERSION__=0x00040400' \
                    -O0 -g3 -Wall -Wextra -c -fmessage-length=0 -march=pentium \
                    -Wno-ignored-qualifiers -mno-ms-bitfields -ffunction-sections -ffreestanding
AS              :=  i686-unknown-linux-gnueabi-gcc
SFLAGS          :=  $(INCLUDE_DIR) -Wa,--gdwarf2 -Wa,-march=pentium -c

.PHONY: clean

all: $(BUILD)

$(BUILD): $(OBJECT)
	@echo "LD      $(BUILD)"
	@$(LD) $(LDFLAGS) -o $(BUILD) $(OBJECT) $(LDADD)

$(OBJECT_ROOT)/%.o: $(SOURCE_ROOT)/%.c
	@mkdir -p $(dir $@)
	@echo "CC      $@"
	@$(CC) $(CFLAGS) -o $@ $<

$(OBJECT_ROOT)/%.o: $(SOURCE_ROOT)/%.S
	@mkdir -p $(dir $@)
	@echo "AS      $@"
	@$(AS) $(SFLAGS) -o $@ $<

clean:
	rm -f $(BUILD) $(MAP) $(OBJECT)
