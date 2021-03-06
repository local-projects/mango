# ---------------------------------------------------------------------------
# mango makefile
#
# Copyright (C) 2012-2017 Twilight 3D Finland Oy Ltd.
# ---------------------------------------------------------------------------

# ---------------------------------------------------------------------------
# configuration
# ---------------------------------------------------------------------------

LIBNAME_MANGO  = mango
LIBNAME_OPENGL = mango-opengl
LIBNAME_VULKAN = mango-vulkan

INCLUDE_BASE = ../../include
SOURCE_BASE  = ../../source
OBJECTS_PATH  = objects

SOURCE_DIRS_MANGO  = mango/core \
                     mango/core/unix \
                     mango/filesystem \
                     mango/filesystem/unix \
                     mango/image \
                     mango/math \
                     mango/jpeg \
                     external/zstd/common \
                     external/zstd/compress \
                     external/zstd/decompress \
                     external/lzfse \
                     external/bc \
                     external/google \
                     external/unrar \
                     external/miniz \
                     external/lz4 \
                     external/lzo \
                     external/bzip2 \
                     external/aes
SOURCE_DIRS_OPENGL = mango/opengl
SOURCE_DIRS_VULKAN = mango/vulkan

# language versions
CC_STD = -std=c11
CPP_STD = -std=c++14

# common compiler options (LLVM/CLANG/GCC)
OPTIONS       = -c -Wall -O3 -ffast-math
OPTIONS_GCC   = -ftree-vectorize
#OPTIONS_X86   = -msse4
OPTIONS_X86   = -mavx
#OPTIONS_X86 = -mavx512dq -mavx512vl -mavx512bw

OPTIONS_X86   += -maes

# linker options after objects (gcc 4.9 workaround)
LINK_POST     =

#NEON          = -mfpu=neon
NEON          = -mfpu=neon-fp16 -mfp16-format=ieee

OS   = $(shell uname)
ARCH = $(shell uname -m)

# define one of these to force cross compilation
#ARCH = ppc64le
#ARCH = mips64
#ARCH = aarch64

# ---------------------------------------------------------------------------
# Linux
# ---------------------------------------------------------------------------

ifeq (Linux, $(OS))

  SOURCE_DIRS_OPENGL += mango/gui/xlib
  SOURCE_DIRS_OPENGL += mango/opengl/glx

  SOURCE_DIRS_VULKAN += mango/gui/xcb

  LIBRARY_MANGO  = lib$(LIBNAME_MANGO).so
  LIBRARY_OPENGL = lib$(LIBNAME_OPENGL).so
  LIBRARY_VULKAN = lib$(LIBNAME_VULKAN).so
  
  CLEAN    = rm -fr *.so $(OBJECTS_PATH)
  INSTALL  = cp *.so /usr/lib ; ldconfig ; rm -rf /usr/include/mango ; cp -r $(INCLUDE_BASE)/mango/ /usr/include/mango/
  LINK_POST += -lpthread -ldl

  ###############################################################################
  #
  # Intel x86-64
  #
  ###############################################################################

  ifeq (x86_64, $(ARCH))
    PLATFORM = x86-64
    CC  = gcc $(OPTIONS) $(OPTIONS_GCC) $(OPTIONS_X86) -fPIC $(CC_STD)
    CPP = g++ $(OPTIONS) $(OPTIONS_GCC) $(OPTIONS_X86) -fPIC -ansi $(CPP_STD)
    LINK_MANGO  = g++ -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = g++ -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = g++ -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

  ###############################################################################
  #
  # Intel x86 32 bit
  #
  ###############################################################################

  ifeq (i686, $(ARCH))
    PLATFORM = x86
    CC  = gcc $(OPTIONS) $(OPTIONS_GCC) $(OPTIONS_X86) -g $(CC_STD)
    CPP = g++ $(OPTIONS) $(OPTIONS_GCC) $(OPTIONS_X86) -g -ansi $(CPP_STD)
    LINK_MANGO  = g++ -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = g++ -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = g++ -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

  ###############################################################################
  #
  # ARM (Tested on Raspberry Pi)
  #
  ###############################################################################

  ifeq (armv6l, $(ARCH))
    PLATFORM = armv6l
    CC  = gcc $(OPTIONS) $(OPTIONS_GCC) -g -mfpu=vfp -mfloat-abi=hard
    CPP = g++ $(OPTIONS) $(OPTIONS_GCC) -g -mfpu=vfp -mfloat-abi=hard -ansi -std=c++0x
    LINK_MANGO  = g++ -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = g++ -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = g++ -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

  ###############################################################################
  #
  # ARM (Tested on ODROID-U3, Raspberry Pi 3)
  #
  ###############################################################################

  ifeq (armv7l, $(ARCH))
    PLATFORM = armv7l
    CC  = gcc $(OPTIONS) $(OPTIONS_GCC) -g -marm -mcpu=cortex-a9 -mtune=cortex-a9 $(NEON) -mfloat-abi=hard -funsafe-math-optimizations -fPIC $(CC_STD)
    CPP = g++ $(OPTIONS) $(OPTIONS_GCC) -g -marm -mcpu=cortex-a9 -mtune=cortex-a9 $(NEON) -mfloat-abi=hard -funsafe-math-optimizations -fpermissive -fPIC -ansi $(CPP_STD)
    LINK_MANGO  = g++ -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = g++ -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = g++ -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

  ###############################################################################
  #
  # PowerPC
  #
  ###############################################################################

  ifeq (ppc64le, $(ARCH))
    PLATFORM = ppc64le
    CC  = powerpc64le-linux-gnu-gcc-5 $(OPTIONS) $(OPTIONS_GCC) -g -mcpu=power8 -funsafe-math-optimizations -fPIC $(CC_STD)
    CPP = powerpc64le-linux-gnu-g++-5 $(OPTIONS) $(OPTIONS_GCC) -g -mcpu=power8 -funsafe-math-optimizations -fpermissive -fPIC -ansi $(CPP_STD)
    LINK_MANGO  = powerpc64le-linux-gnu-g++-5 -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = powerpc64le-linux-gnu-g++-5 -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = powerpc64le-linux-gnu-g++-5 -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

  ###############################################################################
  #
  # MIPS
  #
  ###############################################################################

  ifeq (mips64, $(ARCH))
    PLATFORM = mips64
    CC  = mips64-linux-gnuabi64-gcc-5 $(OPTIONS) $(OPTIONS_GCC) -g -funsafe-math-optimizations -fPIC $(CC_STD)
    CPP = mips64-linux-gnuabi64-g++-5 $(OPTIONS) $(OPTIONS_GCC) -g -funsafe-math-optimizations -fpermissive -fPIC -ansi $(CPP_STD)
    CC += -mips64r5 -mfp64
    CPP += -mips64r5 -mfp64
    LINK_MANGO  = mips64-linux-gnuabi64-g++-5 -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = mips64-linux-gnuabi64-g++-5 -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = mips64-linux-gnuabi64-g++-5 -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

  ###############################################################################
  #
  # ARM64
  #
  ###############################################################################

  ifeq (aarch64, $(ARCH))
    PLATFORM = arm64
    CC  = aarch64-linux-gnu-gcc-5 $(OPTIONS) $(OPTIONS_GCC) -g -funsafe-math-optimizations -fPIC $(CC_STD)
    CPP = aarch64-linux-gnu-g++-5 $(OPTIONS) $(OPTIONS_GCC) -g -funsafe-math-optimizations -fpermissive -fPIC -ansi $(CPP_STD)
    LINK_MANGO  = aarch64-linux-gnu-g++-5 -s -shared -Wl,-soname,$(LIBRARY_MANGO) -o $(LIBRARY_MANGO)
    LINK_OPENGL = aarch64-linux-gnu-g++-5 -s -shared -Wl,-soname,$(LIBRARY_OPENGL) -o $(LIBRARY_OPENGL)
    LINK_VULKAN = aarch64-linux-gnu-g++-5 -s -shared -Wl,-soname,$(LIBRARY_VULKAN) -o $(LIBRARY_VULKAN)
  endif

endif

# ---------------------------------------------------------------------------
# macOS
# ---------------------------------------------------------------------------

ifeq (Darwin, $(OS))

  SOURCE_DIRS_OPENGL += mango/opengl/cocoa

  # hack to create /usr/local (Xcode requires them and they don't exist by default)
  LOCAL    = mkdir -p /usr/local/lib ; mkdir -p /usr/local/include

  LIBRARY_MANGO  = lib$(LIBNAME_MANGO).dylib
  LIBRARY_OPENGL = lib$(LIBNAME_OPENGL).dylib
  LIBRARY_VULKAN =
  PLATFORM = x86-64
  CC  = clang $(OPTIONS) $(OPTIONS_X86) $(CC_STD) -mmacosx-version-min=10.7
  CPP = clang++ $(OPTIONS) $(OPTIONS_X86) $(CPP_STD) -mmacosx-version-min=10.7 $(STD) -stdlib=libc++
  LINK_MANGO  = ld -o $(LIBRARY_MANGO)  -dylib -undefined dynamic_lookup -macosx_version_min 10.7
  LINK_OPENGL = ld -o $(LIBRARY_OPENGL) -dylib -undefined dynamic_lookup -macosx_version_min 10.7
  LINK_VULKAN = ld -o $(LIBRARY_VULKAN) -dylib -undefined dynamic_lookup -macosx_version_min 10.7
  INSTALL  = $(LOCAL) ; cp *.dylib /usr/local/lib ; cp -r $(INCLUDE_BASE)/mango/ /usr/local/include/mango/
  CLEAN    = rm -fr $(OBJECTS_PATH) *.dylib so_locations

endif

# ---------------------------------------------------------------------------
# objects
# ---------------------------------------------------------------------------

# mango

OBJECTS_MANGO += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.cpp,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_MANGO), $(wildcard $(SOURCE_BASE)/$(dir)/*.cpp)))))

OBJECTS_MANGO += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.c,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_MANGO), $(wildcard $(SOURCE_BASE)/$(dir)/*.c)))))

OBJECTS_MANGO += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.mm,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_MANGO), $(wildcard $(SOURCE_BASE)/$(dir)/*.mm)))))

# opengl

OBJECTS_OPENGL += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.cpp,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_OPENGL), $(wildcard $(SOURCE_BASE)/$(dir)/*.cpp)))))

OBJECTS_OPENGL += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.c,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_OPENGL), $(wildcard $(SOURCE_BASE)/$(dir)/*.c)))))

OBJECTS_OPENGL += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.mm,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_OPENGL), $(wildcard $(SOURCE_BASE)/$(dir)/*.mm)))))

# vulkan

OBJECTS_VULKAN += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.cpp,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_VULKAN), $(wildcard $(SOURCE_BASE)/$(dir)/*.cpp)))))

OBJECTS_VULKAN += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.c,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_VULKAN), $(wildcard $(SOURCE_BASE)/$(dir)/*.c)))))

OBJECTS_VULKAN += $(addprefix $(OBJECTS_PATH)/, $(patsubst %.mm,%.o, \
    $(abspath $(foreach dir, $(SOURCE_DIRS_VULKAN), $(wildcard $(SOURCE_BASE)/$(dir)/*.mm)))))

# ---------------------------------------------------------------------------
# rules
# ---------------------------------------------------------------------------

all: $(LIBRARY_MANGO) $(LIBRARY_OPENGL) $(LIBRARY_VULKAN)

$(OBJECTS_PATH)/%.o: %.mm
	@echo [Compile OBJC] $<
	@-mkdir -p $(@D)
	@$(CPP) -I$(INCLUDE_BASE) $< -o $@

$(OBJECTS_PATH)/%.o: %.cpp
	@echo [Compile C++] $<
	@-mkdir -p $(@D)
	@$(CPP) -I$(INCLUDE_BASE) $< -o $@

$(OBJECTS_PATH)/%.o: %.c
	@echo [Compile C] $<
	@-mkdir -p $(@D)
	@$(CC) $< -o $@

$(LIBRARY_MANGO): $(OBJECTS_MANGO)
	@echo [Link $(PLATFORM)] $(LIBRARY_MANGO)
	@$(LINK_MANGO) $(OBJECTS_MANGO) $(LINK_POST)

$(LIBRARY_OPENGL): $(OBJECTS_OPENGL)
	@echo [Link $(PLATFORM)] $(LIBRARY_OPENGL)
	@$(LINK_OPENGL) $(OBJECTS_OPENGL) $(LINK_POST)

$(LIBRARY_VULKAN): $(OBJECTS_VULKAN)
	@echo [Link $(PLATFORM)] $(LIBRARY_VULKAN)
	@$(LINK_VULKAN) $(OBJECTS_VULKAN) $(LINK_POST)

install:
	@echo [Install]
	@$(INSTALL)

clean:
	@echo [Clean]
	@$(CLEAN)
