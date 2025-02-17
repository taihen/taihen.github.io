---
title: "Managing dotfiles with GNU Stow"
date: 2013-09-11T22:36:36+01:00
draft: false
---

### Dotfiles

> In Unix-like operating systems, dotfile is synonymous with hidden file. Usually used by programs to store configuration variables.

I have multiple machines, which I regularly work on. I keep sync of a set of personalised dotfiles between them – customised settings for programs that I’m using, like many of you. Till know I was using my private git repository for things like my private scripts, [Mutt](http://www.mutt.org/ "Mutt E-Mail Client"), [Irssi](http://www.irssi.org/ "Irssi IRC Client"), [Ekg2](http://ekg2.org/ "Ekg2 Multichat Client") or [Tmux](http://tmux.sourceforge.net/ "Tmux Terminal Multiplexer") configurations. On the other hand some I keep publicly available for others i.e. on [GitHub](https://github.com/taihen "GitHub Taihen") like [Vim configs](https://github.com/taihen/vimfiles). And oh boy, there are so many of them! The true is, I customise almost everything that I’m using including simple things like cp (just to get progress bar or interactivity). The problem was always with setting them up on new machine and update it when I need to. Usually it means manually creating symbolic links or writing custom script to do it for me automatically which I did at certain point. While it was fun to write the script, I always knew that it did not cover all my use cases, and it was not really flexible in terms of complex directory structures and so on. Many people have looked at this problem before – and solved it in their own ways. I wasn’t looking for a solution for a long time as I find most of them were quite obscure (as my script wasn’t obscure enough), additionally most had some dependencies that I didn’t wanted on all of my servers like [Homesick](https://github.com/technicalpickles/homesick "Homesick Git repository") (yes, I know there is [Homeshick](https://github.com/andsens/homeshick "Homeshick Git repository")).

But then I came around GNU Stow, completely by accident researching ways to deploy software. This looked very useful for automated deployment scripts and it is commonly available in EPEL for RHEL/CentOS and official repositories in Debian/Ubuntu. But GNU Stow could be also a great way to manage your dotfiles. I like reusing existing tools, so it appealed to me immediately.

### GNU Stow

> GNU Stow is a symlink farm manager which takes distinct packages of software and/or data located in separate directories on the filesystem, and makes them appear to be installed in the same place.

In other words, it tracks the necessary files in subdirectories and links those files from there to the desirable directory.

#### Working with GNU Stow

After installing GNU Stow (apt-get/yum/brew install stow) you will find out that there are many ways to work with it.

I’ve been experimenting with three setups:

-   per-application
-   per-environment
-   external

##### Per-application

Clone your Git dotfiles repository to ~/dotfiles, and enter the directory.

You need to organise per-application directories for example:

```
~/dotfiles/
    irssi/
        .config
        themes/
    bash/
        .bashrc
        .bashrc_aliases
    vim/
        .vimrc
        .vim/
               bundle/
               colors/
```

This will allow you to choose which packages/configuration you would like to install. For example presumably you will not need Irssi config on all of your machines but you would like to have your **.bashrc** consistent across all of them.

To install Bash configuration files, just do:

```
cd ~/dotfiles
stow bash
```

If you would like to install Vim configuration files as well, do:

```
cd ~/dotfiles
stow vim
```

GNU Stow will symbolically link ~/dotfiles/bash/ and ~/dotfiles/vim to you home directory, so that content of bash and vim will be exposed in $HOME directory.

Removing a config package follows the same logic by adding **\-D** flag and specifying installed package:

```
stow -D bash
stow -D vim
```

And **\-R** flag to restow so, delete and install again, basically rescan package for any new files:

```
stow -R bash
stow -R vim
```

##### Per-environment

Again, clone your Git dotfiles repository to ~/dotfiles, and enter the directory.

You need to organise per-environemnt directories for example:

```
~/dotfiles/
    work/
        .config/
        .icewm/
        .mutt/
    server/
        .tmux.conf
    common/
        .vimrc
        .vim/
               bundle/
               colors/
        .bashrc
        .bashrc_aliases
```

This will allow you divide which packages/configuration you would like to install on which type of machines and simplify deployment. For example **work** would be Mutt configuration, IceWM configuration only installed on laptop/desktop, but TMux config on server. Some configs, of course, will be shared especially Vim or Bash.

To install work configuration files on your laptop/desktop, just do:

```
cd ~/dotfiles
stow work
```

But on server:

```
cd ~/dotfiles
stow server
```

And add shared configuration:

```
cd ~/dotfiles
stow common
```

##### External

Sometimes you might have external configs that [someone else is managing](http://dotfiles.github.io/ "GitHub dotfiles") and you just sync and use. I keep them in separate directory ~/Git and synchronise them every now and then. The problem is that if you would run stow on this repository in Git directory it would populate symbolic links where you would run stow command. This undesirable. But stow have trick for this as well. You can specify to which directory you would like to populate those symbolic links with **\-t** flag. For example:

```
cd ~/Git
stow -t ~ someones_dotfiles
```

Would symlink **someones\_dotfiles** content to your home directory.

This is also useful as well if generally you would not like to keep your configuration in ~/dotfiles. Then you can also add **stow -t ~** in your aliases to prevent any accidents.

### Summary

Well, that’s all there is to it. So, I can say GNU Stow offers a quite neat and easily understandable solution for managing dotfiles while not relying on languages (GNU Stow is written in Perl) or tools you probably cannot install on the systems you are working on. Hopefully someone else out there finds this useful as well.
