#****************************************************************************
#* Makefile for vte
#****************************************************************************
SCRIPTS_DIR:=$(abspath $(dir $(lastword $(MAKEFILE_LIST))))
PROJECT_DIR:=$(abspath $(SCRIPTS_DIR)/..)
PACKAGES_DIR:=$(PROJECT_DIR)/packages
BUILD_DIR:=$(PROJECT_DIR)/build
ETC_DIR:=$(PROJECT_DIR)/etc

EDAPACK_BUILD_URL=https://github.com/EDAPack/edapack-build

include $(PROJECT_DIR)/etc/package.info
name:=$(name)
version:=$(version)

ifeq (true,$(VERBOSE))
Q=
WGET=wget
UNTARGZ=tar xvzf
else
Q=@
WGET=wget -q
UNTARGZ=tar xzf
endif

uname_o:=$(shell uname -o)

ifeq (Linux,$(uname_o))
platform=linux_x86_64
else
ifeq (GNU/Linux,$(uname_o))
platform=linux_x86_64
else
platform=unknown
endif
endif

# TODO: Package TGZ and URL

PREFIX=$(BUILD_DIR)/$(name)/$(name)
PACKAGE=$(BUILD_DIR)/$(name)-any-$(version).tar.gz

VTE_VERSION:=$(version)
VTE_DIR=vte-$(VTE_VERSION)
VTE_TGZ=$(VTE_DIR).tar.gz
VTE_URL=https://github.com/fvutils/vte/releases/download/$(VTE_VERSION)/$(VTE_TGZ)

all : $(PACKAGE)


$(PACKAGE) : $(PACKAGES_DIR)/$(VTE_TGZ) \
		$(PACKAGES_DIR)/install.py \
		$(ETC_DIR)/modulefile \
		$(ETC_DIR)/package.info
	$(Q)rm -rf $(PREFiX)
	$(Q)mkdir -p $(PREFIX)
	$(Q)cd $(PREFIX) ; \
		$(UNTARGZ) $(PACKAGES_DIR)/$(VTE_TGZ) --strip-components=1
	$(Q)cd $(PREFIX)/bin ; patch vte < $(SCRIPTS_DIR)/vte.patch
	$(Q)chmod +x $(PREFIX)/bin/vte
	$(Q)mkdir -p $(PREFIX)/etc
	$(Q)cp $(PACKAGES_DIR)/install.py $(PREFIX)/etc
	$(Q)sed -e "s/{{version}}/$(version)/g" \
		-e "s/{{name}}/$(name)/g" \
		$(ETC_DIR)/modulefile  > $(PREFIX)/etc/modulefile
	$(Q)cp $(ETC_DIR)/package.info $(PREFIX)/etc
	$(Q)cd $(PREFIX) ; tar czf $@ *


$(BUILD_DIR)/$(name).d : # TODO: Package TGZ
	$(Q)rm -rf $(BUILD_DIR)/$(name)
	$(Q)mkdir -p $(BUILD_DIR)/$(name)
	$(Q)echo "TODO: setup $(name) build" ; exit 1
	$(Q)touch $@

# TODO: Fetch package TGZ
$(PACKAGES_DIR)/$(VTE_TGZ) : 
	$(Q)mkdir -p $(PACKAGES_DIR)
	$(Q)$(WGET) -O $@ $(VTE_URL)

$(PACKAGES_DIR)/install.py : 
	$(Q)mkdir -p $(PACKAGES_DIR)
	$(Q)$(WGET) -O $@ $(EDAPACK_BUILD_URL)/raw/master/scripts/install.py

release : $(PACKAGE) $(PACKAGES_DIR)/upload.py
	$(Q)python3 $(PACKAGES_DIR)/upload.py \
		--org EDAPack --repo $(name) \
		--key $(GITHUB_API_TOKEN) --version $(version) $(PACKAGE)

$(PACKAGES_DIR)/upload.py :
	$(Q)mkdir -p $(PACKAGES_DIR)
	$(Q)$(WGET) -O $@ $(EDAPACK_BUILD_URL)/raw/master/scripts/upload.py

clean : 
	$(Q)rm -rf $(BUILD_DIR)

clean-all : 
	$(Q)rm -rf $(BUILD_DIR) $(PACKAGES_DIR)
