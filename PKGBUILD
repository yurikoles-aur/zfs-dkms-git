# Maintainer: Yurii Kolesnykov <root@yurikoles.com>
# Contributor: Jonathon Fernyhough <jonathon"m2x+dev>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: Iacopo Isimbaldi <isiachi@rhye.it>
#
# Based on aur/zfs-dkms by Kevin Stolp <kevinstolp@gmail.com>
# Based on archzfs/zfs-dkms-git by Jan Houben <jan@nexttrex.de>
#
# Pull Requests are welcome: https://github.com/yurikoles-aur/zfs-dkms-git
#

pkgname=zfs-dkms-git
pkgver=2.2.99.r848.g56ab541521
pkgrel=1
epoch=2
pkgdesc="Kernel modules for the Zettabyte File System."
arch=('any')
url="https://zfsonlinux.org/"
license=('CDDL')
groups=('zfs-git')
depends=('dkms')
makedepends=('git')
optdepends=("zfs-utils-git=${epoch}:${pkgver}")
provides=("ZFS-MODULE=${pkgver}" "SPL-MODULE=${pkgver}")
# ambiguous, provided for backwards compat, pls don't use
provides+=('zfs')
provides+=("zfs-dkms=${pkgver}")
conflicts=('zfs-dkms')
source=("git+https://github.com/openzfs/zfs.git"
        "0001-only-build-the-module-in-dkms.conf.patch")
sha256sums=('SKIP'
            '539f325e56443554f9b87baff33948b91a280ec1daadcb0c636b105252fcd0f5')
b2sums=('SKIP'
        'a8ab5da81d214e7801f0f8cdf77c076c714a3f17292df15ca35fcf7aef2c4d505348797e3b1da7590ea303ff488490ddba49e6f9e3f8a0bcc975894d51d97c2b')

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
    cd zfs

    dkmsdir="${pkgdir}/usr/src/zfs-${pkgver}"
    install -d "${dkmsdir}"/{config,scripts}
    cp -a configure dkms.conf Makefile.in META zfs_config.h.in zfs.release.in include/ module/ "${dkmsdir}"/
    cp config/compile config/config.* config/missing config/*sh "${dkmsdir}"/config/
    cp scripts/dkms.postbuild "${dkmsdir}"/scripts/
}
