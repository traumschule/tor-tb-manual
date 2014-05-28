@YELP_HELP_RULES@

# XXX: turn into ./configure option
MAL2LATEX = ../mal2latex
# XXX: turn into ./configure option
TOR_AND_HTTPS = ../tor-and-https

HELP_ID = tor-browser-user-manual
HELP_FILES = $(shell cd $(srcdir)/C && git ls-files '*.page')
HELP_MEDIA = $(shell cd $(srcdir)/C && git ls-files 'media')
HELP_PLATFORMS = windows macosx linux
HELP_LINGUAS = en-US @TOR_BROWSER_BUNDLE_LOCALES@

.PHONY: html
html: all media-symlinks.stamp
	set -e && \
		for lc in $(HELP_LINGUAS); do \
			includes="$$(find include -maxdepth 1 -type f)" ; \
			for file in $$includes; do \
				mkdir -p $$(dirname $$file); \
				ln -nsf ../$$file $$lc/$$(basename $$file); \
			done; \
			for platform in $(HELP_PLATFORMS); do \
				mkdir -p html/$$platform/$$lc; \
				yelp-build html -x $(srcdir)/platform-$$platform.xslt \
						-o html/$$platform/$$lc $$lc; \
				for ttf in $$lc/media/*.ttf; do \
					cp --preserve=timestamps $$ttf html/$$platform/$$lc/media; \
				done; \
			done; \
		done

.PHONY: pdf
pdf: all media-symlinks.stamp
	set -e && \
		for lc in $(HELP_LINGUAS); do \
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

media-symlinks.stamp:
	set -e && \
		for lc in $(HELP_LINGUAS); do \
			for media in $(HELP_MEDIA); do \
				if ! [ -f $$lc/$$media ]; then \
					mkdir -p $$(dirname $$lc/$$media); \
					if [ -f $(srcdir)/$$lc/$$media ]; then \
						ln -nsf $(abs_srcdir)/$$lc/$$media $$lc/$$media; \
					else \
						ln -nsf $(abs_srcdir)/C/$$media $$lc/$$media; \
					fi; \
				fi; \
			done; \
		done
	touch media-symlinks.stamp

.PHONY: update-tor-and-https
update-tor-and-https:
	set -e && \
		for lc in C $(HELP_LINGUAS); do \
			for file in $$(find $(TOR_AND_HTTPS) \
					-wholename "*/$$(echo $$lc | sed -e 's/^\([^_-]*\).*/\1/')*/tor-and-https.svg"); do \
				cp $$file $$lc/media; \
				git add $$lc/media/$$(basename $$file); \
			done; \
			for ttf in $$(find $(TOR_AND_HTTPS) \
					-wholename "*/$$(echo $$lc | sed -e 's/^\([^_-]*\).*/\1/')*/*.ttf"); do \
				if ! [ -f "C/media/$$(basename $$ttf)" ]; then \
					cp "$$ttf" C/media; \
					git add "C/media/$$(basename $$ttf)"; \
				fi; \
				if [ $$lc != "C" ]; then \
					ln -nsf "../../C/media/$$(basename $$ttf)" $$lc/media;  \
					git add "$$lc/media/$$(basename $$ttf)"; \
				fi; \
			done; \
		done
	git status --short --untracked-files=no

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
