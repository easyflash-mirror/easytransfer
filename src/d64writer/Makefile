
# do not remove intermediate targets
.SECONDARY:

name := d64writer

ifneq "$(release)" "yes"
	version := $(shell date +%y%m%d-%H%M)
else
	version := 1.0.0
endif

obj :=
obj += obj/d64writer.o
obj += obj/write_disk.o

inc      := src
inc      += ../../libef3usb/src
inc      += ../../eload/src

INCLUDE  := $(addprefix -I,$(inc))

DEFINE   := -DEFVERSION=\"${version}\"

libef3usb := ../../libef3usb/libef3usb.lib
libeload  := ../../eload/eload.lib

.PHONY: all
all: $(name).prg

###############################################################################
# Poor men's dependencies: Let all files depend from all header files
#
headers := $(wildcard $(INCLUDE)/*.h)

obj/%.s: src/%.c $(headers) | obj
	cc65 -t c64 -T -O --static-locals $(INCLUDE) $(DEFINE) -o $@ $<

###############################################################################
obj/%.o: obj/%.s | obj
	ca65 -t c64 $(INCLUDE) -o $@ $<

###############################################################################
obj/%.o: src/%.s | obj
	ca65 -t c64 $(INCLUDE) -o $@ $<

###############################################################################
#
$(libef3usb): force
	$(MAKE) -C $(dir $@)

$(libeload): force
	$(MAKE) -C $(dir $@)

.PHONY: force
force:

###############################################################################
obj:
	mkdir -p $@

$(name).prg: obj/crt0.o $(obj) src/ld.cfg $(libef3usb) $(libeload)
	cl65 -o $@ -m $@.map -C src/ld.cfg -t c64 $(obj) $(libef3usb) $(libeload)
	cat $@.map | grep -e "^Name\|^CODE\|^DATA\|^BSS\|^RODATA\|^LOWCODE"

.PHONY: clean
clean:
	rm -f $(name).prg
	rm -f $(obj)
	rm -rf obj
	-$(MAKE) -C $(dir $(libef3usb)) clean
	-$(MAKE) -C $(dir $(libeload)) clean

