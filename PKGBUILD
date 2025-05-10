# Maintainer: Yurii Kolesnykov <root@yurikoles.com>
# Contributor: Jonathon Fernyhough <jonathon"m2x+dev>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: Iacopo Isimbaldi <isiachi@rhye.it>
#
# Based on aur/zfs-dkms by Kevin Stolp <kevinstolp@gmail.com>
# Based on archzfs/zfs-dkms-git by Jan Houben <jan@nexttrex.de>
#
# Pull Requests are welcome here: https://github.com/yurikoles-aur/zfs-dkms-git
#

pkgname=zfs-dkms-git
pkgver=2.3.99.r330.gc17bdc4914
pkgrel=1
epoch=2
pkgdesc="Kernel modules for the Zettabyte File System."
arch=('any')
url="https://zfsonlinux.org/"
license=('CDDL')
groups=('zfs-git')
makedepends=('git')
provides=("ZFS-MODULE=${pkgver}" "SPL-MODULE=${pkgver}")
# ambiguous, provided for backwards compat, pls don't use
provides+=('zfs')
provides+=("zfs-dkms=${pkgver}")
conflicts=('zfs-dkms')
source=("git+https://github.com/openzfs/zfs.git"
        "0001-only-build-the-module-in-dkms.conf.patch")
sha256sums=('SKIP'
            '7accc040bcd7bece85ff6b39c5f08363900f1a0e982b4b51a0bfcb7daeaffaa2')
b2sums=('SKIP'
        'a27b6d809d5e76b8bea504f908efa98fd7d7145a0e91786d496989b03bb69927f9cc4f92c61f7a73d00e9e1da92a9233b6d643f24b4d08c9d60e9e13d029a154')

pkgver() {
    cd zfs

    git describe --long | sed 's/^zfs-//;s/-rc/rc/;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
    cd zfs

    patch -p1 -i ../0001-only-build-the-module-in-dkms.conf.patch

    # remove unneeded sections from module build
    sed -ri "/AC_CONFIG_FILES/,/]\)/{
/AC_CONFIG_FILES/n
/]\)/n
/^\s*(module\/.*|zfs.release|Makefile)/!d
}" configure.ac

    ./autogen.sh
}

build() {
    cd zfs

    ./scripts/dkms.mkconf -n zfs -v ${pkgver} -f dkms.conf
    # update metadata
    ./scripts/make_gitrev.sh include/zfs_gitrev.h
    _meta_release=${pkgver#*.r}
    sed -i -e "s/Release:[[:print:]]*/Release:      ${_meta_release/./_}/" META
}

package() {
    depends=("zfs-utils-git=${epoch}:${pkgver}" 'dkms')

    cd zfs

    dkmsdir="${pkgdir}/usr/src/zfs-${pkgver}"
    install -d "${dkmsdir}"/{config,scripts}
    cp -a configure dkms.conf Makefile.in META zfs_config.h.in zfs.release.in include/ module/ "${dkmsdir}"/
    cp config/compile config/config.* config/missing config/*sh "${dkmsdir}"/config/
    cp scripts/dkms.postbuild "${dkmsdir}"/scripts/
}
