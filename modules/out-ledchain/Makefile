# name of the target binary
TARGET_EXEC ?= out-ledchain.so

# where the objects and source files go
BUILD_DIR ?= ../../build/out_modules
SRC_DIRS ?= ./src

# input files
SRCS := $(shell find $(SRC_DIRS) -name *.cpp -or -name *.c -or -name *.s)
OBJS := $(SRCS:%=$(BUILD_DIR)/%.o)
DEPS := $(OBJS:.o=.d)

# libraries to link against
LIBS := stdc++ glog
LIBS_DIRS +=

# link against libkmod on linux
UNAME_S := $(shell uname -s)

ifeq ($(UNAME_S),Linux)
	LIBS += kmod
endif

LIBS_FLAGS := $(addprefix -L,$(LIBS_DIRS)) $(addprefix -l,$(LIBS))

# directories to search for includes
INC_DIRS += $(shell find $(SRC_DIRS) -type d) ../../include ../../libs/inih

INC_FLAGS := $(addprefix -I,$(INC_DIRS))

# flags for the C and C++ compiler
CPPFLAGS ?= -fPIC -g -fvisibility=hidden $(INC_FLAGS) -MMD -MP -std=c++1z -fno-strict-aliasing
CFLAGS ?= -fPIC -g -fvisibility=hidden -fno-strict-aliasing

# flags for the linker
LDFLAGS += -g -fPIC -shared $(LIBS_FLAGS)

# git version
GIT_HASH=`git rev-parse HEAD`
COMPILE_TIME=`date -u +'%Y-%m-%d %H:%M:%S UTC'`
GIT_BRANCH=`git branch | grep "^\*" | sed 's/^..//'`
export VERSION_FLAGS=-DGIT_HASH="\"$(GIT_HASH)\"" -DCOMPILE_TIME="\"$(COMPILE_TIME)\"" -DGIT_BRANCH="\"$(GIT_BRANCH)\"" -DVERSION="\"0.1.0\""

ifeq ($(BUILD),RELEASE)
	CFLAGS += -O2
	CPPFLAGS += -O2
else
	CFLAGS += -Og -DDEBUG="1"
	CPPFLAGS += -Og -DDEBUG="1"
endif

# all target
all: $(BUILD_DIR)/$(TARGET_EXEC)

# build the main shared library
$(BUILD_DIR)/$(TARGET_EXEC): $(OBJS)
	$(CC) $(OBJS) -o $@ $(LDFLAGS)

# assembly
$(BUILD_DIR)/%.s.o: %.s
	$(MKDIR_P) $(dir $@)
	$(AS) $(ASFLAGS) -c $< -o $@

# c source
$(BUILD_DIR)/%.c.o: %.c
	$(MKDIR_P) $(dir $@)
	$(CC) $(CFLAGS) $(VERSION_FLAGS) -c $< -o $@

# c++ source
$(BUILD_DIR)/%.cpp.o: %.cpp
	$(MKDIR_P) $(dir $@)
	$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(VERSION_FLAGS) -c $< -o $@


.PHONY: clean

clean:
	$(RM) -r $(BUILD_DIR)

-include $(DEPS)

MKDIR_P ?= mkdir -p
