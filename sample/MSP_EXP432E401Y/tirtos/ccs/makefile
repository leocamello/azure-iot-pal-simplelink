#
#  Copyright (c) 2018-2020, Texas Instruments Incorporated
#  All rights reserved.
#
#  Redistribution and use in source and binary forms, with or without
#  modification, are permitted provided that the following conditions
#  are met:
#
#  *  Redistributions of source code must retain the above copyright
#     notice, this list of conditions and the following disclaimer.
#
#  *  Redistributions in binary form must reproduce the above copyright
#     notice, this list of conditions and the following disclaimer in the
#     documentation and/or other materials provided with the distribution.
#
#  *  Neither the name of Texas Instruments Incorporated nor the names of
#     its contributors may be used to endorse or promote products derived
#     from this software without specific prior written permission.
#
#  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
#  AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO,
#  THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
#  PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR
#  CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
#  EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
#  PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
#  OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
#  WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
#  OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE,
#  EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
#

TREE_ROOT = ../../../..
include $(TREE_ROOT)/build_all/products.mak
include makedefs

CFLAGS :=  -I. -I../.. -I../../.. \
    -DUSE_BALTIMORE_CERT \
    -I$(TREE_ROOT)/sdk/inc/ \
    -I$(TREE_ROOT)/sdk/certs/ -I$(TREE_ROOT)/sdk/c-utility/inc/ \
    -I$(TREE_ROOT)/sdk/c-utility/deps/azure-macro-utils-c/inc \
    -I$(TREE_ROOT)/sdk/c-utility/deps/umock-c/inc \
    -I$(TREE_ROOT)/sdk/c-utility/inc/azure_c_shared_utility \
    -I$(TREE_ROOT)/sdk/iothub_client/inc -I$(TREE_ROOT)/sdk/serializer/inc \
    -g $(CFLAGS) --cmd_file=$(CONFIGPKG)/compiler.opt

LIBS =  $(TREE_ROOT)/build_all/sdk/lib/ccs/m4f/common_sl_release.a \
    $(TREE_ROOT)/build_all/sdk/lib/ccs/m4f/iotclient_sl_release.a \
    $(TREE_ROOT)/build_all/sdk/lib/ccs/m4f/serializer_sl_release.a \
    $(TREE_ROOT)/build_all/pal/lib/ccs/m4f/pal_sl_release.a

VPATH = ..:../..:../../..:$(TREE_ROOT)/sdk/certs

%.o : %.c

%.o : %.c ti_drivers_config.h $(CONFIGPKG)/compiler.opt
	@echo $(CC) $(CFLAGS) -c $<
	@$(CC) $(CFLAGS) -c $<

all: simplesample_http.out

$(CONFIGPKG)/linker.cmd $(CONFIGPKG)/compiler.opt:
	@ echo
	$(error "$(CONFIGPKG) is not built. You can build it by issuing \
            $(MAKE) in $(CONFIGPKG).")
	@ echo

.INTERMEDIATE: syscfg
ti_drivers_config.c ti_ndk_config.c ti_net_config.c ti_drivers_config.h: syscfg
	@ echo generation complete

syscfg: ../azure_iot.syscfg
	@ echo Generating configuration files...
	@ $(SYSCONFIG_TOOL) --product $(SIMPLELINK_MSP432E4_SDK_INSTALL_DIR)/.metadata/product.json --board /ti/boards/MSP_EXP432E401Y --output $(@D) $<

simplesample_http.out: ti_drivers_config.o ti_ndk_config.o ti_net_config.o main_tirtos.o certs.o startsntp.o simplesample_http.o network.o MSP_EXP432E401Y_TIRTOS.cmd $(CONFIGPKG)/linker.cmd $(LIBS)
	@echo building $@ ..
	@$(LD) -o $@ $^ -x -m $@.map $(LFLAGS)

clean:
	@echo cleaning ..
	@ $(RM) *.o *.out *.map *.rov.xs
	@ $(RM) ti_net_config.c ti_ndk_config.c ti_drivers_config.c ti_drivers_config.h
	@ $(RMDIR)
