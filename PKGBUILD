# Maintainer: Bruno Silva <brunofernandes at ua dot pt>
# Contributor: Stefan Loewen <stefan.loewen@gmail.com>

pkgname='omnetpp-qt'
pkgver=5.3
_pkgver=5-3
pkgrel=1
_pkgname='omnetpp'
pkgdesc='OMNeT++ Discrete Event Simulator. OMNeT++ is an extensible, modular, component-based C++ simulation library and framework, primarily for building network simulators: QT version'
url='http://www.omnetpp.org'
license=('custom')
depends=(libxml2 qt5-base tcl tk blt jdk8-openjdk openmpi libpcap doxygen graphviz clang openscenegraph)
makedepends=(sh wget qt5-base cmake gcc bison flex perl openscenegraph)
arch=('i686' 'x86_64')
provides=('omnetpp')
conflicts=('omnetpp')

if [ $(echo "018b3cadb7cff592da0959b5cff3923e0d2f9a621cadd2d385e5dfc6547d992c50720f0e89f01145b2041fb58b8e3fec94dbabc832e61b3cabc49bc5d887b905 omnetpp-5.3-src-linux.tgz" | sha512sum -c --status; echo $?) -ne 0 ];
then
	sh download.sh
fi

source=(OMNeT++.desktop
	omnetpp.sh
	download.sh
	fix-configure-for-loop.patch
	omnetpp-${pkgver}-src-linux.tgz::https://www.omnetpp.org/omnetpp/send/30-omnet-releases/2325-omnetpp-${_pkgver}-linux)

sha512sums=('a5772a605592ed2db839609f8298d1d71fb9141eb1b30dac584b788414dfe49b250ba803351a3a84f90c6b89f8e09e7b129a037af17c9b94c22dff2003a5edd8'
            'facb711a01c41665c7909f82b4cee65ddee232e0c526f754ce1ab148dbc6c65abb9b24255f985be245fb2c33f91623365eac730ef83cb1a7c595a09726856fa1'
            '47f80ac3615d7df568fc3da2d57860399b873137a1663c959572d7bd2fea59421ebfad3fd7991630247ad1b7c28f9b5b87912e35f738adce2ad9b61e66d62dc0'
            'd609715e05e1c08580dce8ceba20501a00d886d2eb3462da4fc3b283415c5cca0747c6c593e207ba77388f4af72c8cf23ddf8b4b2b3d594c31daf4cab6bb21d8'
            '018b3cadb7cff592da0959b5cff3923e0d2f9a621cadd2d385e5dfc6547d992c50720f0e89f01145b2041fb58b8e3fec94dbabc832e61b3cabc49bc5d887b905')

build() {
	cd ${srcdir}/${_pkgname}-${pkgver}

	sed -i 's/WITH_OSGEARTH=yes/WITH_OSGEARTH=no/' configure.user
	patch -p1 -i ${srcdir}/fix-configure-for-loop.patch
	./configure --prefix=/opt --libdir=/opt/lib --libexecdir=/opt/lib

	PATH=${srcdir}/${_pkgname}-${pkgver}/bin:$PATH
	LD_LIBRARY_PATH=${srcdir}/${_pkgname}-${pkgver}/lib:$LD_LIBRARY_PATH
	make
}

package() {
	# Install build to /opt
	cd ${srcdir}
	mkdir -p "${pkgdir}"/opt 
	mv  "${_pkgname}-${pkgver}" ${pkgdir}/opt/${_pkgname} || return 1

	# run OMNeT++ as a normal user
	touch ${pkgdir}/opt/${_pkgname}/ide/error.log
	chmod 777 ${pkgdir}/opt/${_pkgname}/ide/error.log

	# copy profile.d file
	mkdir -p ${pkgdir}/etc/profile.d/
	cp omnetpp.sh ${pkgdir}/etc/profile.d/
	
	# copy desktop shortcut
	mkdir -p ${pkgdir}/usr/share/applications/
	cp OMNeT++.desktop ${pkgdir}/usr/share/applications/

	# Install License
	cd ${pkgdir}/opt/${_pkgname}/doc
	install -D -m644 License "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

