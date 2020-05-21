#https://eleccelerator.com/fusecalc/fusecalc.php?chip=atmega328p&LOW=E2&HIGH=D2&EXTENDED=FD&LOCKBIT=3F

MCU=atmega328p
F_CPU=8000000
BAUD_RATE=57600
LED_START_FLASHES=3

OBJCOPY=avr-objcopy
CFLAGS=-std=gnu99 -Wall -g -mmcu=${MCU} -DF_CPU=${F_CPU} -I.
CFLAGS+=-DBAUD_RATE=${BAUD_RATE} -DLED_START_FLASHES=${LED_START_FLASHES}
CFLAGS+=-fpack-struct -fshort-enums -ffunction-sections -fdata-sections
CFLAGS+=-Os -fno-inline-small-functions -fno-split-wide-types -mrelax
LDFLAGS=-Wl,--section-start=.text=0x7800
LDFLAGS+=-Wl,--section-start=.opti_api=0x7fe8
LDFLAGS+=-Wl,-section-start=.mystrings=0x7fc0
LDFLAGS+=-Wl,--section-start=.version=0x7ffe
LDFLAGS+=-Wl,--relax -Wl,--gc-sections -nostartfiles -nostdlib
LDFLAGS+=-Wl,--undefined=api_functions -Wl,--undefined=optiboot_version

TARGET=optiboot
SRC = optiboot.c bootapi.c stub.c
OBJS = $(SRC:.c=.o)

.c.o:
	@echo 'Building $@'
	-avr-gcc $(CFLAGS) -c $<
	@echo ' '

all: optiboot.elf optiboot.hex sizedummy

optiboot.elf: $(OBJS)
	@echo 'Building target: $@'
	-avr-gcc $(CFLAGS) $(LDFLAGS) -o $(TARGET).elf $(OBJS)
	@echo 'Finished building target: $@'
	@echo ' '

optiboot.hex: optiboot.elf
	@echo 'Create Flash image (ihex format)'
	-avr-objcopy -R .eeprom -R .fuse -R .lock -R .signature -O ihex optiboot.elf  "optiboot.hex"
	@echo 'Finished building: $@'
	@echo ' '

sizedummy: optiboot.elf
	@echo 'Invoking: Print Size'
	-avr-size --format=avr --mcu=atmega328p optiboot.elf
	@echo 'Finished building: $@'
	@echo ' '

flash:
	avrdude -p ${MCU} -c usbasp-clone -U flash:w:${TARGET}.hex:i \
    -U lfuse:w:0xE2:m -U hfuse:w:0xD2:m -U efuse:w:0x05:m -U lock:w:0x3F:m
	@echo ' '

clean:
	rm -f *.o *.elf *.hex
