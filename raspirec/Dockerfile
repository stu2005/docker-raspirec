# epgdump
FROM debian:latest AS epgdump-build
WORKDIR /epgdump
RUN apt-get update \
&&  apt-get full-upgrade -y \
&&  apt-get autoremove --purge -y \
&&  apt-get install git build-essential cmake -y \
&&  git clone https://github.com/Piro77/epgdump.git . \
&&  cmake . \
&&  make

# raspirec
FROM debian:latest AS raspirec-source
WORKDIR /raspirec
RUN apt-get update \
&&  apt-get full-upgrade -y \
&&  apt-get autoremove --purge -y \
&&  apt-get install git -y \
&&  git clone https://github.com/kaikoma-soft/raspirec.git .

# recfsusb2n
FROM collelog/recfsusb2n-build:epgdatacapbon-latest-debian AS recfsusb2n-image

# libarib25
FROM collelog/libarib25-build:epgdatacapbon-latest-debian AS libarib25-image

# final image
FROM debian:latest
COPY --from=epgdump-build /epgdump/epgdump /usr/local/bin/epgdump
COPY --from=raspirec-source /raspirec /root/raspirec
COPY --from=recfsusb2n-image /build /
COPY --from=libarib25-image /build /
WORKDIR /root/raspirec
RUN apt-get update \
&&  apt-get full-upgrade -y \
&&  apt-get autoremove --purge -y \
&&  apt-get install -y \
    sqlite3 \
    ruby \
    ruby-sqlite3 \
    ruby-sys-filesystem \
    ruby-net-ssh \
    ruby-sinatra \
    ruby-slim \
    ruby-sass \
    pcscd \
    libpcsclite1 \
    libpcsclite-dev \
&&  apt-get clean \
&&  rm -rf /var/lib/apt/lists/* \
&&  mkdir -p ~/.config/raspirec
CMD ["ruby","./raspirec.rb","-f"]
EXPOSE 4567
