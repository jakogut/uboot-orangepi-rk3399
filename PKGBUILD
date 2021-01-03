# U-Boot: Orange Pi 4
# Maintainer: Kevin Mihelich <kevin@archlinuxarm.org>
# Maintainer: Joseph Kogut <joseph.kogut@gmail.com>

buildarch=8

pkgname=uboot-orangepi4
pkgver=2020.04
pkgrel=1
_srcname=u-boot-${pkgver}
pkgdesc="U-Boot for Orange Pi 4"
arch=('x86_64' 'aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
backup=('boot/boot.txt' 'boot/boot.scr')
makedepends=('bc' 'git' 'dtc')
options=(!distcc !strip !ccache)
install=${pkgname}.install
atfver=2.3
source=("ftp://ftp.denx.de/pub/u-boot/u-boot-${pkgver}.tar.bz2"
	"https://github.com/ARM-software/arm-trusted-firmware/archive/v${atfver}.tar.gz"
	"0001-add-orangepi4-makefile.patch"
        'boot.txt'
        'mkscr')
md5sums=('51113d2288c55110e33a895c65ab9f60'
         '06ad72bdf63b922a3f3865d81f5d9ad2'
         '6fef01ba666f32c3f7cc7338df11e7e8'
         'e2869196adb467048d6e8c86fbf15fee'
         '021623a04afd29ac3f368977140cfbfd')
PHOST="$CARCH"
case "$CARCH" in
    aarch64) makedepends+=('gcc-arm-none-eabi' 'gcc')
            ;;
    x86_64) source+=('gcc-arm-8.2-2019.01-x86_64-arm-eabi.tar.xz::https://developer.arm.com/-/media/Files/downloads/gnu-a/8.2-2019.01/gcc-arm-8.2-2019.01-x86_64-arm-eabi.tar.xz?revision=72b9b9ec-30c5-4543-ae95-3d840eb01dae&la=en&hash=C898D59AC42008ED0527538ADF200C11F3E8F53A'
                     'gcc-arm-8.2-2019.01-x86_64-aarch64-linux-gnu.tar.xz::https://developer.arm.com/-/media/Files/downloads/gnu-a/8.2-2019.01/gcc-arm-8.2-2019.01-x86_64-aarch64-linux-gnu.tar.xz?revision=21270570-4ec0-4bad-a9e1-09707614066a&la=en&hash=AFEDF645AF5B94336DB4E1E608806CEC87A02B8A')
            md5sums+=('fc11c4f55085c613133d7527a00fed8a'
                      'ed467a18abc7cf81d53c0cf6014b1867')          
            _pkgarch="aarch64"
            CARCH="aarch64"
            CHOST="aarch64-linux-gnu-"
            export PATH="${srcdir}/aarch64/bin:${PATH}"
            export ARCH='aarch64'
            export CROSS_COMPILE='aarch64-linux-gnu-'
            ;;
esac
         
prepare() {
  cd ${srcdir}/${_srcname}
  patch -p1 -i ../0001-add-orangepi4-makefile.patch
  
  if [ -e ${srcdir}/gcc-arm-8.2-2019.01-x86_64-aarch64-linux-gnu.tar.xz ]; then
    if [ -d ${srcdir}/aarch64 ]; then
        rm -r ${srcdir}/aarch64 
    fi
    echo "Preparing Cross-Compilers"
    cd ${srcdir}/
    mkdir aarch64
    cd aarch64
    cp -r ../gcc-arm-8.2-2019.01-x86_64-aarch64-linux-gnu/* ./
    cp -r ../gcc-arm-8.2-2019.01-x86_64-arm-eabi/* ./
    echo "symbolic linking arm-eabi to arm-none-eabi"
    cd bin
    ln -s arm-eabi-addr2line arm-none-eabi-addr2line
    ln -s arm-eabi-c++filt arm-none-eabi-c++filt
    ln -s arm-eabi-gcc arm-none-eabi-gcc
    ln -s arm-eabi-gcc-ranlib arm-none-eabi-gcc-ranlib
    ln -s arm-eabi-gdb arm-none-eabi-gdb
    ln -s arm-eabi-ld.bfd arm-none-eabi-ld.bfd
    ln -s arm-eabi-ranlib arm-none-eabi-ranlib
    ln -s arm-eabi-strip arm-none-eabi-strip
    ln -s arm-eabi-ar arm-none-eabi-ar
    ln -s arm-eabi-cpp arm-none-eabi-cpp
    ln -s arm-eabi-gcc-8.2.1 arm-none-eabi-gcc-8.2.1
    ln -s arm-eabi-gcov arm-none-eabi-gcov
    ln -s arm-eabi-gfortran arm-none-eabi-gfortran
    ln -s arm-eabi-nm arm-none-eabi-nm
    ln -s arm-eabi-readelf arm-none-eabi-readelf
    ln -s arm-eabi-as arm-none-eabi-as
    ln -s arm-eabi-elfedit arm-none-eabi-elfedit
    ln -s arm-eabi-gcc-ar arm-none-eabi-gcc-ar
    ln -s arm-eabi-gcov-dump arm-none-eabi-gcov-dump
    ln -s arm-eabi-gprof arm-none-eabi-gprof
    ln -s arm-eabi-objcopy arm-none-eabi-objcopy
    ln -s arm-eabi-size arm-none-eabi-size
    ln -s arm-eabi-c++ arm-none-eabi-c++
    ln -s arm-eabi-g++ arm-none-eabi-g++
    ln -s arm-eabi-gcc-nm arm-none-eabi-gcc-nm
    ln -s arm-eabi-gcov-tool arm-none-eabi-gcov-tool
    ln -s arm-eabi-ld arm-none-eabi-ld
    ln -s arm-eabi-objdump arm-none-eabi-objdump
    ln -s arm-eabi-strings arm-none-eabi-strings
  fi
}

build() {
  unset CLFAGS CXXFLAGS CPPFLAGS LDFLAGS 
  
  if [ "$PHOST" = "x86_64" ]; then
    CPPFLAGS=""
    CFLAGS=""
    CXXFLAGS=""
    LDFLAGS=""
    export PATH="${srcdir}/aarch64/bin:${PATH}"
  fi

  cd ${srcdir}/arm-trusted-firmware-${atfver}
  make realclean
  make PLAT=rk3399

  cd ${srcdir}/${_srcname}

  # Fixup DDR4 support for Orange Pi 4
  cp configs/orangepi{-rk3399,4}_defconfig
  echo 'CONFIG_RAM_RK3399_LPDDR4=y' >> configs/orangepi4_defconfig
  sed -i "s|CONFIG_DEFAULT_FDT_FILE=\"rockchip/rk3399-orangepi.dtb\"|CONFIG_DEFAULT_FDT_FILE=\"rk3399-orangepi4.dtb\"|" configs/orangepi4_defconfig
  sed -i "s|CONFIG_DEFAULT_DEVICE_TREE=\"rk3399-orangepi\"|CONFIG_DEFAULT_DEVICE_TREE=\"rk3399-orangepi4\"|" configs/orangepi4_defconfig

  cp arch/arm/dts/rk3399-orangepi{,4}.dts
  cp arch/arm/dts/rk3399-{rockpro64,orangepi4}-u-boot.dtsi

  make orangepi4_defconfig

  echo 'CONFIG_IDENT_STRING=" Arch Linux ARM"' >> .config
  make EXTRAVERSION=-${pkgrel} BL31=${srcdir}/arm-trusted-firmware-${atfver}/build/rk3399/release/bl31/bl31.elf
}

package() {
  cd ${_srcname}

  mkdir -p "${pkgdir}/boot"

  cp idbloader.img u-boot.itb "${pkgdir}/boot"

  tools/mkimage -A arm -O linux -T script -C none -n "U-Boot boot script" -d ../boot.txt "${pkgdir}/boot/boot.scr"
  cp ../{boot.txt,mkscr} "${pkgdir}"/boot
}
