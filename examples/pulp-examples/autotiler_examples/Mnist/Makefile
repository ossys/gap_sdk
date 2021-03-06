# Copyright (C) 2017 GreenWaves Technologies
# All rights reserved.

# This software may be modified and distributed under the terms
# of the BSD license.  See the LICENSE file for details.

pulpChip = GAP
PULP_APP = Mnist
TILER_PATH = $(GAP_SDK_HOME)/tools/autotiler
TILER_GENERATOR_PATH=$(TILER_PATH)/autotiler_generator
PULP_APP_SRCS = Mnist.c MnistKernels.c MnistKernelsInit.c $(TILER_GENERATOR_PATH)/CnnStdModel/CNN_BasicKernels.c ImgIO.c
PULP_APP_SRCS += $(TILER_GENERATOR_PATH)/CnnStdModel/CNN_HwCE.c

pulpFc=1
RM=rm -f


#To activeate Hardware CE SET -DRT_HAS_HWCE=1
USE_HARDWARE_CE = -DRT_HAS_HWCE=1

PULP_CFLAGS += -O3 $(USE_HARDWARE_CE)
PULP_CFLAGS += -mno-memcpy -fno-tree-loop-distribute-patterns  -fdata-sections -ffunction-sections
PULP_LDFLAGS +=  -flto -Wl,--gc-sections
# The generated code outputs a maybe-uninitialized error which is rather difficult to suppress
# in a clean way.
PULP_CFLAGS += -Wall -Wno-maybe-uninitialized -Wno-unused-but-set-variable
PULP_CFLAGS += -I$(TILER_PATH)/StdTypes -I$(TILER_GENERATOR_PATH)/CnnStdModel

# The double colon allows us to force this to occur before the imported all target
# Link model generation to all step
all:: model

# Build the code generator
GenTile:
	gcc -o GenMnist $(USE_HARDWARE_CE) -I$(TILER_PATH)/GenTiling -I$(TILER_PATH)/StdTypes/ -I$(TILER_GENERATOR_PATH)/CnnStdModel MnistGenerator.c $(TILER_GENERATOR_PATH)/CnnStdModel/CNN_Generator.c $(TILER_PATH)/GenTiling/LibTile.a

# Run the code generator
MnistKernels.c MnistKernelsInit.c: GenTile
	./GenMnist

model: MnistKernels.c MnistKernelsInit.c

clean::
	$(RM) GenMnist MnistKernels.* MnistKernelsInit.*

.PHONY: model clean

include $(GAP_SDK_HOME)/tools/rules/pulp_rules.mk

