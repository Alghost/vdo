#
# Copyright (c) 2017 Red Hat, Inc.
#
# This program is free software; you can redistribute it and/or
# modify it under the terms of the GNU General Public License
# as published by the Free Software Foundation; either version 2
# of the License, or (at your option) any later version.
# 
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# 
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA
# 02110-1301, USA. 
#
# $Id: //eng/vdo-releases/magnesium/src/packaging/src-dist/user/examples/nagios/Makefile#1 $

INSTALLFILES=nagios_check_albserver.pl		\
	nagios_check_vdostats_logicalSpace.pl	\
	nagios_check_vdostats_physicalSpace.pl	\
	nagios_check_vdostats_savingPercent.pl


INSTALLDIR=$(DESTDIR)/$(defaultdocdir)/$(name)/examples/nagios

.PHONY: all clean install
all:;

clean:;

install:
	install -d $(INSTALLDIR)
	for i in $(INSTALLFILES); do \
	  install $$i $(INSTALLDIR); \
	done
