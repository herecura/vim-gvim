# vim:set ft=sh:
# Maintainer: BlackEagle < ike DOT devolder AT gmail DOT com >
# thx for the original vim pkg:
# Contributor: Jan "heftig" Steffens <jan.steffens@gmail.com>
# Contributor: tobias [ tobias at archlinux org ]
# Contributor: Daniel J Griffiths <ghost1227@archlinux.us>

pkgbase=vim-gvim
pkgname=('vim-gvim-gtk2' 'vim-gvim-gtk3' 'vim-gvim-common')
_basever=8.2
_patchlevel=0805
if [ "$_patchlevel" = "0" ]; then
    pkgver=${_basever}
else
    pkgver=${_basever}.${_patchlevel}
fi
_gitcommit=fe81389d606ae79415d208207295f41a4375c580
pkgrel=1
_versiondir=vim${_basever/./}
arch=('x86_64')
license=('custom:vim')
url="http://www.vim.org"
makedepends=('gpm' 'perl' 'python' 'lua' 'desktop-file-utils' 'gtk2' 'gettext' 'pkgconfig' 'sed' 'git' 'ruby' 'gtk3' 'libxt')
source=(
    "$pkgbase::git://github.com/vim/vim#commit=$_gitcommit"
    'license.txt'
)
sha256sums=('SKIP'
            'bb4744930a0030085d382356e9fdd4f2049b6298147aee2470c7fca7ec82fd55')

prepare() {
    # remove old build dirs if exist
    [ -d gvim-build-gtk2 ] && rm -rf gvim-build-gtk2
    [ -d gvim-build-gtk3 ] && rm -rf gvim-build-gtk3

    cp -a ${pkgbase} gvim-build-gtk2
    (
        cd gvim-build-gtk2 && rm -rf ./.git*
    )

    # define the place for the global (g)vimrc file (set to /etc/vimrc)
    sed -i 's|^.*\(#define SYS_.*VIMRC_FILE.*"\) .*$|\1|' \
        gvim-build-gtk2/src/feature.h
    sed -i 's|^.*\(#define VIMRC_FILE.*"\) .*$|\1|' \
        gvim-build-gtk2/src/feature.h

    cp -a gvim-build-gtk2 gvim-build-gtk3

    cd ${srcdir}/gvim-build-gtk2
    (cd src && autoconf)

    cd ${srcdir}/gvim-build-gtk3
    (cd src && autoconf)
}

build() {
    msg2 'Building vim-gvim-gtk2'
    cd ${srcdir}/gvim-build-gtk2
    ./configure --prefix=/usr --localstatedir=/var/lib/vim \
        --mandir=/usr/share/man --with-compiledby=BlackEagle \
        --with-features=huge --enable-gpm --enable-acl --with-x=yes \
        --enable-gui=gtk2 --enable-multibyte --enable-cscope \
        --disable-netbeans  --enable-perlinterp=dynamic \
        --enable-python3interp=dynamic --enable-rubyinterp=dynamic \
        --enable-luainterp=dynamic
    make

    msg2 'Building vim-gvim-gtk3'
    cd ${srcdir}/gvim-build-gtk3
    ./configure --prefix=/usr --localstatedir=/var/lib/vim \
        --mandir=/usr/share/man --with-compiledby=BlackEagle \
        --with-features=huge --enable-gpm --enable-acl --with-x=yes \
        --enable-gui=gtk3 --enable-multibyte --enable-cscope \
        --disable-netbeans  --enable-perlinterp=dynamic \
        --enable-python3interp=dynamic --enable-rubyinterp=dynamic \
        --enable-luainterp=dynamic
    make
}

package_vim-gvim-gtk2() {
    pkgdesc='Vi Improved, gtk2 gui'
    depends=('vim-cli' 'vim-gvim-common' 'desktop-file-utils' 'gtk2' 'hicolor-icon-theme' 'shared-mime-info')
    provides=('gvim')
    replaces=('vim-gvim-gtk')
    conflicts=('vim-gvim-gtk3')

    # allow install of icons and desktopfiles
    install -dm755 "${pkgdir}/usr/share/icons/hicolor/48x48/apps"
    install -dm755 "${pkgdir}/usr/share/icons/locolor/32x32/apps"
    install -dm755 "${pkgdir}/usr/share/icons/locolor/16x16/apps"
    install -dm755 "${pkgdir}/usr/share/applications"

    cd ${srcdir}/gvim-build-gtk2
    make -j1 VIMRCLOC=/etc DESTDIR=${pkgdir} install

    # move vim to gvim
    rm -f ${pkgdir}/usr/bin/gvim
    mv ${pkgdir}/usr/bin/{vim,gvim}
    # remove files provided by vim-cli
    rm -f ${pkgdir}/usr/bin/{vimtutor,xxd,rview,rvim,view,vimdiff,ex}
    rm -f ${pkgdir}/usr/share/man/*{,/*}/{vim*,vimtutor*,xxd*,rview*,rvim*,view*,vimdiff*,ex*}
    # recreate gvim symlinks
    (
    cd ${pkgdir}/usr/bin
    for link in eview evim gview gvimdiff rgview rgvim; do
        rm -f ${link}
        ln -s gvim ${link}
    done
    )

    # Runtime provided by runtime package
    rm -r ${pkgdir}/usr/share/vim

    # Move the man pages for common packaging
    mv ${pkgdir}/usr/share/man ${srcdir}/gvim-man-install

    # remove vim desktop file
    rm ${pkgdir}/usr/share/applications/vim.desktop

    # license
    install -dm755 ${pkgdir}/usr/share/licenses/vim-gvim-gtk2
    install -Dm644 ${srcdir}/license.txt \
        ${pkgdir}/usr/share/licenses/vim-gvim-gtk2/license.txt
}

package_vim-gvim-gtk3() {
    pkgdesc='Vi Improved, gtk3 gui'
    depends=('vim-cli' 'vim-gvim-common' 'desktop-file-utils' 'gtk3' 'hicolor-icon-theme' 'shared-mime-info')
    provides=('gvim')
    replaces=('vim-gvim-gtk')
    conflicts=('vim-gvim-gtk2')

    # allow install of icons and desktopfiles
    install -dm755 "${pkgdir}/usr/share/icons/hicolor/48x48/apps"
    install -dm755 "${pkgdir}/usr/share/icons/locolor/32x32/apps"
    install -dm755 "${pkgdir}/usr/share/icons/locolor/16x16/apps"
    install -dm755 "${pkgdir}/usr/share/applications"

    cd ${srcdir}/gvim-build-gtk3
    make -j1 VIMRCLOC=/etc DESTDIR=${pkgdir} install

    # move vim to gvim
    rm -f ${pkgdir}/usr/bin/gvim
    mv ${pkgdir}/usr/bin/{vim,gvim}
    # remove files provided by vim-cli
    rm -f ${pkgdir}/usr/bin/{vimtutor,xxd,rview,rvim,view,vimdiff,ex}
    rm -f ${pkgdir}/usr/share/man/*{,/*}/{vim*,vimtutor*,xxd*,rview*,rvim*,view*,vimdiff*,ex*}
    # recreate gvim symlinks
    (
    cd ${pkgdir}/usr/bin
    for link in eview evim gview gvimdiff rgview rgvim; do
        rm -f ${link}
        ln -s gvim ${link}
    done
    )

    # Runtime provided by runtime package
    rm -r ${pkgdir}/usr/share/vim

    # Move the man pages for common packaging
    mv ${pkgdir}/usr/share/man ${srcdir}/gvim-man-install

    # remove vim desktop file
    rm ${pkgdir}/usr/share/applications/vim.desktop

    # license
    install -dm755 ${pkgdir}/usr/share/licenses/vim-gvim-gtk3
    install -Dm644 ${srcdir}/license.txt \
        ${pkgdir}/usr/share/licenses/vim-gvim-gtk3/license.txt
}

package_vim-gvim-common() {
    pkgdesc='common files for gvim/qvim'

    # Install the common split from gvim/qvim
    install -dm755 ${pkgdir}/usr/share
    mv ${srcdir}/gvim-man-install ${pkgdir}/usr/share/man

    # license
    install -dm755 ${pkgdir}/usr/share/licenses/vim-gvim-common
    install -Dm644 ${srcdir}/license.txt \
        ${pkgdir}/usr/share/licenses/vim-gvim-common/license.txt
}

