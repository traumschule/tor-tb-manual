@YELP_HELP_RULES@

HELP_ID = tor-browser-user-manual
HELP_FILES = $(shell cd C && git ls-files '*.page')
HELP_MEDIA = $(shell cd C && git ls-files 'media')
HELP_PLATFORMS = windows macosx linux
HELP_LINGUAS = ar de es fa fr it ko nl pl pt_PT ru tr vi zh_CN

.PHONY: html
html: all
	set -e && \
		for lc in C $(HELP_LINGUAS); do \
			for media in $(HELP_MEDIA); do \
				if ! [ -f $$lc/$$media ]; then \
					mkdir -p $$(dirname $$lc/$$media); \
					ln -nsf ../../C/$$media $$lc/$$media; \
				fi; \
			done; \
			for platform in $(HELP_PLATFORMS); do \
				mkdir -p html/$$platform/$$lc; \
				yelp-build html -x platform-$$platform.xslt \
						-o html/$$platform/$$lc $$lc; \
			done; \
		done;

clean:
	set -e && \
		for lc in C $(HELP_LINGUAS); do \
			for media in $(HELP_MEDIA); do \
				if [ -L $$lc/$$media ]; then \
					rm $$lc/$$media; \
				fi; \
				rmdir --ignore-fail-on-non-empty $$(dirname $$lc/$$media); \
			done; \
		done;

distclean-local:
	rm -rf html
