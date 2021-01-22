# U-Boot: Orange Pi 4
# Maintainer: Kevin Mihelich <kevin@archlinuxarm.org>
# Maintainer: Joseph Kogut <joseph.kogut@gmail.com>

buildarch=8

pkgname=uboot-orangepi4
pkgver=2021.01
pkgrel=1
_srcname=u-boot-${pkgver}
pkgdesc="U-Boot for Orange Pi 4"
arch=('aarch64')
url='http://www.denx.de/wiki/U-Boot/WebHome'
license=('GPL')
backup=('boot/boot.txt' 'boot/boot.scr')
makedepends=('bc' 'git')
install=${pkgname}.install
atfver=2.3
opi4_patch_sha=6e40789
source=("ftp://ftp.denx.de/pub/u-boot/u-boot-${pkgver}.tar.bz2"
	"https://github.com/ARM-software/arm-trusted-firmware/archive/v${atfver}.tar.gz"
	"https://raw.githubusercontent.com/armbian/build/${opi4_patch_sha}/patch/u-boot/u-boot-rockchip64-mainline/add-board-orangepi-4.patch"
        'boot.txt'
        'mkscr')
md5sums=('a3206df1c1b97df7a4ddcdd17cb97d0c'
         '06ad72bdf63b922a3f3865d81f5d9ad2'
         '40cb0841f3f76b3e45c3b0f927cefa46'
         '743a5578cd1a2c7effae08a82bd63856'
         '021623a04afd29ac3f368977140cfbfd')
prepare() {
  cd ${srcdir}/${_srcname}
  patch -p1 -i ../add-board-orangepi-4.patch
}

build() {
  unset CFLAGS CXXFLAGS CPPFLAGS LDFLAGS

  cd ${srcdir}/arm-trusted-firmware-${atfver}
  make realclean
  make PLAT=rk3399

  cd ${srcdir}/${_srcname}

  make orangepi-4-rk3399_defconfig

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
