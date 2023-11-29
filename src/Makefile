SYSROOT=$(HOME)/tmp/root/usr/local/riscv64-carrier
CHERI_SDK=$(HOME)/cheri/output/sdk

CC=$(CHERI_SDK)/bin/riscv64-unknown-freebsd-clang
CFLAGS=--target=riscv64-carrier -march=rv64gcxcheri -mabi=lp64d \
	--sysroot=$(HOME)/cheri/output/sdk/sysroot-riscv64-hybrid \
	-fPIC -fPIE -mno-relax -static\
	-U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=0  -fno-stack-protector \
	-fno-common \
	-ffreestanding -nostdlib -nostdinc \
	-I $(SYSROOT)/include \
	-I /home/chu/cheri/llvm-project/clang/lib/Headers
LD=$(CHERI_SDK)/bin/riscv64-unknown-freebsd-ld
AS=$(CHERI_SDK)/bin/riscv64-unknown-freebsd-cc -x assembler-with-cpp
AR=$(CHERI_SDK)/bin/riscv64-unknown-freebsd-ar
OC=$(CHERI_SDK)/bin/riscv64-unknown-freebsd-objcopy
OD=$(CHERI_SDK)/bin/riscv64-unknown-freebsd-objdump
RM=rm -fr

LDFLAGS=-fuse-ld=lld -mno-relax

INCDIRS		:= -I .
SRCDIRS		:= . default

SFILES		:= $(foreach dir, $(SRCDIRS), $(wildcard $(dir)/*.S))
CFILES		:= $(foreach dir, $(SRCDIRS), $(wildcard $(dir)/*.c))
CPPFILES	:= $(foreach dir, $(SRCDIRS), $(wildcard $(dir)/*.cpp))

SDEPS		:= $(patsubst %, %, $(SFILES:.S=.o.d))
CDEPS		:= $(patsubst %, %, $(CFILES:.c=.o.d))
CPPDEPS		:= $(patsubst %, %, $(CPPFILES:.cpp=.o.d))
DEPS		:= $(SDEPS) $(CDEPS) $(CPPDEPS)

SOBJS		:= $(patsubst %, %, $(SFILES:.S=.o))
COBJS		:= $(patsubst %, %, $(CFILES:.c=.o))
CPPOBJS		:= $(patsubst %, %, $(CPPFILES:.cpp=.o)) 
OBJS		:= $(SOBJS) $(COBJS) $(CPPOBJS)

NAME		:= libonnx.a

.PHONY: all clean

all : $(NAME)

$(NAME) : $(OBJS)
	@echo [AR] Archiving $@
	@$(AR) -rcs $@ $(OBJS)

$(SOBJS) : %.o : %.S
	@echo [AS] $<
	@$(AS) $(ASFLAGS) -MD -MP -MF $@.d $(INCDIRS) -c $< -o $@

$(COBJS) : %.o : %.c
	@echo [CC] $<
	@$(CC) $(CFLAGS) -MD -MP -MF $@.d $(INCDIRS) -c $< -o $@

$(CPPOBJS) : %.o : %.cpp
	@echo [CXX] $<
	@$(CXX) $(CXXFLAGS) -MD -MP -MF $@.d $(INCDIRS) -c $< -o $@

clean:
	@$(RM) $(DEPS) $(OBJS) $(NAME)

sinclude $(DEPS)

