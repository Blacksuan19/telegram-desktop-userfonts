# Maintainer: Andy Kluger <https://t.me/andykluger>
# Contributor: Sven-Hendrik Haase <svenstaro@gmail.com>
# Contributor: hexchain <i@hexchain.org>

pkgname=telegram-desktop-userfonts
pkgver=2.4.7
pkgrel=1
conflicts=('telegram-desktop')
provides=('telegram-desktop')
pkgdesc='Official Telegram Desktop client, with your fonts as set by fontconfig'
arch=('x86_64')
url="https://desktop.telegram.org/"
license=('GPL3')
depends=('hunspell' 'ffmpeg' 'hicolor-icon-theme' 'lz4' 'minizip' 'openal'
         'qt5-imageformats' 'xxhash' 'libdbusmenu-qt5' 'qt5-wayland' 'gtk3')
makedepends=('cmake' 'git' 'ninja' 'python' 'range-v3' 'tl-expected' 'microsoft-gsl' 'libwebrtc')
optdepends=('ttf-opensans: default Open Sans font family')
source=("https://github.com/telegramdesktop/tdesktop/releases/download/v${pkgver}/tdesktop-${pkgver}-full.tar.gz"
"Use-tg_owt-webrtc-fork.patch"
"Update-webrtc-packaged-build-for-tg_owt.patch::https://github.com/desktop-app/cmake_helpers/commit/d955882cb4d4c94f61a9b1df62b7f93d3c5bff7d.patch"
"Add_external_jpeg.patch::https://github.com/desktop-app/cmake_helpers/commit/ed9fa2e798a1f175840479417d760c51181959b8.patch"
)
sha512sums=('712ab6896f89f7df0c7ac297039ee3b3532c159e17f66e4539b701a35d04d4709b558755d592d3cd91df541a2d2ca9f0485cf073c32f0b69a18848ab2ccd1993'
            '071591c6bb71435f8186dcaf570703718051f00366dbbe3f13c4df3706d3de1f168bff4bfa707ad1d6f09f5505c925f0b01d76fd65efe904f3ba7db693d63f43'
            'b3c44e76a3907f7acc197746b471564577e912bf0561e9576dc8459211c88f400716437bcaa10967376461c69c8a98a56477d26d3feb9ca34747d9208bf5f6c6'
            '3891f191f720e77d463365d1415ff8c20866d0d898909dcbe757d334c582c38975d47c33e82ae54e3cfbce7f46c257e9f2eb76b673a76c37446ecf1e9a9c681b')

prepare() {
    cd tdesktop-$pkgver-full
    for ttf in Telegram/lib_ui/fonts/*.ttf; do
        rm $ttf
        touch $ttf
    done
    sed -i 's/DemiBold/Bold/g' Telegram/lib_ui/ui/style/style_core_font.cpp

    cd cmake
    patch -R -Np1 -i ${srcdir}/Add_external_jpeg.patch
    patch -R -Np1 -i ${srcdir}/Update-webrtc-packaged-build-for-tg_owt.patch
    patch -R -Np1 -i ${srcdir}/Use-tg_owt-webrtc-fork.patch
    sed 's|set(webrtc_build_loc ${webrtc_loc}/out/$<CONFIG>/obj)|set(webrtc_build_loc /usr/lib)|' -i external/webrtc/CMakeLists.txt
}

build() {
    cd tdesktop-$pkgver-full

    # Turns out we're allowed to use the official API key that telegram uses for their snap builds:
    # https://github.com/telegramdesktop/tdesktop/blob/8fab9167beb2407c1153930ed03a4badd0c2b59f/snap/snapcraft.yaml#L87-L88
    # Thanks @primeos!
    cmake . \
        -B build \
        -G Ninja \
        -DCMAKE_INSTALL_PREFIX="/usr" \
        -DCMAKE_BUILD_TYPE=Release \
        -DTDESKTOP_API_ID=611335 \
        -DTDESKTOP_API_HASH=d524b414d21f4d37f08684c1df41ac9c \
        -DDESKTOP_APP_USE_PACKAGED_FONTS=OFF \
        -DTDESKTOP_DISABLE_REGISTER_CUSTOM_SCHEME=ON \
        -DTDESKTOP_LAUNCHER_BASENAME="telegramdesktop" \
        -DDESKTOP_APP_SPECIAL_TARGET="" \
        -DDESKTOP_APP_WEBRTC_LOCATION=/usr/include/libwebrtc
    ninja -C build
}

package() {
    cd tdesktop-$pkgver-full
    DESTDIR=$pkgdir ninja -C build install
}
