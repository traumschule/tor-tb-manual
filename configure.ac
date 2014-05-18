AC_INIT([tor-browser-user-manual],
        m4_esyscmd([build-aux/git-version-gen .version]),
        [tbb-dev@lists.torproject.org])

AM_INIT_AUTOMAKE([foreign])

AC_ARG_WITH(tor-browser-bundle,
            AC_HELP_STRING([--with-tor-browser-bundle=PROGRAM],
                           [location of the clone of tor-browser-bundle.git]),
            [tor_browser_bundle_dir=$withval])
VERSIONS_PATH="${tor_browser_bundle_dir}/gitian/versions"
if ! sh -n "$VERSIONS_PATH" 2>/dev/null; then
    AC_MSG_ERROR([Please clone tor-browser-bundle.git and specify its location using --with-tor-browser-bundle])
fi

TOR_BROWSER_VERSION=$(. "$VERSIONS_PATH"; echo "$TORBROWSER_VERSION")
if test "x$TOR_BROWSER_VERSION" = "x"; then
    AC_MSG_ERROR([Couldn't determine Tor Browser version.])
fi
TOR_BROWSER_BUNDLE_LOCALES=$(. "$VERSIONS_PATH"; echo "$BUNDLE_LOCALES")

AC_SUBST(TOR_BROWSER_BUNDLE_LOCALES)

YELP_HELP_INIT

AC_CONFIG_FILES([Makefile
                 tor-browser-version.txt])

AC_OUTPUT
