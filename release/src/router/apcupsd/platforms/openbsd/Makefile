topdir:=../..

SUBDIRS =
include $(topdir)/autoconf/targets.mak

all-install: install-openbsd
all-uninstall: uninstall-openbsd

install-openbsd:
	$(call DISTINST,OpenBSD)
	$(call MKDIR,/etc)
	$(call INSTPROG,744,apcupsd,/etc/rc.apcupsd)
	$(VV)-today="`date +%Y%m%d%H%M`"; \
	grep -q '# TAG_APCUPSD' /etc/rc.shutdown; \
	if [ $$? -ne 0 ]; then \
		echo "  PATCH $(DESTDIR)/etc/rc.shutdown"; \
		rm -f $(DESTDIR)/etc/rc.shutdown.$$today; \
		cp -p /etc/rc.shutdown $(DESTDIR)/etc/rc.shutdown.$$today; \
		( echo "# Do not remove the 'TAG_APCUPSD' text, below"; \
		  echo "if [ -f $(PWRFAILDIR)/powerfail ]; then			     # TAG_APCUPSD";\
		  echo "    powerdown=YES					     # TAG_APCUPSD"; \
		  echo "    echo ''						     # TAG_APCUPSD"; \
		  echo "    echo 'Please ensure that the UPS has powered off before' # TAG_APCUPSD"; \
		  echo "    echo 'rebooting.  Otherwise, the UPS may cut the power'  # TAG_APCUPSD"; \
		  echo "    echo 'during the reboot.'				     # TAG_APCUPSD"; \
		  echo "    echo ''						     # TAG_APCUPSD"; \
		  echo "fi							     # TAG_APCUPSD"; \
		) >> $(DESTDIR)/etc/rc.shutdown; \
	fi; \
	grep -q /etc/rc.apcupsd /etc/rc.local; \
	if [ $$? -ne 0 ]; then \
		echo "  PATCH $(DESTDIR)/etc/rc.local"; \
		rm -f $(DESTDIR)/etc/rc.local.$$today; \
		cp -p /etc/rc.local $(DESTDIR)/etc/rc.local.$$today; \
		( echo "# Start the UPS daemon. Do not remove the 'TAG_APCUPSD' text"; \
		  echo "# if [ -x /etc/rc.apcupsd ]; then     # TAG_APCUPSD"; \
		  echo "#	  /etc/rc.apcupsd start       # TAG_APCUPSD"; \
		  echo "# fi				      # TAG_APCUPSD"; \
		) >> $(DESTDIR)/etc/rc.local; \
		echo ""; \
		( echo "While /etc/rc.local has been patched to run apcupsd,";\
		echo "the commands are currently commented out.  You should"; \
		echo "examine the $(sysconfdir)/apcupsd.conf file to ensure"; \
		echo "that it is suitable for your site.  Then run"; \
		echo "/etc/rc.apcupsd manually to ensure sane operation."; \
		echo "Once you are satisfied, uncomment the appropriate"; \
		echo "lines in /etc/rc.local" ) | /usr/bin/fmt; \
	fi

uninstall-openbsd:
	$(call DISTUNINST,OpenBSD)
	-$(call UNINST,/etc/rc.apcupsd)
	$(VV)-today="`date +%Y%m%d%H%M`"; \
	for f in $(DESTDIR)/etc/rc.local $(DESTDIR)/etc/rc.shutdown; do \
		grep -q '# TAG_APCUPSD' $$f; \
		if [ $$? -eq 0 ]; then \
			echo "  PATCH $$f"; \
			rm -f $$f.$$today; \
			cp -p $$f $$f.$$today; \
			$(SED) -e '/TAG_APCUPSD/d;' \
				< $$f.$$today > $$f; \
			chmod 644 $$f; \
		fi; \
	done
