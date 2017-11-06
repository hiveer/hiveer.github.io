---
layout: post
---

#### Use system clipboard as the default register of vim
Then, you can copy you code to any other editor for system.  
You need to add this to your `.vimrc` file

    set clipboard+=unnamed

#### When use vim in a tmux session
You will find the configuration above is not work any more.  
[Here is a good explanation for this](http://stackoverflow.com/questions/11404800/fix-vim-tmux-yank-paste-on-unnamed-register)  
And the author also give us [an approch](https://github.com/ChrisJohnsen/tmux-MacOSX-pasteboard) to solve this problem  
ex. for Mac OS X

    brew install reattach-to-user-namespace

In `.tmux.conf`

    set-option -g default-command "reattach-to-user-namespace -l zsh"

Then reopen your tmux session and everything will be ok!
