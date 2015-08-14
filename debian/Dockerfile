FROM tianon/debian-devel

# TODO find a cleaner way to get ".git" in the image without this hack
RUN git clone --no-checkout git://anonscm.debian.org/pkg-go/packages/golang-github-gorilla-mux.git /usr/src/golang-github-gorilla-mux

# start by adding just "debian/control" so we can get mk-build-deps with maximum caching
ADD control /usr/src/golang-github-gorilla-mux/debian/
WORKDIR /usr/src/golang-github-gorilla-mux

# get all the build deps of _this_ package in a nice repeatable way
RUN apt-get update && mk-build-deps -irt'apt-get --no-install-recommends -yq' debian/control

# need our debian/ directory to compile _this_ package
ADD . /usr/src/golang-github-gorilla-mux/debian

# go download and unpack our upstream source
#RUN uscan --force-download --verbose --download-current-version
RUN git fetch --tags && ./debian/helpers/generate-tarball.sh ../
RUN origtargz --unpack

# tianon is _really_ lazy, and likes a preseeded bash history
RUN echo 'origtargz --unpack && dpkg-buildpackage -us -uc && lintian -EvIL+pedantic' >> /root/.bash_history
