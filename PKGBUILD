# Maintainer: Andy Kluger <https://t.me/andykluger>
# Upstream PKGBUILD (community repo):
# Maintainer: Sven-Hendrik Haase <svenstaro@gmail.com>
# Contributor: hexchain <i@hexchain.org>
pkgname=telegram-desktop-userfonts
pkgver=3.0.0
pkgrel=1
conflicts=('telegram-desktop')
provides=('telegram-desktop')
pkgdesc='Official Telegram Desktop client, with your fonts as set by fontconfig'
arch=('x86_64')
url="https://desktop.telegram.org/"
license=('GPL3')
depends=('hunspell' 'ffmpeg' 'hicolor-icon-theme' 'lz4' 'minizip' 'openal' 'ttf-opensans'
         'qt5-imageformats' 'qt5-svg' 'xxhash' 'libdbusmenu-qt5' 'kwayland' 'glibmm'
         'rnnoise' 'pipewire' 'libxtst' 'libxrandr' 'jemalloc' 'libtg_owt')
makedepends=('cmake' 'git' 'ninja' 'python' 'range-v3' 'tl-expected' 'microsoft-gsl'
             'extra-cmake-modules' 'gtk3' 'webkit2gtk')
optdepends=('gtk3: GTK environment integration'
            'webkit2gtk: embedded browser features'
            'xdg-desktop-portal: desktop integration')
source=("https://github.com/telegramdesktop/tdesktop/releases/download/v${pkgver}/tdesktop-${pkgver}-full.tar.gz")
sha512sums=('2439de9e11fa6150f4306a0da79a20b1552e4432cf669764669e4bf2eca7311b031e8b22c23cde1efe640e1ef2ac71916fd7151d31b475cbaabc78d79ee50415')

prepare() {
    cd tdesktop-$pkgver-full
    for ttf in Telegram/lib_ui/fonts/*.ttf; do
        rm $ttf
        touch $ttf
    done
    sed -i 's/DemiBold/Bold/g' Telegram/lib_ui/ui/style/style_core_font.cpp
}

build() {
    cd tdesktop-$pkgver-full

    # Turns out we're allowed to use the official API key that telegram uses for their snap builds:
    # https://github.com/telegramdesktop/tdesktop/blob/8fab9167beb2407c1153930ed03a4badd0c2b59f/snap/snapcraft.yaml#L87-L88
    # Thanks @primeos!
    cmake \
        -B build \
        -G Ninja \
        -DCMAKE_INSTALL_PREFIX="/usr" \
        -DCMAKE_BUILD_TYPE=Release \
        -DTDESKTOP_API_ID=611335 \
        -DDESKTOP_APP_USE_PACKAGED_FONTS=OFF \
        -DTDESKTOP_API_HASH=d524b414d21f4d37f08684c1df41ac9c
    ninja -C build
}

package() {
    cd tdesktop-$pkgver-full
    DESTDIR=$pkgdir ninja -C build install
}
