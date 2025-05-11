# Maintainer: Leo <i@hardrain980.com>
pkgname=('nvidia-vgpu-16-utils' 'nvidia-vgpu-16-utils-unlock')
pkgbase=nvidia-vgpu-16-utils
pkgver=535.247.02
pkgrel=1
arch=('x86_64')
url="https://www.nvidia.com/"
license=('custom:proprietary')
provides=('nvidia-utils')
conflicts=('nvidia-utils')
options=(!strip)
_pkg="NVIDIA-Linux-x86_64-${pkgver}-vgpu-kvm"
_pkg_unlock="NVIDIA-Linux-x86_64-${pkgver}-vgpu-kvm-unlock"
source=(
	"${_pkg}.run"
	"vgpu_unlock_${pkgver}.patch"
)
sha256sums=(
	'd6bffcc1c2bfc0613981541df573ef87fd49ed2cb1df99d66e7f0f65f051c994'
	'24c519d71d8c53cc1198dbebdc1e58470c7cdd1472f4d07342029e6600298907'
)

prepare() {
	sh "${_pkg}.run" -x
	cp -rp "${srcdir}/${_pkg}" "${srcdir}/${_pkg_unlock}"
	patch -Np1 -d "${srcdir}/${_pkg_unlock}" < "${srcdir}/vgpu_unlock_${pkgver}.patch"
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

package_nvidia-vgpu-16-utils() {
	pkgdesc="NVIDIA vGPU drivers utilities v16"
	_utils "${srcdir}/${_pkg}"
}

package_nvidia-vgpu-16-utils-unlock() {
	pkgdesc="NVIDIA vGPU drivers utilities v16, with vGPU unlock patch"
	depends+=('vgpu_unlock-rs')
	_utils "${srcdir}/${_pkg_unlock}"
}
