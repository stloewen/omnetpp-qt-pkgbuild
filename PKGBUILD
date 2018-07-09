# Maintainer: Bruno Silva <brunofernandes at ua dot pt>
# Contributor: Stefan Loewen <stefan.loewen@gmail.com>

pkgname='omnetpp-qt'
pkgver=5.2.1
_pkgver=5-2-1
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

if [ $(echo "b69a6bbd1994ddf5c57cf6c55391578c8ae7d9bba59c30372a005e8e521f1db4fc53d090ea701ac77632cae83865efef258bbf24b10753e57e9ffa9bedf0f6ac omnetpp-5.2.1-src-linux.tgz" | sha512sum -c --status; echo $?) -ne 0 ];
then
	sh download.sh
fi

source=(OMNeT++.desktop
	omnetpp.sh
	download.sh
	fix-configure-for-loop.patch
	omnetpp-${pkgver}-src-linux.tgz::https://omnetpp.org/component/jdownloads/send/32-release-older-versions/2321-omnetpp-${_pkgver}-linux)

sha512sums=('a5772a605592ed2db839609f8298d1d71fb9141eb1b30dac584b788414dfe49b250ba803351a3a84f90c6b89f8e09e7b129a037af17c9b94c22dff2003a5edd8'
            'facb711a01c41665c7909f82b4cee65ddee232e0c526f754ce1ab148dbc6c65abb9b24255f985be245fb2c33f91623365eac730ef83cb1a7c595a09726856fa1'
            '06833536ca7f87ac4ca69cbfb855fdc8ba5020584b7ecfc722dd868657eb5eda5e544ae0292b5eaf74072da290f3d0e374c1ba396230a6ed848e9bc34a78a772'
            'd609715e05e1c08580dce8ceba20501a00d886d2eb3462da4fc3b283415c5cca0747c6c593e207ba77388f4af72c8cf23ddf8b4b2b3d594c31daf4cab6bb21d8'
            'b69a6bbd1994ddf5c57cf6c55391578c8ae7d9bba59c30372a005e8e521f1db4fc53d090ea701ac77632cae83865efef258bbf24b10753e57e9ffa9bedf0f6ac')

build() {
	cd ${srcdir}/${_pkgname}-${pkgver}

	sed -i 's/WITH_OSGEARTH=yes/WITH_OSGEARTH=no/' configure.user
	patch -p1 -i ${srcdir}/fix-configure-for-loop.patch
	./configure --prefix=/opt --libdir=/opt/lib --libexecdir=/opt/lib

	PATH=${srcdir}/${_pkgname}-${pkgver}/bin:$PATH
	LD_LIBRARY_PATH=${srcdir}/${_pkgname}-${pkgver}/lib:$LD_LIBRARY_PATH
	make -j 4
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

