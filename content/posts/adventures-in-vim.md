---
date: 2024-09-19
draft: false
params:
  author: Stradex
title: Thoughts about six months using Vim.
---

# Why I started using vim?

I do programming since a long time. Most of my time programming was related to Game Development *(Networking)*, Web App Development, and Desktop development. Most of the time I did software in my local machine or I didn't have to use remote servers too much since six months ago. I used to work with VSCode which I used to find it great and I was really comfortable with, I also used Atom before that and Sublime Text Editor even before, but after I started to work with multiple remote servers everything changed for me.

I first tried to use VSCode to work with my remote servers, but the issue with that is that VSCode SSH extension installed a folder in the remote servers, and if you installed a extension with VSCode, for example PHP extension, it was installed in the server and I had projects with large codebases that because of those extensions went down because of excessive use of resources.
My point is, that VSCode extension was really invasive to be managing a remote server, god forbids a production server.
There was also another VSCode extension called "VSCode FTP" which connected to the remote server via FTP, but it sometimes failed.

But let's assume that VSCode works perfect with remote servers, it still requires you to install VSCode in your local machine, and if a fire starts and you are far away from your work computer, then it will take some time to reach your work computer or set up VSCode in a to start working. So knowing how to use VIM *(and VI even more)* allows you to easily connect to a server via terminal using SSH from any computer or device *(even your phone!)* and turn down a fire fast if necessary.

Why I am talking about turning off fires? Because since six months I started working in a Start Up and burning fires are a common thing at least in this Start Up, so being vim is the friend code editor for a firefighter development that needs to quickly fix something that happened.

## What are my thoughts after six months of using vim?

Okay, not gonna lie you here. The first months of using vim I was slower at coding that with VSCode and other IDEs I used.
Only after I learned some tricks with I was able to catch up with other IDEs speed and feeling comfortable with.
Now I use VIM with tabs, sometimes even a terminal open while I use vim, etc... 

Some of the most common things I do with VIM:
* Better hand position to navigate with **HJKL** instead of using arrows.
* Fast navigation with **w** to move to next word and **b** to move to previous word, so I can navigate a line faster.
* Use of tabs so I can easily navigate between files that I use frequently or I need to use for my daily work. This is my configuration for working with tabs:
    - *:map \<F7\> :tabp \<CR\>*
    - *:map \<F8\> :tabn \<CR\>*
* Always seeing the file name at bottom so if I have lots of tabs I can see the proper file name in the bottom since the tab title will be to short to even know which file I am working with. I use this command for that *:set laststatus=2*
* Using sessions.
    - Create session from current vim workplace *:mksession! my_vim_session.vim*
    - Open that session: vim -S my_vim_session.vim
* Using BLOCK VISUAL MODE to edit multilines.
* Open a terminal inside with **:term**
* Using vim arguments list to edit multiple files at the same time. THis is how to use it.
    - First, clear your arguments with :argd \*
    - Then add your arguments with **:argadd**
    - If you need to add multiple files at the same time, you can use some wildcards. For example, let's assume you want to rescusively add all files that end with php inside the my_folder directory. Then you can use :argadd my_folder_/\*\*/\*.php
    - Execute a vim command in all the files you added with **:argadd**. To check which files are in the args list, you can use **:args**. Example, **:argdo :%s/TextToReplace/NewText/ | w**
* Referring to current file with *%*
* Referring to current folder with *%:h*
* Execute some terminal commands using :!
    - For example :!git diff % *(will diff the current file you are using)*
