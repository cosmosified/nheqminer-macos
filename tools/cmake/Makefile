
target_name ?= cmake

include ../Makefile.build

CMAKE_VERSION="3.8.1"

cmake_clone:
	if [ ! -f "cmake-$(CMAKE_VERSION).tar.gz" ]; then curl -OL https://cmake.org/files/v3.8/cmake-$(CMAKE_VERSION).tar.gz; fi;
	if [ ! -d "cmake-$(CMAKE_VERSION)" ]; then tar xzf cmake-$(CMAKE_VERSION).tar.gz; fi

cmake_config:
	( cd cmake-$(CMAKE_VERSION); CFLAGS="" CXXFLAGS="" ./bootstrap --prefix=${BSPTOOLS}/o; )

cmake_build:
	make -C cmake-$(CMAKE_VERSION) -j ${BSPJOB}

cmake_install:
	make -C cmake-$(CMAKE_VERSION) -j ${BSPJOB} install

cmake_uninstall:
	make -C cmake-$(CMAKE_VERSION) uninstall

cmake_clean:
	if [ -f "cmake-$(CMAKE_VERSION)/Makefile" ]; then make -C cmake-$(CMAKE_VERSION) distclean; fi

cmake_distclean:
	rm -rf cmake-$(CMAKE_VERSION)
	rm -rf cmake-$(CMAKE_VERSION).tar.gz
