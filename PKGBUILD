# Maintainer: Natalia Fenclová <natalia.fenclova@keemail.me>
# Contributor: Marc Boocha <marcboocha@gmail.com>

# Based on the x86_64-elf-gcc PKGBUILD

_target=x86_64-elf
pkgname=$_target-gcc-large
pkgver=9.1.0
pkgrel=1
pkgdesc='The GNU Compiler Collection - cross compiler for x86_64-elf target (large code model libgcc)'
arch=(x86_64)
url='http://gcc.gnu.org/'
license=(GPL LGPL)
depends=($_target-binutils zlib libmpc mpfr gmp)
options=(!emptydirs)
conflicts=($_target-gcc)
source=("https://mirrors.kernel.org/gnu/gcc/gcc-$pkgver/gcc-$pkgver.tar.xz"
	"libiberty-ignore-cflags.patch")
sha256sums=('79a66834e96a6050d8fe78db2c3b32fb285b230b855d0a66288235bc04b327a0'
            '8b2aea00e98f7c311b1d0fb14e4b435a03c65fde32bc992c924edb6fa7b83c9c')
_basedir=gcc-$pkgver

prepare() {
	cd $_basedir
	
	patch -p1 -i $srcdir/libiberty-ignore-cflags.patch

	mkdir $srcdir/gcc-build
}

build() {
	cd gcc-build

	$srcdir/$_basedir/configure \
		--target=$_target \
		--libexecdir=/usr/lib \
		--prefix=/usr \
		--disable-nls \
		--disable-plugin \
		--enable-languages=c,c++ \
		--without-headers

	make all-gcc
	make all-target-libgcc CFLAGS_FOR_TARGET+='-mcmodel=large -mno-red-zone'
}

check() {
	cd gcc-build

	# increase stack size to prevent test failures
	# http://gcc.gnu.org/bugzilla/show_bug.cgi?id=31827
	ulimit -s 32768

	# do not abort on error as some are "expected"
	make -k check || true
	$srcdir/$_basedir/contrib/test_summary
}

package() {
	cd gcc-build

	make DESTDIR="$pkgdir" install-gcc
	make DESTDIR="$pkgdir" install-target-libgcc

	# Remove info and make since it expected already present by host compiler
	rm -vr "$pkgdir"/usr/share/{info,man}
}

