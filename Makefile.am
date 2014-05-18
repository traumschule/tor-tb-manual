@YELP_HELP_RULES@

# XXX: turn into ./configure option
MAL2LATEX = ../mal2latex
HELP_ID = tor-browser-user-manual
HELP_FILES = $(shell cd C && git ls-files '*.page')
HELP_MEDIA = $(shell cd C && git ls-files 'media')
HELP_PLATFORMS = windows macosx linux
HELP_LINGUAS = @TOR_BROWSER_BUNDLE_LOCALES@

.PHONY: html
html: all media-symlinks.stamp
	set -e && \
		for lc in C $(HELP_LINGUAS); do \
			for platform in $(HELP_PLATFORMS); do \
				mkdir -p html/$$platform/$$lc; \
				yelp-build html -x platform-$$platform.xslt \
						-o html/$$platform/$$lc $$lc; \
			done; \
		done

.PHONY: pdf
pdf: all media-symlinks.stamp
	set -e && \
		for lc in C $(HELP_LINGUAS); do \
			for platform in $(HELP_PLATFORMS); do \
				PDF_DIR=pdf/$$platform/$$lc; \
				LATEX_FILE=$(HELP_ID)-$$lc.latex; \
				mkdir -p $$PDF_DIR; \
				yelp-build cache -o $$PDF_DIR/index.cache $$lc; \
				xsltproc --xinclude \
					$(MAL2LATEX)/mal2latex.xsl $$PDF_DIR/index.cache \
					> $$PDF_DIR/$$LATEX_FILE; \
				(cd $$PDF_DIR && lualatex $$LATEX_FILE); \
			done; \
		done;

media-symlinks.stamp: all
	set -e && \
		for lc in C $(HELP_LINGUAS); do \
			for media in $(HELP_MEDIA); do \
				if ! [ -f $$lc/$$media ]; then \
					mkdir -p $$(dirname $$lc/$$media); \
					ln -nsf ../../C/$$media $$lc/$$media; \
				fi; \
			done; \
		done
	touch media-symlinks.stamp

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
	rm -rf pdf
