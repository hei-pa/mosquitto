

.PHONY: all install uninstall reallyclean clean static static_pub static_sub

ifeq ($(WITH_SHARED_LIBRARIES),yes)
SHARED_DEP:=../lib/libmosquitto.so.${SOVERSION}
endif

static : static_pub static_sub
ifeq ($(WITH_SHARED_LIBRARIES),yes)
ALL_DEPS:= mosquitto_pub mosquitto_sub mosquitto_bridge
else
ifeq ($(WITH_STATIC_LIBRARIES),yes)
ALL_DEPS:= static_pub static_sub static_bridge
endif
endif

all : ${ALL_DEPS}

static : static_pub static_sub static_bridge
	# This makes mosquitto_pub/sub versions that are statically linked with
	# libmosquitto only.

static_pub : pub_client.o client_shared.o ../lib/libmosquitto.a
	${CROSS_COMPILE}${CC} $^ -o mosquitto_pub ${CLIENT_LDFLAGS} -lssl -lcrypto -lpthread

static_sub : sub_client.o sub_client_output.o client_shared.o ../lib/libmosquitto.a
	${CROSS_COMPILE}${CC} $^ -o mosquitto_sub ${CLIENT_LDFLAGS} -lssl -lcrypto -lpthread

static_bridge : bridge_client.o client_shared.o ../lib/libmosquitto.a
	${CROSS_COMPILE}${CC} $^ -o mosquitto_bridge ${CLIENT_LDFLAGS} -lssl -lcrypto -lpthread

mosquitto_pub : pub_client.o client_shared.o
	${CROSS_COMPILE}${CC} $^ -o $@ ${CLIENT_LDFLAGS}

mosquitto_sub : sub_client.o sub_client_output.o client_shared.o
	${CROSS_COMPILE}${CC} $^ -o $@ ${CLIENT_LDFLAGS}

pub_client.o : pub_client.c ../lib/libmosquitto.so.${SOVERSION}
mosquitto_bridge : bridge_client.o client_shared.o
	${CROSS_COMPILE}${CC} $^ -o $@ ${CLIENT_LDFLAGS}

pub_client.o : pub_client.c ../lib/libmosquitto.so.${SOVERSION}
	${CROSS_COMPILE}${CC} -c $< -o $@ ${CLIENT_CFLAGS}

sub_client.o : sub_client.c ${SHARED_DEP}
	${CROSS_COMPILE}${CC} -c $< -o $@ ${CLIENT_CFLAGS}

sub_client_output.o : sub_client_output.c ${SHARED_DEP}
	${CROSS_COMPILE}${CC} -c $< -o $@ ${CLIENT_CFLAGS}

bridge_client.o : bridge_client.c ${SHARED_DEP}
	${CROSS_COMPILE}${CC} -c $< -o $@ ${CLIENT_CFLAGS}

client_shared.o : client_shared.c client_shared.h
	${CROSS_COMPILE}${CC} -c $< -o $@ ${CLIENT_CFLAGS}

../lib/libmosquitto.so.${SOVERSION} :
	$(MAKE) -C ../lib

../lib/libmosquitto.a :
	$(MAKE) -C ../lib libmosquitto.a

install : all
	$(INSTALL) -d "${DESTDIR}$(prefix)/bin"
	$(INSTALL) ${STRIP_OPTS} mosquitto_pub "${DESTDIR}${prefix}/bin/mosquitto_pub"
	$(INSTALL) ${STRIP_OPTS} mosquitto_sub "${DESTDIR}${prefix}/bin/mosquitto_sub"
	$(INSTALL) ${STRIP_OPTS} mosquitto_bridge "${DESTDIR}${prefix}/bin/mosquitto_bridge"

uninstall :
	-rm -f "${DESTDIR}${prefix}/bin/mosquitto_pub"
	-rm -f "${DESTDIR}${prefix}/bin/mosquitto_sub"
	-rm -f "${DESTDIR}${prefix}/bin/mosquitto_bridge"

reallyclean : clean

clean : 
	-rm -f *.o mosquitto_pub mosquitto_sub mosquitto_bridge
