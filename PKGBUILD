# Maintainer: Thaodan <theodorstormgrade@gmail.com>
# Contributor: Weng Xuetian <wengxt@gmail.com>


# enable this if you run out of memory during linking
#_lowmem=true

# enable gtk3 wayland (experimental)
_gtk3_wayland=false

# try to build with PGO
# currently broken
_pgo=true

# globalmenu
# to support globalmenu a patch from ubuntu is applied
# source:
# http://bazaar.launchpad.net/~mozillateam/firefox/firefox-trunk.head
# /view/head:/debian/patches/unity-menubar.patch

_pkgname=firefox
pkgname=$_pkgname-kde-opensuse
pkgver=61.0.2
pkgrel=1
pkgdesc="Standalone web browser from mozilla.org with OpenSUSE patch, integrate better with KDE"
arch=('i686' 'x86_64')
license=('MPL' 'GPL' 'LGPL')
url="https://build.opensuse.org/package/show/mozilla:Factory/MozillaFirefox"
depends=('mozilla-common' 'libxt' 'startup-notification' 'mime-types'
         'dbus-glib' 'hicolor-icon-theme'
	 'libvpx' 'icu'  'libevent' 'nss>=3.28.3' 'nspr>=4.10.6' 'hunspell'
	 'sqlite' 'libnotify' 'kmozillahelper' 'ffmpeg' 'gtk3')

makedepends=('unzip' 'zip' 'diffutils' 'python' 'yasm' 'mesa' 'imake'
             'xorg-server-xvfb' 'libpulse' 'inetutils' 'autoconf2.13' 'rust'
             'cargo' 'mercurial' 'llvm' 'clang'
             'gtk2')
if [[ -n $_pgo ]] ; then
  makedepends+=('gcc7')
fi
optdepends=('networkmanager: Location detection via available WiFi networks'
            'speech-dispatcher: Text-to-Speech')
provides=("firefox=${pkgver}")
conflicts=('firefox')
_patchrev=42510f8eee28
options=('!emptydirs'  'debug' 'strip')
_patchurl=http://www.rosenauer.org/hg/mozilla/raw-file/$_patchrev
_repo=https://hg.mozilla.org/mozilla-unified
source=("hg+$_repo#tag=FIREFOX_${pkgver//./_}_RELEASE"
        mozconfig
        firefox.desktop
        vendor.js
        kde.js
        firefox-fixed-loading-icon.png
	# Firefox patchset
	firefox-branded-icons-$_patchrev.patch::$_patchurl/firefox-branded-icons.patch
	firefox-kde-$_patchrev.patch::$_patchurl/firefox-kde.patch
	# Gecko/toolkit patchset
	mozilla-kde-$_patchrev.patch::$_patchurl/mozilla-kde.patch
	mozilla-nongnome-proxies-$_patchrev.patch::$_patchurl/mozilla-nongnome-proxies.patch
	unity-menubar.patch
	add_missing_pgo_rule.patch
        pgo_fix_missing_kdejs.patch
        https://dev.gentoo.org/~anarchy/mozilla/patchsets/firefox-61.0-patches-01.tar.xz
        https://gitweb.gentoo.org/repo/gentoo.git/plain/www-client/firefox/files/firefox-61.0-mozHunspell.patch
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

  if [ $_gtk3_wayland = true ] ; then
      echo "ac_add_options --enable-default-toolkit=cairo-gtk3-wayland" \
           >> .mozconfig
  fi
     


  echo -n "$_google_api_key" >google-api-key
  echo "ac_add_options --with-google-api-keyfile=\"$PWD/google-api-key\"" >>.mozconfig

  echo -n "$_google_default_client_id $_google_default_client_secret" >google-oauth-api-key
  echo "ac_add_options --with-google-api-keyfile=\"$PWD/google-oauth-api-key\"" >>.mozconfig

  echo -n "$_mozilla_api_key" >mozilla-api-key
  echo "ac_add_options --with-mozilla-api-keyfile=\"$PWD/mozilla-api-key\"" >>.mozconfig
  
  msg "Patching for KDE"
  patch -Np1 -i "$srcdir/mozilla-nongnome-proxies-$_patchrev.patch"
  patch -Np1 -i "$srcdir/mozilla-kde-$_patchrev.patch"

  patch -Np1 -i "$srcdir/firefox-kde-$_patchrev.patch"
  patch -Np1 -i "$srcdir/firefox-branded-icons-$_patchrev.patch"
  
  # add globalmenu support
  patch -Np1 -i "$srcdir/unity-menubar.patch"

  # add missing rule for pgo builds
  patch -Np1 -i "$srcdir"/add_missing_pgo_rule.patch

  # add missing file Makefile for pgo builds
  patch -Np1 -i "$srcdir"/pgo_fix_missing_kdejs.patch

  # Add missing include of nsCOMPtr.h, bug #660726
  patch -Np1 -i "$srcdir/"firefox-61.0-mozHunspell.patch
  
  # https://bugzilla.mozilla.org/show_bug.cgi?id=1435212
  #patch -Np1 -i ../0001-Bug-1435212-Add-support-for-FFmpeg-4.0.-r-bryce.patch

  # gentoo patches
  for file in "$srcdir"/firefox/*.patch ; do
    case $file in
      *gentoo*) : ;;
      *ffmpeg*) : ;;
      *)
        patch -Np1 -i "$file"
        ;;
    esac
  done
  
  # WebRTC build tries to execute "python" and expects Python 2
  mkdir -p "$srcdir/path"
  ln -sf /usr/bin/python2 "$srcdir/path/python"

  # Fix tab loading icon (flickers with libpng 1.6)
  # https://bugzilla.mozilla.org/show_bug.cgi?id=841734
  # TODO: Remove this; Firefox 34 might use CSS animations for the loading icon
  # https://bugzilla.mozilla.org/show_bug.cgi?id=759252
  cp "$srcdir/firefox-fixed-loading-icon.png" \
     browser/themes/linux/tabbrowser/loading.png
     
}

build() {
  #export CXXFLAGS="${CFLAGS}"
  cd mozilla-unified
  export MOZ_SOURCE_REPO="$_repo"
  

  # Hardening
  LDFLAGS+=" -Wl,-z,now"


 
  if [[ -n $_lowmem || $CARCH == i686 ]]; then
    LDFLAGS+=" -Xlinker --no-keep-memory"
  fi

  if [[ -n $_pgo ]]; then
    CC=/usr/bin/gcc-7
    CXX=/usr/bin/g++-7
    
    if in_array ccache ${BUILDENV[*]} ; then
      CC="ccache gcc-7"
      CXX="ccache g++-7"
    fi
    
    CCACHE_CC=/usr/bin/gcc-7 \
    CC=$CC\
    CXX=$CXX \
      DISPLAY=:99 MOZ_PGO=1 \
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

  # Use system-provided dictionaries
  rm -rf "$pkgdir"/usr/lib/firefox/{dictionaries,hyphenation}
  ln -s /usr/share/hunspell "$pkgdir/usr/lib/firefox/dictionaries"
  ln -s /usr/share/hyphen "$pkgdir/usr/lib/firefox/hyphenation"

  # Use system certificates
  ln -srf "$pkgdir/usr/lib/libnssckbi.so" \
     "$pkgdir/usr/lib/$_pkgname/libnssckbi.so"

  #workaround for now
  #https://bugzilla.mozilla.org/show_bug.cgi?id=658850
  ln -sf firefox "$pkgdir/usr/lib/firefox/firefox-bin"
}
md5sums=('SKIP'
         '49c2d3dc354e18ab12c8c4e6f7d68d6b'
         '14e0f6237a79b85e60256f4808163160'
         '5cee310a9040ccc5abcf29742b84aeb8'
         '05bb69d25fb3572c618e3adf1ee7b670'
         '6e335a517c68488941340ee1c23f97b0'
         'd7ce23a18da21c05cd756766e177834f'
         '9a9a8cd8da7231e3bfc57132a7863021'
         'd501f3cf592451a6d377fa748b6967b6'
         '0661e259fe57df87fca791f4aeb78da0'
         'b3c34d2c275f5373cfcbde17b74d2aa5'
         'fe24f5ea463013bb7f1c12d12dce41b2'
         '3fa8bd22d97248de529780f5797178af'
         '20f84d63538909f6b069de1b6b10f4b3'
         '13d3ece023495d23f665c78f19b70be8')
