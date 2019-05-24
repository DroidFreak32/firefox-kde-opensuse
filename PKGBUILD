# Maintainer: Thaodan <theodorstormgrade@gmail.com>
# Contributor: Weng Xuetian <wengxt@gmail.com>


# enable this if you run out of memory during linking
#_lowmem=true

# build with PGO
_pgo=true

# globalmenu
# to support globalmenu a patch from ubuntu is applied
# source:
# http://bazaar.launchpad.net/~mozillateam/firefox/firefox-trunk.head
# /view/head:/debian/patches/unity-menubar.patch

# patches from gentoo:
# https://dev.gentoo.org/~anarchy/mozilla/patchsets/firefox-67.0-patches-05.tar.xz

_pkgname=firefox
pkgname=$_pkgname-kde-opensuse
pkgver=67.0
pkgrel=1
pkgdesc="Standalone web browser from mozilla.org with OpenSUSE patch, integrate better with KDE"
arch=('i686' 'x86_64')
license=('MPL' 'GPL' 'LGPL')
url="https://build.opensuse.org/package/show/mozilla:Factory/MozillaFirefox"
depends=('mozilla-common' 'libxt' 'startup-notification' 'mime-types'
         'dbus-glib' 'hicolor-icon-theme'
	 'libvpx' 'icu'  'libevent' 'nss>=3.28.3' 'nspr>=4.10.6' 'hunspell'
	 'sqlite' 'libnotify' 'kmozillahelper' 'ffmpeg' 'gtk3'
         # system av1
         'dav1d' 'aom'
         # system harfbuzz
         'harfbuzz'
         # system graphite
         'graphite')

makedepends=('unzip' 'zip' 'diffutils' 'python2-setuptools' 'yasm' 'mesa' 'imake'
             'xorg-server-xvfb' 'libpulse' 'inetutils' 'autoconf2.13' 'rust'
             'cargo' 'mercurial' 'llvm' 'clang'
             'gtk2' 'nodejs' 'cbindgen' 'nasm' 'python' 'python2-psutil' 'xz')

optdepends=('networkmanager: Location detection via available WiFi networks'
            'speech-dispatcher: Text-to-Speech'
            'pulseaudio: Audio support')
provides=("firefox=${pkgver}")
conflicts=('firefox')
_patchrev=9c008b241362
options=('!emptydirs')
_patchurl=http://www.rosenauer.org/hg/mozilla/raw-file/$_patchrev
_repo=https://hg.mozilla.org/mozilla-unified
source=("hg+$_repo#tag=FIREFOX_${pkgver//./_}_RELEASE"
        mozconfig
        firefox.desktop
        vendor.js
        kde.js
	# Firefox patchset
	firefox-branded-icons-$_patchrev.patch::$_patchurl/firefox-branded-icons.patch
	firefox-kde-$_patchrev.patch::$_patchurl/firefox-kde.patch
	# Gecko/toolkit patchset
	mozilla-kde-$_patchrev.patch::$_patchurl/mozilla-kde.patch
	mozilla-nongnome-proxies-$_patchrev.patch::$_patchurl/mozilla-nongnome-proxies.patch
	unity-menubar.patch
	add_missing_pgo_rule.patch
        pgo_fix_missing_kdejs.patch
        2001_system_graphite2_support.patch
        2000_system_harfbuzz_support.patch
        # pgo fixes
        mozilla-1516803.patch
        mozilla-1516081.patch
        pgo.patch
        # https://bugzilla.mozilla.org/show_bug.cgi?id=1521249
        2009_rust-1.33-support.patch.xz
        2010_rust-1.33-support.patch.xz
        2011_rust-1.33-support.patch.xz
        # use sytem av1
        7002_system_av1_support.patch
        # https://bugzilla.mozilla.org/show_bug.cgi?id=1542958
        # note: fixes compile errrors when using elf migrartion
        2014_spectre_variant2_bug1542958.patch
        2015_spectre_variant2_bug1542958.patch
)

# Google API keys (see http://www.chromium.org/developers/how-tos/api-keys)
# Note: These are for Arch Linux use ONLY. For your own distribution, please
# get your own set of keys. Feel free to contact foutrelis@archlinux.org for
# more information.
_google_api_key=AIzaSyDwr302FpOSkGRpLlUpPThNTDPbXcIn_FM
_google_default_client_id=413772536636.apps.googleusercontent.com
_google_default_client_secret=0ZChLK6AxeA3Isu96MkwqDR4


# Mozilla API keys (see https://location.services.mozilla.com/api)
# Note: These are for Arch Linux use ONLY. For your own distribution, please
# get your own set of keys. Feel free to contact heftig@archlinux.org for
# more information.
_mozilla_api_key=16674381-f021-49de-8622-3021c5942aff

prepare() {
  cd mozilla-unified

  cp "$srcdir/mozconfig" .mozconfig

  echo -n "$_google_api_key" >google-api-key
  echo "ac_add_options --with-google-location-service-api-keyfile=\"$PWD/google-api-key\"" >>.mozconfig

  echo -n "$_google_default_client_id $_google_default_client_secret" >google-oauth-api-key
  echo "ac_add_options --with-google-safebrowsing-api-keyfile=\"$PWD/google-oauth-api-key\"" >>.mozconfig

  echo -n "$_mozilla_api_key" >mozilla-api-key
  echo "ac_add_options --with-mozilla-api-keyfile=\"$PWD/mozilla-api-key\"" >>.mozconfig
  
  msg "Patching for KDE"
  patch -Np1 -i "$srcdir/mozilla-nongnome-proxies-$_patchrev.patch"
  patch -Np1 -i "$srcdir/mozilla-kde-$_patchrev.patch"

  patch -Np1 -i "$srcdir/firefox-kde-$_patchrev.patch"
  patch -Np1 -i "$srcdir/firefox-branded-icons-$_patchrev.patch"
  
  # add globalmenu support
  patch -Np1 -i "$srcdir/unity-menubar.patch"
  
  patch -Np1 -i "$srcdir"/2000_system_harfbuzz_support.patch
  patch -Np1 -i "$srcdir"/2001_system_graphite2_support.patch
  patch -Np1 -i "$srcdir"/7002_system_av1_support.patch

  # https://bugzilla.mozilla.org/show_bug.cgi?id=1521249
  # patch -Np1 -i ../0001-bz-1468911.patch patch is to large for the AUR
  xzcat "$srcdir"/2009_rust-1.33-support.patch.xz | patch -Np1
  xzcat "$srcdir"/2010_rust-1.33-support.patch.xz | patch -Np1
  xzcat "$srcdir"/2011_rust-1.33-support.patch.xz | patch -Np1

  
  # https://bugzilla.mozilla.org/show_bug.cgi?id=1542958
  # note: fixes compile errrors when using elf migrartion
  patch -Np1 -i  "$srcdir"/2014_spectre_variant2_bug1542958.patch
  patch -Np1 -i  "$srcdir"/2015_spectre_variant2_bug1542958.patch
  
  if [[ $_pgo ]] ; then
    # https://bugzilla.mozilla.org/show_bug.cgi?id=1516803
    patch -Np1 -i  "$srcdir"/mozilla-1516803.patch
    # https://bugzilla.mozilla.org/show_bug.cgi?id=1516081
    patch -Np1 -i  "$srcdir"/mozilla-1516081.patch

    # add missing rule for pgo builds
    patch -Np1 -i "$srcdir"/add_missing_pgo_rule.patch

    patch -Np1 -i "$srcdir"/pgo.patch

    # add missing file Makefile for pgo builds
    patch -Np1 -i "$srcdir"/pgo_fix_missing_kdejs.patch

    echo "ac_add_options --enable-lto" >> .mozconfig
    echo "ac_add_options --disable-elf-hack" >> .mozconfig
  fi
}

build() {
  #export CXXFLAGS="${CFLAGS}"
  cd mozilla-unified
  export MOZ_SOURCE_REPO="$_repo"
  export MOZBUILD_STATE_PATH="$srcdir/mozbuild"
  export MOZ_APP_REMOTINGNAME=$pkgname

  export CC=gcc
  export CXX=g++
  export AR="gcc-ar"
  export NM="gcc-nm"
  export RANLIB="gcc-ranlib"

  export MOZ_MAKE_FLAGS="$MAKEFLAGS"
  export STRIP=/bin/true

  if [[ -n $_lowmem || $CARCH == i686 ]]; then
    LDFLAGS+=" -Xlinker --no-keep-memory"
  fi

  if [[ -n $_pgo ]]; then
    export DISPLAY=:99
    export MOZ_PGO=1
    
    xvfb-run \
      -a \
      -s "-extension GLX -screen 0 1280x1024x24" \
      ./mach build
  else
    ./mach build
  fi
  ./mach buildsymbols
}

package() {
  cd mozilla-unified

  [[ "$CARCH" == "i686" ]] && cp "$srcdir/kde.js" obj-i686-pc-linux-gnu/dist/bin/defaults/pref
  [[ "$CARCH" == "x86_64" ]] && cp "$srcdir/kde.js" obj-x86_64-pc-linux-gnu/dist/bin/defaults/pref

  DESTDIR="$pkgdir" ./mach install

  install -Dm644 "$srcdir/vendor.js" "$pkgdir/usr/lib/firefox/browser/defaults/preferences/vendor.js"
  install -Dm644 "$srcdir/kde.js" "$pkgdir/usr/lib/firefox/browser/defaults/preferences/kde.js"

  _distini="$pkgdir/usr/lib/firefox/distribution/distribution.ini"
  install -Dm644 /dev/stdin "$_distini" <<END
[Global]
id=archlinux
version=1.0
about=Mozilla Firefox for Arch Linux

[Preferences]
app.distributor=archlinux
app.distributor.channel=$pkgname
app.partner.archlinux=archlinux
END

  for i in 16 22 24 32 48 64 128 256; do
      install -Dm644 browser/branding/official/default$i.png \
        "$pkgdir/usr/share/icons/hicolor/${i}x${i}/apps/firefox.png"
  done

  install -Dm644 browser/branding/official/content/about-logo.png \
    "$pkgdir/usr/share/icons/hicolor/192x192/apps/firefox.png"
  install -Dm644 browser/branding/official/content/about-logo@2x.png \
    "$pkgdir/usr/share/icons/hicolor/384x384/apps/firefox.png"

  install -Dm644 "$srcdir/firefox.desktop" "$pkgdir/usr/share/applications/firefox.desktop"

  #workaround for now
  #https://bugzilla.mozilla.org/show_bug.cgi?id=658850
  ln -sf firefox "$pkgdir/usr/lib/firefox/firefox-bin"
}
md5sums=('SKIP'
         '15a351d5936fb76a779d3afa2baa1308'
         '14e0f6237a79b85e60256f4808163160'
         '5cee310a9040ccc5abcf29742b84aeb8'
         '05bb69d25fb3572c618e3adf1ee7b670'
         'd7ce23a18da21c05cd756766e177834f'
         '1fd3db31015977eb77960716d6d2dbe6'
         '270eed48793b9358702e7de6185c32be'
         '8b24c694c259064b1296e7a1397c3aee'
         '4cd539459e253f53f631ccca51650a87'
         'fe24f5ea463013bb7f1c12d12dce41b2'
         '3fa8bd22d97248de529780f5797178af'
         '554514bf00a7927a85280f19e52a55fb'
         '5b0d39aebb1ce84f89bda300320b851f'
         'becf6bf9ceb6008401832c855ccadff9'
         '79d27c8896913c7d87b148240995ab69'
         'f867ae41a722630cc5567e2dcc51676d'
         'ef358dae95d487740dda8114df4d5f90'
         'f343dc6520abb0be8eb77da17256ce21'
         '4594ded5cdcdaae038bc49645643f914'
         'df439e02304d302009c320a540f01dbe'
         'f0e6dccbb32695d2d9057dacd650822c'
         'cca2f77b28b5fd53cbc919045006ab0a')
