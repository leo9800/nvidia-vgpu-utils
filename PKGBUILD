# Maintainer: Leo <i@hardrain980.com>
pkgname=('nvidia-vgpu-18-utils')
pkgbase=nvidia-vgpu-18-utils
pkgver=570.148.06
pkgrel=1
arch=('x86_64')
url="https://www.nvidia.com/"
license=('custom:proprietary')
provides=('nvidia-utils')
conflicts=('nvidia-utils')
options=(!strip)
_pkg="NVIDIA-Linux-x86_64-${pkgver}-vgpu-kvm"
_pkg_unlock="NVIDIA-Linux-x86_64-${pkgver}-vgpu-kvm-unlock"
source=("${_pkg}.run")
sha256sums=('fedcb2fca8bdb80ade416d113c0b31a8fbc33ebf4c8cfb1fcc5325023db0fd45')

prepare() {
	sh "${_pkg}.run" -x
}

_utils() {
	cd "$1"

	# binary
	install -Dm0755 nvidia-bug-report.sh "${pkgdir}/usr/bin/nvidia-bug-report.sh"
	install -Dm0755 nvidia-debugdump "${pkgdir}/usr/bin/nvidia-debugdump"
	install -Dm4755 nvidia-modprobe "${pkgdir}/usr/bin/nvidia-modprobe"
	install -Dm0755 nvidia-smi "${pkgdir}/usr/bin/nvidia-smi"
	install -Dm0755 nvidia-vgpud "${pkgdir}/usr/bin/nvidia-vgpud"
	install -Dm0755 nvidia-vgpu-mgr "${pkgdir}/usr/bin/nvidia-vgpu-mgr"
	install -Dm0755 nvidia-xid-logd "${pkgdir}/usr/bin/nvidia-xid-logd"

	# firmware
	install -Dm0644 -t "${pkgdir}/usr/lib/firmware/nvidia/${pkgver}/" firmware/*.bin

	# conf
	install -Dm0644 -t "${pkgdir}/usr/share/nvidia/vgpu/" vgpuConfig.xml

	# doc
	install -Dm0644 nvidia-modprobe.1.gz "${pkgdir}/usr/share/man/man1/nvidia-modprobe.1.gz"
	install -Dm0644 nvidia-smi.1.gz "${pkgdir}/usr/share/man/man1/nvidia-smi.1.gz"

	# lib
	install -Dm0755 "libnvidia-ml.so.${pkgver}" "${pkgdir}/usr/lib/libnvidia-ml.so.${pkgver}"
	install -Dm0755 "libnvidia-vgpu.so.${pkgver}" "${pkgdir}/usr/lib/libnvidia-vgpu.so.${pkgver}"
	install -Dm0755 "libnvidia-vgxcfg.so.${pkgver}" "${pkgdir}/usr/lib/libnvidia-vgxcfg.so.${pkgver}"

	# systemd
	install -Dm0644 -t "${pkgdir}/usr/lib/systemd/system/" init-scripts/systemd/*.service
	sed -i '/Wants=syslog.target/d' "${pkgdir}/usr/lib/systemd/system/"*.service

	# avoid loading nouveau
	echo "blacklist nouveau" | install -Dm0644 /dev/stdin "${pkgdir}/usr/lib/modprobe.d/nvidia-utils.conf"

	# license
	install -Dm0644 'LICENSE' "${pkgdir}/usr/share/licenses/nvidia-utils-vgpu/LICENSE"

	find "${pkgdir}" -type f -name '*.so*' ! -path '*xorg/*' -print0 | while read -d $'\0' _lib; do
		_soname=$(dirname "${_lib}")/$(readelf -d "${_lib}" | grep -Po 'SONAME.*: \[\K[^]]*' || true)
		_base=$(echo ${_soname} | sed -r 's/(.*)\.so.*/\1.so/')
		[[ -e "${_soname}" ]] || ln -s $(basename "${_lib}") "${_soname}"
		[[ -e "${_base}" ]] || ln -s $(basename "${_soname}") "${_base}"
	done
}

package_nvidia-vgpu-18-utils() {
	pkgdesc="NVIDIA vGPU drivers utilities v18"
	_utils "${srcdir}/${_pkg}"
}
