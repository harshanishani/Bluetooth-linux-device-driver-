MODULE := hello_bt_serdev

obj-m := $(MODULE).o

# Kernel build directory
KDIR := /lib/modules/$(shell uname -r)/build

# Current directory
PWD := $(shell pwd)


all:
	$(MAKE) -C $(KDIR) M=$(PWD) modules


clean:
	$(MAKE) -C $(KDIR) M=$(PWD) clean


install:
	sudo insmod $(MODULE).ko
	dmesg | tail -n 20


remove:
	sudo rmmod $(MODULE)
	dmesg | tail -n 20
