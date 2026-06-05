FROM amd64/ubuntu:20.04 AS base

#If you found this docker image helpful please donate RVN to the maintainer
LABEL maintainer="RV9zdNeUTQUToZUcRp9uNF8gwH5LzDFtan"

EXPOSE 8766/tcp
EXPOSE 8767/tcp

ENV DEBIAN_FRONTEND=noninteractive

#Install runtime dependencies
RUN apt-get update && \
	apt-get install -y --no-install-recommends \
	bash wget net-tools libminiupnpc17 \
	libevent-2.1 libevent-pthreads-2.1 \
	libboost-system1.71 libboost-filesystem1.71 libboost-chrono1.71 \
	libboost-program-options1.71 libboost-thread1.71 \
	libzmq5 && \
	apt-get clean

FROM base AS build

#Install build dependencies
RUN apt-get update && \
	apt-get install -y --no-install-recommends \
	bash net-tools build-essential libtool autotools-dev automake \
	pkg-config libssl-dev libevent-dev bsdmainutils python3 \
	libboost-system1.71-dev libboost-filesystem1.71-dev libboost-chrono1.71-dev \
	libboost-program-options1.71-dev libboost-test1.71-dev libboost-thread1.71-dev \
	libzmq3-dev libminiupnpc-dev && \
	apt-get clean

#Copy source dir
COPY . /home/th3/build/TH3Chain/
WORKDIR /home/th3/build/TH3Chain

#build db4 from source
WORKDIR /home/th3/build/TH3Chain/contrib
RUN ./install_db4.sh ../../

# build TH3core
WORKDIR /home/th3/build/TH3Chain
RUN ./autogen.sh && ./configure --disable-tests BDB_LIBS="-L/home/th3/build/db4/lib -ldb_cxx-4.8" BDB_CFLAGS="-I/home/th3/build/db4/include" --with-gui=no  && make -j4

FROM base AS final

#Add our service account user
RUN useradd -ms /bin/bash raven && \
	mkdir /var/lib/th3 && \
	chown raven:raven /var/lib/th3 && \
	ln -s /var/lib/th3 /home/raven/.raven && \
	chown -h raven:raven /home/raven/.raven

VOLUME /var/lib/th3

#Copy the compiled binaries from the build
COPY --from=build /home/th3/build/TH3Chain/src/th3d /usr/local/bin/th3d
COPY --from=build /home/th3/build/TH3Chain/src/th3-cli /usr/local/bin/th3-cli

WORKDIR /home/raven
USER raven

CMD /usr/local/bin/th3d -datadir=/var/lib/th3 -printtoconsole -onlynet=ipv4
