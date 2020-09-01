---
title: vim 7.4
date: 2013-08-29T16:02:05+08:00
tags: [vim, editor]
---

在vim-cn邮件组看到[vim][vim]7.4发布啦！！！！！
这个版本主要变化包括:

    - 新的正则表达式引擎
    - 对诸多 runtime 文件进行了改善
    - 更好的语法加亮及缩进

官网没有下载编译了一个Mac办的cli出来自己用。

<!--more-->

编译选项为：

    ./configure --with-features=huge --enable-cscope --enable-luainterp=dynamic --enable-pythoninterp=dynamic --enable-rubyinterp=dynamic --enable-perlinterp=dynamic --enable-tclinterp --enable-multibyte --disable-gui



当然你也可以选择[Macvim][macvim]。
:version

    VIM - Vi IMproved 7.4 (2013 Aug 10, compiled Aug 21 2013 15:43:32)
    MacOS X (unix) version
    Included patches: 1-5
    Compiled by Agassi@Agassi.local
    Huge version without GUI.  Features included (+) or not (-):
    +arabic          +file_in_path    +mouse_sgr       +tag_binary
    +autocmd         +find_in_path    -mouse_sysmouse  +tag_old_static
    -balloon_eval    +float           +mouse_urxvt     -tag_any_white
    -browse          +folding         +mouse_xterm     +tcl
    ++builtin_terms  -footer          +multi_byte      +terminfo
    +byte_offset     +fork()          +multi_lang      +termresponse
    +cindent         -gettext         -mzscheme        +textobjects
    -clientserver    -hangul_input    +netbeans_intg   +title
    +clipboard       +iconv           +path_extra      -toolbar
    +cmdline_compl   +insert_expand   +perl/dyn        +user_commands
    +cmdline_hist    +jumplist        +persistent_undo +vertsplit
    +cmdline_info    +keymap          +postscript      +virtualedit
    +comments        +langmap         +printer         +visual
    +conceal         +libcall         +profile         +visualextra
    +cryptv          +linebreak       +python/dyn      +viminfo
    +cscope          +lispindent      -python3         +vreplace
    +cursorbind      +listcmds        +quickfix        +wildignore
    +cursorshape     +localmap        +reltime         +wildmenu
    +dialog_con      -lua             +rightleft       +windows
    +diff            +menu            +ruby/dyn        +writebackup
    +digraphs        +mksession       +scrollbind      -X11
    -dnd             +modify_fname    +signs           -xfontset
    -ebcdic          +mouse           +smartindent     -xim
    +emacs_tags      -mouseshape      -sniff           -xsmp
    +eval            +mouse_dec       +startuptime     -xterm_clipboard
    +ex_extra        -mouse_gpm       +statusline      -xterm_save
    +extra_search    -mouse_jsbterm   -sun_workshop
    +farsi           +mouse_netterm   +syntax
       system vimrc file: "$VIM/vimrc"
         user vimrc file: "$HOME/.vimrc"
     2nd user vimrc file: "~/.vim/vimrc"
          user exrc file: "$HOME/.exrc"
      fall-back for $VIM: "/usr/local/share/vim"
    Compilation: gcc -c -I. -Iproto -DHAVE_CONFIG_H   -DMACOS_X_UNIX -no-cpp-precomp  -g -O2 -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=1     -I/System/Library/Frameworks/Tcl.framework/Headers  -D_REENTRANT=1  -D_THREAD_SAFE=1  -D_DARWIN_C_SOURCE=1
    Linking: gcc   -L.   -L/usr/local/lib -o vim        -lncurses  -liconv -framework Cocoa   -fstack-protector -L/usr/local/lib  -L/System/Library/Perl/5.12/darwin-thread-multi-2level/CORE -lm -lutil -lc   -F/System/Library/Frameworks -framework Tcl -framework CoreFoundation

下载地址：https://d.pr/f/RTnc

把`vim`放在`$PATH`中。
`vim74/`放在`/usr/loval/share/vim`下。

[vim]: https://www.vim.org
[macvim]: https://code.google.com/p/macvim/