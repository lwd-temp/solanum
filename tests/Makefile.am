check_PROGRAMS = runtests \
	chmode1 \
	match1 \
	misc \
	msgbuf_parse1 \
	msgbuf_unparse1 \
	hostmask1 \
	privilege1 \
	rb_dictionary1 \
	rb_snprintf_append1 \
	rb_snprintf_try_append1 \
	sasl_abort1 \
	send1 \
	send_multiline1 \
	serv_connect1 \
	substitution1
AM_CFLAGS=$(WARNFLAGS)
AM_CPPFLAGS = $(DEFAULT_INCLUDES) -I../librb/include -I..
AM_LDFLAGS = -no-install
LDADD = libutil.a tap/libtap.a ../librb/src/librb.la ../ircd/libircd.la -ldl

CLEANFILES = TESTS

# Override -rpath or programs will be linked to installed libraries
libdir=$(abs_top_builddir)

runtests_CPPFLAGS = -DC_TAP_SOURCE='"$(abs_top_srcdir)/tests"' \
	-DC_TAP_BUILD='"$(abs_top_builddir)/tests"'
check_LIBRARIES = tap/libtap.a libutil.a
tap_libtap_a_SOURCES = tap/basic.c tap/basic.h \
	tap/float.c tap/float.h tap/macros.h
libutil_a_SOURCES = ircd_util.c client_util.c

TESTS: Makefile
	printf '%s\n' $(check_PROGRAMS) | sed '/^runtests$$/d' > TESTS

check-local: $(check_PROGRAMS) \
	TESTS \
	../authd/authd \
	../bandb/bandb \
	../ssld/ssld \
	$(patsubst ../modules/%.c,../modules/.libs/%.so,$(wildcard ../modules/*.c)) \
	$(patsubst ../modules/core/%.c,../modules/core/.libs/%.so,$(wildcard ../modules/core/*.c))

	rm -rf runtime/modules && mkdir -p runtime/modules/autoload
	for f in ../modules/core/.libs/*.so; do ln -s "../../../modules/core/.libs/$${f##*/}" "runtime/modules/$${f##*/}"; done
	for f in ../modules/.libs/*.so; do ln -s "../../../../modules/.libs/$${f##*/}" "runtime/modules/autoload/$${f##*/}"; done

	ASAN_OPTIONS="${ASAN_OPTIONS}:detect_leaks=false" ./runtests -l $(abs_top_srcdir)/tests/TESTS

clean-local:
	rm -rf runtime/modules
	rm -rf *.db *.log
