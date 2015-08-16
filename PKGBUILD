# Maintainer: Lone_Wolf <lonewolf@xs4all.nl>
# Contributor : Jan de Groot <jgc@archlinux.org>
# Contributor: Andreas Radke <andyrtr@archlinux.org>
# Adapted from offical archlinux mesa package

pkgname=mesa-r600-git
pkgver=20110912
pkgrel=1
_realver=7.12
pkgdesc="Mesa R600 gallium for Radeon HD 2xxx and later - git version.If you live in the US, you should delete --enable-texture-float \ line."
arch=('x86_64')
depends=('libxt' 'libxxf86vm' 'libxdamage' 'xorg-server' 'libffi' 'libxv')
makedepends=('pkgconfig' 'python2' 'talloc' 'libxml2' 'imake' 'git' 'glproto' 'dri2proto>=2.6' 'llvm' 'xorg-server-devel' 'libxvmc' 'libvdpau')
optdepends=('libtxc_dxtn: S3TC support'
            'mesa-demos: glxinfo and glxgears')
provides=(mesa=${_realver} libgl=${_realver} ati-dri=${_realver} libglapi=${_realver})
replaces=(mesa libgl ati-dri libglapi)
conflicts=('xf86-video-ati<6.9.0-6' mesa libgl ati-dri libglapi)
url="http://mesa3d.sourceforge.net"
license=(custom)
source=(LICENSE)
md5sums=('5c65a0fe315dd347e09b1f2826a1df5a')

_gitroot='git://anongit.freedesktop.org/git/mesa/mesa'
_gitname='mesa'

build() {
  msg 'Connecting to git.freedesktop.org GIT server....'
  if [ -d ${_gitname} ] ; then
    cd ${_gitname} && git pull origin
  else
    git clone ${_gitroot}
  fi
  msg 'GIT checkout done or server timeout'
  msg 'Starting make...'

  cd "${srcdir}"

  # Cleanup and prepare the build dir
  [ -d build ] && rm -rf build
  cp -r ${_gitname} build
  cd build
    ./autogen.sh --prefix=/usr \
    --with-dri-driverdir=/usr/lib/xorg/modules/dri \
    --with-gallium-drivers=r300,r600,swrast \
    --with-dri-drivers=r300,r600,swrast \
    --enable-texture-float \
    --enable-glx-tls \
    --enable-xcb \
    --enable-shared-dricore \
    --enable-gbm \
    --enable-gallium-gbm \
    --enable-xvmc \
    --enable-vdpau \
    --enable-gallium-g3dvl \
    --enable-shared-glapi \
    --enable-osmesa \
    --enable-xorg \
# left out compile flags
# default = auto 
#  --enable-32-bit	   build 32-bit libraries [default=auto]
#  --enable-64-bit	   build 64-bit libraries [default=auto]
#  --enable-dri            enable DRI modules [default=auto]
#  --enable-glx            enable GLX library [default=auto]
#  --disable-driglx-direct enable direct rendering in GLX and EGL for DRI
#                          [default=auto]
#
# default = enabled
#  --disable-shared	   build shared libraries [default=enabled]
#  --disable-asm	   disable assembly usage [default=enabled on supported plaforms]
#  --disable-pic	   compile PIC objects [default=enabled for shared builds on supported platforms]
#  --disable-egl           disable EGL library [default=enabled]
#  --disable-glu           enable OpenGL Utility library [default=enabled]
#  --enable-gallium-llvm   build gallium LLVM support [default=enabled on
#                          x86/x86_64]
#
#
# default = disabled
#  --enable-selinux	   Build SELinux-aware Mesa [default=disabled]
#  --disable-opengl	   disable support for standard OpenGL API [default=no]
# gles1 , gles2 & openvg are for embedded and handhelds devices, this package targets desktops and laptops
#  --enable-gles1          enable support for OpenGL ES 1.x API [default=no]
#  --enable-gles2          enable support for OpenGL ES 2.x API [default=no]
#  --enable-openvg         enable support for OpenVG API [default=no]
#  --enable-d3d1x          enable support for Direct3D 10 & 11 low-level API [default=no]
#  --enable-xa             enable build of the XA X Acceleration API [default=no]
#  --enable-xlib-glx       make GLX library Xlib-based instead of DRI-based [default=disable]
#  --enable-gallium-egl    enable optional EGL state tracker 
#			   (not required for EGL support in Gallium with OpenGL and OpenGL ES)
#                          [default=disable]
  make
}

package() {

# Mesa
  cd "${srcdir}/build" 
  make DESTDIR="${pkgdir}" install

  rm -f "${pkgdir}/usr/lib/libGL.so"*
  rm -f "${pkgdir}/usr/lib/libGLESv"*
  rm -f "${pkgdir}/usr/lib/libEGL"*
  rm -rf "${pkgdir}/usr/lib/egl"
  rm -f ${pkgdir}/usr/lib/pkgconfig/{glesv1_cm.pc,glesv2.pc,egl.pc}
  rm -rf "${pkgdir}/usr/lib/xorg"
  rm -f "${pkgdir}/usr/include/GL/glew.h"
  rm -f "${pkgdir}/usr/include/GL/glxew.h"
  rm -f "${pkgdir}/usr/include/GL/wglew.h"
  rm -f "${pkgdir}/usr/include/GL/glut.h"
  rm -rf ${pkgdir}/usr/include/{GLES,GLES2,EGL,KHR}
# libgl
  cd "${srcdir}/build" 
  install -m755 -d "${pkgdir}/usr/lib"
  install -m755 -d "${pkgdir}/usr/lib/xorg/modules/extensions"
  bin/minstall lib/libGL.so* "${pkgdir}/usr/lib/"
  bin/minstall lib/libdricore.so* "${pkgdir}/usr/lib/"
  bin/minstall lib/libglsl.so* "${pkgdir}/usr/lib/"
  cd src/mesa/drivers/dri
  make -C ${srcdir}/build/src/gallium/targets/dri-swrast DESTDIR="${pkgdir}" install
  ln -s libglx.xorg "${pkgdir}/usr/lib/xorg/modules/extensions/libglx.so"
# ati-dri
  cd "${srcdir}/build/src/mesa/drivers/dri"
  make -C ${srcdir}/build/src/gallium/targets/dri-r300 DESTDIR="${pkgdir}" install
  make -C ${srcdir}/build/src/gallium/targets/dri-r600 DESTDIR="${pkgdir}" install
#license
  install -m755 -d "${pkgdir}/usr/share/licenses/${pkgname}"
  install -m755 "${srcdir}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/"
}
