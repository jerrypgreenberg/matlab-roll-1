# $Id$
#
# @Copyright@
# 
# 				Rocks(r)
# 		         www.rocksclusters.org
# 		         version 5.6 (Emerald Boa)
# 		         version 6.1 (Emerald Boa)
# 
# Copyright (c) 2000 - 2013 The Regents of the University of California.
# All rights reserved.	
# 
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are
# met:
# 
# 1. Redistributions of source code must retain the above copyright
# notice, this list of conditions and the following disclaimer.
# 
# 2. Redistributions in binary form must reproduce the above copyright
# notice unmodified and in its entirety, this list of conditions and the
# following disclaimer in the documentation and/or other materials provided 
# with the distribution.
# 
# 3. All advertising and press materials, printed or electronic, mentioning
# features or use of this software must display the following acknowledgement: 
# 
# 	"This product includes software developed by the Rocks(r)
# 	Cluster Group at the San Diego Supercomputer Center at the
# 	University of California, San Diego and its contributors."
# 
# 4. Except as permitted for the purposes of acknowledgment in paragraph 3,
# neither the name or logo of this software nor the names of its
# authors may be used to endorse or promote products derived from this
# software without specific prior written permission.  The name of the
# software includes the following terms, and any derivatives thereof:
# "Rocks", "Rocks Clusters", and "Avalanche Installer".  For licensing of 
# the associated name, interested parties should contact Technology 
# Transfer & Intellectual Property Services, University of California, 
# San Diego, 9500 Gilman Drive, Mail Code 0910, La Jolla, CA 92093-0910, 
# Ph: (858) 534-5815, FAX: (858) 534-7345, E-MAIL:invent@ucsd.edu
# 
# THIS SOFTWARE IS PROVIDED BY THE REGENTS AND CONTRIBUTORS ``AS IS''
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO,
# THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
# PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE REGENTS OR CONTRIBUTORS
# BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR
# BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
# WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE
# OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN
# IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
# 
# @Copyright@
#
# $Log$

REDHAT.ROOT = $(shell readlink -f ${CURDIR}/../../)
PACKAGES=16

-include $(ROCKSROOT)/etc/Rules.mk
include Rules.mk
include pull.mk

FIK := $(shell cat fik.txt)

build: $(SRC_DIRS)
	cp installer_input $(SOURCE_DIR)
	patch -b $(SOURCE_DIR)/install patch-files/install.patch
	mkdir -p $(REDHAT.ROOT)/src/build-matlab
	perl -pi -e "s!ROLLINSTALL!$(REDHAT.ROOT)/src/build-matlab!" $(SOURCE_DIR)/installer_input
	perl -pi -e "s!FIK!$(FIK)!" $(SOURCE_DIR)/installer_input
	perl -pi -e "s!ROLLPATH!$(CURDIR)/../../src/matlab-license-files!" $(SOURCE_DIR)/installer_input
	( \
		export -n DISPLAY=; \
		cd $(SOURCE_DIR); \
		./install -inputFile installer_input ; \
		mkdir -p $(REDHAT.ROOT)/src/build-matlab/licenses; \
         )
	@echo "::: Package $(NAME) build complete :::"

install::
	mkdir -p $(REDHAT.ROOT)/src/build-matlab-modules
	( \
           packageCount=1; \
           mkdir  -p $(REDHAT.ROOT)/src/build-matlab_$$packageCount; \
           cd  $(REDHAT.ROOT)/src/build-matlab; \
           find . ! -type d -exec ls -l {} \; >$(REDHAT.ROOT)/src/$$$$.tmp; \
           find . \( -name .\* -a ! -type d \) -exec ls -l {} \; >>$(REDHAT.ROOT)/src/$$$$.tmp; \
           sum=0; \
           total=`du -b -s|awk '{print $$1}'`; \
           maxPackageSize=$$(( $$total / $(PACKAGES) )); \
           echo "MAX SIZE=$$maxPackageSize"; \
           exec <$(REDHAT.ROOT)/src/$$$$.tmp; \
           cat /dev/null > $(REDHAT.ROOT)/src/.files; \
           while read line; do \
               fileSize=`echo $$line |awk '{print $$5}'`; \
               fileName=`echo $$line |awk '{print $$9}'`; \
               sum=$$(( $$sum + $$fileSize )); \
               if test $$sum -ge $$maxPackageSize; then \
                  sum=$$(( $$sum - $$fileSize )); \
                  echo "NEW PACKAGE: OLD SUM $$sum"; \
                  tar cvf $(REDHAT.ROOT)/src/package.tar `cat $(REDHAT.ROOT)/src/.files`; \
                  cd $(REDHAT.ROOT)/src/build-matlab_$$packageCount; \
                  tar xvf $(REDHAT.ROOT)/src/package.tar; \
                  cd $(REDHAT.ROOT)/src/build-matlab; \
                  sum=$$fileSize; \
                  packageCount=$$(( $$packageCount + 1 )); \
                  mkdir  -p $(REDHAT.ROOT)/src/build-matlab_$$packageCount; \
                  cat /dev/null > $(REDHAT.ROOT)/src/.files; \
               fi; \
               echo $$fileName >> $(REDHAT.ROOT)/src/.files; \
           done; \
           tar cvf $(REDHAT.ROOT)/src/package.tar `cat $(REDHAT.ROOT)/src/.files`; \
           cd $(REDHAT.ROOT)/src/build-matlab_$$packageCount; \
           tar xvf $(REDHAT.ROOT)/src/package.tar; \
           cd $(REDHAT.ROOT)/src/build-matlab; \
           mkdir -p $(REDHAT.ROOT)/src/build-matlab_$$packageCount/appdata/products; \
           mkdir -p $(REDHAT.ROOT)/src/build-matlab_$$packageCount/appdata/components; \
           cp -r -d appdata/products/* $(REDHAT.ROOT)/src/build-matlab_$$packageCount/appdata/products; \
           cp -r -d appdata/components/* $(REDHAT.ROOT)/src/build-matlab_$$packageCount/appdata/components; \
           rm -f $(REDHAT.ROOT)/src/$$$$.tmp; \
           rm -f $(REDHAT.ROOT)/src/.files; \
           rm -f $(REDHAT.ROOT)/src/package.tar \
        )
	cat matlab.module | perl -pi -e "s!MODULEROOT!/opt/modulefiles/applications/matlab!g;s!VERSION!$(VERSION)!g" \
          > $(REDHAT.ROOT)/src/build-matlab-modules/$(NAME).module
	@echo "::: Package $(NAME) install complete :::"


clean::
	rm -rf $(SOURCE_DIR)
