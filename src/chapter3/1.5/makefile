# chapter3/1/Makefile
#
# Author: Wenbo Yang <solrex@gmail.com> <http://solrex.cn>
#
# This file is part of the source code of book "Write Your Own OS with Free
# and Open Source Software". Homepage @ <http://share.solrex.cn/WriteOS/>.
#
# This file is licensed under the GNU General Public License; either
# version 3 of the License, or (at your option) any later version. 

CC=gcc
LD=ld
OBJCOPY=objcopy

CFLAGS=-c -g
TRIM_FLAGS=-R .pdr -R .comment -R.note -S -O binary
#SCFLAGS=-fno-builtin -Wall -Wextra -nostdlib -nostartfiles -nodefaultlibs -Werror
SCFLAGS=-fno-builtin -Wall -Wextra -nostdlib -nostartfiles -nodefaultlibs

LDFILE_BOOT=solrex_x86_boot.ld
LDFILE_DOS=solrex_x86_dos.ld
#LDFILE_DOS=c.ld
LDFLAGS_BOOT=-T$(LDFILE_BOOT)
LDFLAGS_DOS=-T$(LDFILE_DOS)

5mcvga.com: 5M_mem_rw_mix_c_vga.o switch_vga_mode.o
	$(LD) -o 5M_mem_rw_mix_c_vga.elf $(LDFLAGS_DOS) $^
	$(OBJCOPY) $(TRIM_FLAGS) 5M_mem_rw_mix_c_vga.elf $@
5M_mem_rw_mix_c_vga.o: 5M_mem_rw_mix_c_vga.S config.h
	$(CC) $(CFLAGS) $<

switch_vga_mode.com: switch_vga_mode.o
	$(LD) -o switch_vga_mode.elf $(LDFLAGS_DOS) $^
	$(OBJCOPY) $(TRIM_FLAGS) switch_vga_mode.elf $@
switch_vga_mode.o: switch_vga_mode.c config.h
	gcc -nostdlib -nostartfiles -nodefaultlibs -c $<

cg_r32r0.com: cg_r3_to_r0.elf
	$(OBJCOPY) $(TRIM_FLAGS) $< $@
cg_r3_to_r0.elf: cg_r3_to_r0.o 
	$(LD) -o $@ $(LDFLAGS_DOS) $^
cg_r3_to_r0.o: cg_r3_to_r0.S
	$(CC) $(CFLAGS) $<

cg_to_r3.com: cg_to_r3.elf
	$(OBJCOPY) $(TRIM_FLAGS) $< $@
cg_to_r3.elf: cg_to_r3.o 
	$(LD) -o $@ $(LDFLAGS_DOS) $^
cg_to_r3.o: cg_to_r3.S
	$(CC) $(CFLAGS) $<

pt_1.com: pt_1.elf
	$(OBJCOPY) $(TRIM_FLAGS) $< $@
pt_1.elf: pt_1.o 
	$(LD) -o $@ $(LDFLAGS_DOS) $^
pt_1.o: pt_1.S pm.h
	$(CC) $(CFLAGS) $<

pt_2.com: pt_2.elf
	$(OBJCOPY) $(TRIM_FLAGS) $< $@
pt_2.elf: pt_2.o 
	$(LD) -o $@ $(LDFLAGS_DOS) $^
pt_2.o: pt_2.S pm.h
	$(CC) $(CFLAGS) $<

cg.com: cg.elf
	$(OBJCOPY) $(TRIM_FLAGS) $< $@
cg.elf: cg.o 
	$(LD) -o $@ $(LDFLAGS_DOS) $^
cg.o: cg.S
	$(CC) $(CFLAGS) $<
ldt.com: ldt.o 
	$(LD) -o ldt.elf $(LDFLAGS_DOS) $^
	$(OBJCOPY) $(TRIM_FLAGS) ldt.elf $@
ldt.o: ldt.S
	$(CC) $(CFLAGS) $<

#5M_mem_rw.com: 5M_mem_rw.o k.o
5mrw.bin: 5M_mem_rw.o 
	$(LD) -o 5M_mem_rw.elf $(LDFLAGS_BOOT) $^
	$(OBJCOPY) $(TRIM_FLAGS) 5M_mem_rw.elf $@
5mrw.com: 5M_mem_rw.o 
	$(LD) -o 5M_mem_rw.elf $(LDFLAGS_DOS) $^
	$(OBJCOPY) $(TRIM_FLAGS) 5M_mem_rw.elf $@

5M_mem_rw.o: 5M_mem_rw.S
	$(CC) $(CFLAGS) $<

5mrwbr.com: 5M_mem_rw_big_real.o
	$(LD) -o 5M_mem_rw.elf $(LDFLAGS_DOS) $^
	$(OBJCOPY) $(TRIM_FLAGS) 5M_mem_rw.elf $@

5M_mem_rw_big_real.o: 5M_mem_rw_big_real.S
	$(CC) $(CFLAGS) $<

5mc.com: 5M_mem_rw_mix_c.o k.o 
	$(LD) -o 5mc.elf $(LDFLAGS_DOS) $^
	$(OBJCOPY) $(TRIM_FLAGS) 5mc.elf $@

5M_mem_rw_mix_c.o: 5M_mem_rw_mix_c.S
	$(CC) $(CFLAGS) $<

k.s: k.c
	gcc $(SCFLAGS) -S $<
k.o: k.s
	gcc $(SCFLAGS) -c $<

idt.com: idt.com.elf
	$(OBJCOPY) $(TRIM_FLAGS) $< $@
idt.com.elf: idt.com.o
	#$(LD) -o $@ $(LDFLAGS_DOS) $<
	$(LD) -o $@ -Tdescent.ld $<
idt.com.o: idt.S
	$(CC) -o $@ $(CFLAGS) $<


LOADER.BIN: loader.S
	$(CC) $(CFLAGS) loader.S
	$(LD) loader.o -o loader.elf $(LDFLAGS_DOS)
	$(OBJCOPY) $(TRIM_FLAGS) loader.elf $@

boot.img: boot.bin
	@dd if=boot.bin of=boot.img bs=512 count=1
	@dd if=/dev/zero of=boot.img skip=1 seek=1 bs=512 count=2879

# You must have the authority to do mount, or you must use "su root" or
# "sudo" command to do "make copy"
copy: boot.img LOADER.BIN
	@mkdir -p /tmp/floppy;\
	mount -o loop boot.img /tmp/floppy/ -o fat=12;\
	cp LOADER.BIN /tmp/floppy/;\
	umount /tmp/floppy/;\
	rm -rf /tmp/floppy/;

clean: 
	rm -f *.o *.elf *.bin *.BIN *.com
	#@rm -f *.o *.elf *.bin *.BIN

distclean: clean
	@rm -f boot.img

release: clean
	@mv boot.img solrex.img
