pkgname=('dmd')
pkgdesc='The D programming language reference compiler'
pkgver=2.090.1
pkgrel=1
arch=('x86_64')
url='https://www.dlang.org'
backup=('etc/dmd.conf')
depends=('gcc' 'libphobos')
makedepends=('git')
license=('Boost')
source=("git+https://github.com/dlang/dmd.git#tag=v$pkgver"
        "git+https://github.com/dlang/druntime.git#tag=v$pkgver"
        "git+https://github.com/dlang/phobos.git#tag=v$pkgver"
        "git+https://github.com/dlang/tools.git#tag=v$pkgver"
        "http://downloads.dlang.org/releases/2.x/$pkgver/dmd.$pkgver.linux.tar.xz"
    	'dmd.conf'
        'dmd-doc.desktop'
)
sha256sums=('SKIP'
	    'SKIP'
	    'SKIP'
	    'SKIP'
	    '0720ab200e0db7b8bd613d0bd7d55720bd48a88a7e88e6dc66ec78d3b6a666ee'
        '3d639e89528fed1da90006f4dfb2b0fdc41308da5a96d953381ff4ccf257c035'
	    '4b7b8722b3fa11082f0f332397b1b66c85b30ce773c43c3fedcba5768a1484b1'
)
prepare() {
    # We only want to extract the docs & samples, not the prebuild executables
    tar xfJ "dmd.$pkgver.linux.tar.xz" dmd2/html

    # Make sure the version is not -dirty
    sed -i "s/\.git/.nope/" "$srcdir"/dmd/src/build.d
}
build() {
    cd "$srcdir"/dmd
    make -f posix.mak BUILD=release ENABLE_RELEASE=1 PIC=1 AUTO_BOOTSTRAP=1

    cd "$srcdir"/druntime
    make -f posix.mak DMD="$srcdir"/dmd/generated/linux/release/*/dmd BUILD=release ENABLE_RELEASE=1 PIC=1

    cd "$srcdir"/phobos
    make -f posix.mak DMD="$srcdir"/dmd/generated/linux/release/*/dmd BUILD=release ENABLE_RELEASE=1 PIC=1

    # This requires object.d to compile, thus need to be after druntime is built
    cd "$srcdir"/dmd
    make -C docs DMD=ldmd2 AUTO_BOOTSTRAP=1
}
package() {
    cd "$srcdir"/dmd

    install -Dm755 "$srcdir"/dmd/generated/linux/release/*/dmd "$pkgdir"/usr/bin/dmd

    mkdir -p "$pkgdir"/etc
    install -Dm644 "$srcdir"/dmd.conf "$pkgdir"/etc/dmd.conf

    mkdir -p "$pkgdir"/usr/share/man/man1
    mkdir -p "$pkgdir"/usr/share/man/man5
    cp generated/docs/man/man1/dmd.1 "$pkgdir"/usr/share/man/man1/
    cp -r generated/docs/man/man5/* "$pkgdir"/usr/share/man/man5/

    find "$pkgdir"/usr -type f | xargs chmod 0644
    chmod 755 "$pkgdir"/usr/bin/*

    mkdir -p "$pkgdir"/usr/share/applications
    install -Dm644 "$srcdir"/dmd-doc.desktop "$pkgdir"/usr/share/applications/dmd-doc.desktop

    mkdir -p "$pkgdir"/usr/share/d/samples/
    cp -r samples/* "$pkgdir"/usr/share/d/samples/

    mkdir -p "$pkgdir"/usr/share/d/html
    cp -r "$srcdir"/dmd2/html/* "$pkgdir"/usr/share/d/html/

    install -Dm644 LICENSE.txt "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
