# TODO: remove redundancies!

# XXX this does not work with Automake!!!
#
# In fact the very concept is entirely antithetical to Automake.
#
# SUBDIRS are explicitly a listing of all the directories that make
# must recurse into BEFORE processing the current directory.
#
# These python scripts must be moved into a sub-directory, and _only_
# executed IF they need to be, and all the nut-scanner sources need
# to be moved out of a sub-directory into this directory.
#
# Anyway, for the time being, we force build in ./ before nut-scanner,
# to have nutscan-{usb,snmp}.h built before going into the nut-scanner
# sub-directory
SUBDIRS = . nut-scanner

EXTRA_DIST = nut-usbinfo.pl nut-recorder.sh nut-ddl-dump.sh \
  gitlog2changelog.py nut-snmpinfo.py driver-list-format.sh

all: nut-scanner-deps 

# XXX these rules are all bogus!  They cause un-named target files to
# always be rebuilt!  None of that is ever the right way to use make,
# and especially not Automake.  Explicit filenames and their exact
# dependencies need to be properly listed.
nut-scanner-deps: nut-scanner-deps-snmpinfo nut-scanner-deps-usb

nut-scanner-deps-snmpinfo:
	@if python -c 1; then \
		$(MAKE) $(top_builddir)/tools/nut-scanner/nutscan-snmp.c; \
	else \
		echo "----------------------------------------------------------------------"; \
		echo "Warning: Python is not available."; \
		echo "Skipping the SNMP helper files regeneration."; \
		echo "----------------------------------------------------------------------"; \
	fi

$(top_builddir)/tools/nut-scanner/nutscan-snmp.c: $(top_srcdir)/drivers/*-mib.c
	@echo "Regenerating the SNMP helper files."; \
		$(top_srcdir)/tools/nut-snmpinfo.py

nut-scanner-deps-usb:
	@if perl -e 1; then \
		$(MAKE) $(top_builddir)/tools/nut-scanner/nutscan-usb.h; \
	else \
		echo "----------------------------------------------------------------------"; \
		echo "Warning: Perl is not available."; \
		echo "Skipping the USB helper files regeneration."; \
		echo "----------------------------------------------------------------------"; \
	fi

$(top_builddir)/tools/nut-scanner/nutscan-usb.h: $(top_srcdir)/drivers/*-hid.c $(top_srcdir)/drivers/*usb*.c $(top_srcdir)/drivers/nutdrv_qx.c
	@echo "Regenerating the USB helper files."; \
		$(top_srcdir)/tools/nut-usbinfo.pl

# call the USB info script upon "make dist", and if Perl is present
# call the SNMP info script upon "make dist", and if Python is present
# and call both for building nut-scanner
# Also ensure that data/driver.list is well formatted
dist-hook:
	@if python -c 1; then \
		echo "Regenerating the SNMP helper files."; \
		$(distdir)/nut-snmpinfo.py; \
	else \
		echo "----------------------------------------------------------------------"; \
		echo "Warning: Python is not available."; \
		echo "Skipping the SNMP helper files regeneration."; \
		echo "----------------------------------------------------------------------"; \
	fi

	@if perl -e 1; then \
		echo "Regenerating the USB helper files."; \
		$(distdir)/nut-usbinfo.pl; \
	else \
		echo "----------------------------------------------------------------------"; \
		echo "Warning: Perl is not available."; \
		echo "Skipping the USB helper files regeneration."; \
		echo "----------------------------------------------------------------------"; \
	fi

	@$(distdir)/driver-list-format.sh;

.PHONY: nut-scanner-deps nut-scanner-snmp-deps nut-scanner-usb-deps
