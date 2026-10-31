# Maintainer: crueter <crueter at crueter dot x y z>
# Contributor: Kaydax <contact+aur@kaydax.xyz>
# Contributor: Yellow <yellow@example.com>
# Contributor: Lenny McLennington <lennymclennington@protonmail.com>
# Contributor: Sefa Eyeoglu <contact@scrumplex.net>
# Contributor: Elijah Gregg <lovetocode999@tilde.team>
# Contributor: Miko <mikoxyzzz@gmail.com>
# Contributor: Cheru Berhanu <aur attt cheru doot dev>
# Contributor: dada513 <dada513@protonmail.com>
# Contributor: flatrick <patrikm87 at gmail>

pkgname=polymc
pkgver=8.1
pkgrel=2
pkgdesc="Minecraft launcher with the ability to manage multiple instances"
arch=('x86_64')
url="https://github.com/PolyMC/PolyMC"
license=('GPL3')
depends=('java-runtime' 'libgl' 'qt6-base' 'qt6-5compat' 'qt6-svg'
         'qt6-imageformats' 'zlib' 'hicolor-icon-theme' 'quazip-qt6' 'qt6-charts')
makedepends=('cmake' 'extra-cmake-modules' 'java-environment>=8'
             'java-environment<26' 'scdoc')
optdepends=('glfw: to use system GLFW libraries'
            'openal: to use system OpenAL libraries'
            'visualvm: Profiling support'
            'xorg-xrandr: for older minecraft versions'
)
source=("https://github.com/PolyMC/PolyMC/releases/download/$pkgver/PolyMC-$pkgver.tar.gz")
sha256sums=('217a80e1bc3412239b3a45e4fc45f3308388dd75d7a4aafdd0a75c12590ac7d3')

build() {
  local java_home java_version java_major
  local newest_java_home='' newest_java_major=0

  for java_home in /usr/lib/jvm/*; do
    [[ -d $java_home && -x $java_home/bin/java && -x $java_home/bin/javac &&
      -x $java_home/bin/jar ]] || continue

    java_version=$("$java_home/bin/javac" -version 2>&1) || continue
    java_version=${java_version##*$'\n'}
    [[ $java_version =~ ^javac[[:space:]]+([^[:space:]]+)$ ]] || continue
    java_version=${BASH_REMATCH[1]}

    if [[ $java_version =~ ^1\.([0-9]+)([._+-].*)?$ ]]; then
      java_major=${BASH_REMATCH[1]}
    elif [[ $java_version =~ ^([0-9]+)([._+-].*)?$ ]]; then
      java_major=${BASH_REMATCH[1]}
    else
      continue
    fi

    java_major=$((10#$java_major))
    if ((java_major >= 8 && java_major < 26 &&
      java_major > newest_java_major)); then
      newest_java_home=$java_home
      newest_java_major=$java_major
    fi
  done

  if [[ -z $newest_java_home ]]; then
    printf 'error: no compatible JDK found in /usr/lib/jvm (requires major 8 through 25)\n' >&2
    return 1
  fi

  local JAVA_HOME="$newest_java_home"
  local PATH="$JAVA_HOME/bin:$PATH"
  export JAVA_HOME PATH

  cmake -B build -S PolyMC-$pkgver \
      -DLauncher_BUILD_PLATFORM='archlinux' \
      -DLauncher_QT_VERSION_MAJOR='6' \
      -DCMAKE_BUILD_TYPE='None' \
      -DCMAKE_INSTALL_PREFIX='/usr' \
      -DJava_JAVA_EXECUTABLE="$JAVA_HOME/bin/java" \
      -DJava_JAVAC_EXECUTABLE="$JAVA_HOME/bin/javac" \
      -DJava_JAR_EXECUTABLE="$JAVA_HOME/bin/jar" \
      -Wno-dev
  cmake --build build
}

package() {
  DESTDIR="$pkgdir" cmake --install build
}
