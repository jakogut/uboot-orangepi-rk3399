# U-Boot: Orange Pi 4
# Maintainer: Kevin Mihelich <kevin@archlinuxarm.org>
# Maintainer: Joseph Kogut <joseph.kogut@gmail.com>

buildarch=8

pkgname=uboot-orangepi4
pkgver=2020.04
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

prepare() {
  cd ${srcdir}/${_srcname}
  patch -p1 -i ../0001-add-orangepi4-makefile.patch
}

build() {
  unset CLFAGS CXXFLAGS CPPFLAGS LDFLAGS

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
