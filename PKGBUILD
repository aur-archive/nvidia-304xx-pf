# Maintainer : Kars Wang <jaklsy AT gmail DOT com>

pkgname=nvidia-304xx-pf
pkgver=304.121
pkgrel=3
_goodkver=3.14
_badkver=3.15
_modver="${_goodkver}-pf"
_extramodules="extramodules-${_modver}"
_kernver="$(cat "/usr/lib/modules/${_extramodules}/version")"
_SYSSRC="/usr/lib/modules/${_kernver}/build"
_pf_kernel="$(pacman -Qqo '/boot/vmlinuz-linux-pf')"
#_pf_headers="$(pacman -Qqo "${_SYSSRC}")"
_cpu="$(sed -e 's/linux-pf//' <<< "${_pf_kernel}")"

pkgdesc='NVIDIA drivers for linux-pf, 304xx legacy branch.'
arch=('i686' 'x86_64')
url='http://www.nvidia.com/'
makedepends=("linux-pf-headers>=${_goodkver}" "linux-pf-headers<${_badkver}")
depends=("linux-pf>=${_goodkver}" "linux-pf<${_badkver}" "nvidia-304xx-utils=${pkgver}" 'nvidia-304xx-libgl')
if [ -n "${_cpu}" ]; then
    true && pkgdesc="NVIDIA drivers for linux-pf${_cpu}, 304xx legacy branch."
    true && makedepends=("linux-pf-headers${_cpu}>=${_goodkver}" "linux-pf-headers${_cpu}<${_badkver}")
    true && depends=("linux-pf${_cpu}>=${_goodkver}" "linux-pf${_cpu}<${_badkver}" "nvidia-304xx-utils=${pkgver}")
fi
conflicts=('nvidia-96xx' 'nvidia-173xx' 'nvidia-304xx-pf-core2' 'nvidia-304xx-pf-k8'
        'nvidia-304xx-pf-atom' 'nvidia-304xx-pf-psc' 'nvidia-304xx-pf-p4' 'nvidia-304xx-pf-p3'
        'nvidia-304xx-pf-pm' 'nvidia-304xx-pf-k7' 'nvidia-pf-core2' 'nvidia-pf-k8'
        'nvidia-pf-atom' 'nvidia-pf-psc' 'nvidia-pf-p4' 'nvidia-pf-p3'
        'nvidia-pf-pm' 'nvidia-pf-k7')
license=('custom')
install=nvidia-304xx-pf.install
options=(!strip)

if [ "$CARCH" = 'i686' ]; then
    _arch='x86'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}"
    source=("http://us.download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('b8c950f0a30a6e1f503630fbd0ca8ab9')
elif [ "$CARCH" = 'x86_64' ]; then
    _arch='x86_64'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}-no-compat32"
    source=("http://us.download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('41e6d2b6325e5b0ae9889cfbd6bec696')
fi

prepare() {
    cd "${srcdir}"
    [ -d "${_pkg}" ] && rm -rf "${_pkg}"
    sh "${_pkg}.run" --extract-only
    cd "${pkg}"
}

build() {
    cd "${_pkg}/kernel"
    [ -n "${_cpu}" ] && msg "Found optimized linux-pf${_cpu}, building ${pkgname}${_cpu}."
    make SYSSRC="${_SYSSRC}" module
}

package() {
if [ -n "${_cpu}" ]; then
    true && pkgname="${pkgname}${_cpu}"
    true && conflicts=("${conflicts[@]/nvidia-304xx-pf${_cpu}/}" 'nvidia-304xx-pf')
fi
    install -D -m644 "${srcdir}/${_pkg}/kernel/nvidia.ko" \
        "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia.ko"
    install -d -m755 "${pkgdir}/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "${pkgdir}/usr/lib/modprobe.d/nvidia-pf.conf"
    echo "blacklist nvidiafb" >> "${pkgdir}/usr/lib/modprobe.d/nvidia-pf.conf"
    sed -i -e "s/KERNEL_VERSION='.*'/KERNEL_VERSION='${_kernver}'/" \
        "${startdir}/nvidia-304xx-pf.install"
    gzip -9 "${pkgdir}/usr/lib/modules/${_extramodules}/nvidia.ko"
}
