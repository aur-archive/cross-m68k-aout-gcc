# Contributor: SebRmv <sbriais at-symbol free dot fr> 
# PKGBUILD based on cross-mipsel-linux-gnu-gcc
# (made by: Vojtech Horky <vojta . horky at-symbol seznam . cz>)
pkgname=cross-m68k-aout-gcc
_pkgname=gcc
_target="m68k-aout"
pkgver=3.4.6
pkgrel=3
pkgdesc="The GNU Compiler Collection for the Motorola M68000 architecture"
url="http://www.gnu.org/software/gcc/"
arch=('i686' 'x86_64')
license=('GPL')
depends=('mpfr' 'sh' "cross-m68k-aout-binutils" 'cloog' 'ppl')
options=('!ccache' '!distcc' '!emptydirs' '!libtool' '!strip' 'staticlibs')
source=("ftp://ftp.gnu.org/gnu/gcc/gcc-${pkgver}/${_pkgname}-core-${pkgver}.tar.bz2")
sha1sums=('5f41c5e31fdc04e83fa16b07729866d53ffaadce')
_sysroot="/usr/lib/cross-${_target}"

prepare() {
	cd ${srcdir}/${_pkgname}-${pkgver}

patch -p0 <<EOF
--- gcc/config/m68k/m68k.md	2012-04-27 15:26:19.031848444 +0200
+++ gcc/config/m68k/m68k.md	2012-04-27 15:28:37.191849063 +0200
@@ -3679,7 +3679,7 @@
       target = operand_subword_force (operands[0], 0, SFmode);
       result = expand_binop (SImode, xor_optab,
 			     operand_subword_force (operands[1], 0, SFmode),
-			     GEN_INT (0x80000000), target, 0, OPTAB_WIDEN);
+			     GEN_INT (-2147483647 - 1), target, 0, OPTAB_WIDEN);
       if (result == 0)
 	abort ();
 
@@ -3723,7 +3723,7 @@
       target = operand_subword (operands[0], 0, 1, DFmode);
       result = expand_binop (SImode, xor_optab,
 			     operand_subword_force (operands[1], 0, DFmode),
-			     GEN_INT (0x80000000), target, 0, OPTAB_WIDEN);
+			     GEN_INT (-2147483647 - 1), target, 0, OPTAB_WIDEN);
       if (result == 0)
 	abort ();
 
@@ -7170,7 +7170,7 @@
       target = operand_subword (operands[0], 0, 1, XFmode);
       result = expand_binop (SImode, xor_optab,
 			     operand_subword_force (operands[1], 0, XFmode),
-			     GEN_INT (0x80000000), target, 0, OPTAB_WIDEN);
+			     GEN_INT (-2147483647 - 1), target, 0, OPTAB_WIDEN);
       if (result == 0)
 	abort ();
EOF
}

build() {
	cd ${srcdir}/${_pkgname}-${pkgver}

        CFLAGS=${CFLAGS//-D_FORTIFY_SOURCE=?/}
        export CFLAGS
        
        CPPFLAGS=${CPPFLAGS//-D_FORTIFY_SOURCE=?/}
        export CPPFLAGS
	
	./configure \
		"--prefix=${_sysroot}" \
		"--bindir=/usr/bin" "--program-prefix=${_target}-" \
		"--target=${_target}" "--build=$CHOST" "--host=$CHOST" \
		--with-gnu-as --with-gnu-ld \
		--disable-nls --disable-threads \
		--enable-languages=c \
		--disable-libgcj \
		--disable-shared \
		|| return 1
	
	make all-gcc "inhibit_libc=true" || return 1
}

package() {
	cd ${srcdir}/${_pkgname}-${pkgver}
	
	make DESTDIR=${pkgdir} install-gcc || return 1
	
	msg "Removing duplicit files..."
	# remove these files as they are already in the system
	# (with native gcc)
	rm -Rf ${pkgdir}${_sysroot}/{man,info}
	# remove conflicting binaries
	find ${pkgdir}/usr/bin/ -type f -not -name 'm68k-aout*' -delete
	
	msg "Creating out-of-path executables..."
	# symlink executables to single directory with no-arch-prefix name
	mkdir -p ${pkgdir}/usr/bin/cross/${_target}/;
	cd ${pkgdir}/usr/bin/cross/${_target}/;
	for bin in ${pkgdir}/usr/bin/${_target}-*; do
		bbin=`basename "$bin"`;
		ln -s "/usr/bin/${bbin}" `echo "$bbin" | sed "s#^${_target}-##"`;
	done
}
