
target_name ?= nheqminer

include ../Makefile.build

NHEQMINER_VERSION="0.5c"

# basic CUDA check for macOS
OSX_CUDA_BIN := $(shell ls -d /usr/local/cuda/bin/nvcc | tail -n 1)
OSX_CUDA_LIB := $(shell ls -d /usr/local/cuda/lib/libcuda.dylib | tail -n 1)
ifneq ($(OSX_CUDA_BIN),)
ifneq ($(OSX_CUDA_LIB),)
ENABLE_CUDA=y
endif
endif

nheqminer_clone:
	if [ ! -f "nheqminer-$(NHEQMINER_VERSION).tar.gz" ]; then curl -o nheqminer-$(NHEQMINER_VERSION).tar.gz -L https://github.com/nicehash/nheqminer/archive/$(NHEQMINER_VERSION).tar.gz; fi
	if [ ! -d "nheqminer-$(NHEQMINER_VERSION)" ]; then tar xvf nheqminer-$(NHEQMINER_VERSION).tar.gz; fi
	if [ ! -d "nheqminer" ]; then ln -sf nheqminer-$(NHEQMINER_VERSION) nheqminer; fi

nheqminer_config:
	$(call patchme,nheqminer-$(NHEQMINER_VERSION))
	( cd nheqminer-$(NHEQMINER_VERSION)/cpu_xenoncat/asm_mac; rm -rf equihash_avx*.elf.o equihash_avx*.o ; sh assemble.sh; )
	( mkdir -p nheqminer-cpu; cd nheqminer-cpu; \
		BOOST_ROOT=${BSPROOTFS} \
		cmake -DCMAKE_INSTALL_PREFIX=${BSPINSTALL} \
		-DCMAKE_CXX_FLAGS="-fvisibility=hidden" \
		-DCMAKE_EXE_LINKER_FLAGS="-L/usr/local/cuda/lib" \
		-DUSE_CPU_TROMP=ON -DUSE_CUDA_TROMP=OFF -DUSE_CPU_XENONCAT=ON -DUSE_CUDA_DJEZO=OFF ../nheqminer-$(NHEQMINER_VERSION); \
	)
ifeq ($(ENABLE_CUDA),y)
	( mkdir -p nheqminer-gpu; cd nheqminer-gpu; \
		BOOST_ROOT=${BSPROOTFS} \
		cmake -DCMAKE_INSTALL_PREFIX=${BSPINSTALL} \
		-DCMAKE_CXX_FLAGS="-fvisibility=hidden" \
		-DCMAKE_EXE_LINKER_FLAGS="-L/usr/local/cuda/lib" \
		-DUSE_CPU_TROMP=OFF -DUSE_CUDA_TROMP=OFF -DUSE_CPU_XENONCAT=ON -DUSE_CUDA_DJEZO=ON ../nheqminer-$(NHEQMINER_VERSION); \
	)
	( mkdir -p nheqminer-old; cd nheqminer-old; \
		BOOST_ROOT=${BSPROOTFS} \
		cmake -DCMAKE_INSTALL_PREFIX=${BSPINSTALL} \
		-DCMAKE_CXX_FLAGS="-fvisibility=hidden" \
		-DCMAKE_EXE_LINKER_FLAGS="-L/usr/local/cuda/lib" \
		-DUSE_CPU_TROMP=OFF -DUSE_CUDA_TROMP=ON -DUSE_CPU_XENONCAT=OFF -DUSE_CUDA_DJEZO=OFF ../nheqminer-$(NHEQMINER_VERSION); \
	)
endif

nheqminer_build:
	make -C nheqminer-cpu -j ${BSPJOB}
ifeq ($(ENABLE_CUDA),y)
	make -C nheqminer-gpu -j ${BSPJOB}
	make -C nheqminer-old -j ${BSPJOB}
endif

nheqminer_install:
	if [ -f "nheqminer-cpu/nheqminer" ]; then cp -af nheqminer-cpu/nheqminer ${BSPINSTALL}/nheqminer-cpu; fi
	if [ -f "nheqminer-gpu/nheqminer" ]; then cp -af nheqminer-gpu/nheqminer ${BSPINSTALL}/nheqminer-gpu; fi
	if [ -f "nheqminer-old/nheqminer" ]; then cp -af nheqminer-old/nheqminer ${BSPINSTALL}/nheqminer-old; fi

nheqminer_uninstall:
	rm -rf ${BSPINSTALL}/nheqminer-cpu
	rm -rf ${BSPINSTALL}/nheqminer-gpu
	rm -rf ${BSPINSTALL}/nheqminer-old

nheqminer_clean:
	if [ -f "nheqminer-cpu/Makefile" ]; then make -C nheqminer-cpu clean; fi
	if [ -f "nheqminer-gpu/Makefile" ]; then make -C nheqminer-gpu clean; fi
	if [ -f "nheqminer-old/Makefile" ]; then make -C nheqminer-old clean; fi

nheqminer_distclean:
	rm -rf nheqminer-cpu
	rm -rf nheqminer-gpu
	rm -rf nheqminer-old
	rm -rf nheqminer-$(NHEQMINER_VERSION)
