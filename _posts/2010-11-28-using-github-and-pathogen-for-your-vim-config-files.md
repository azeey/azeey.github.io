---
title: Using Pathogen for your vim config files
layout: post
kind: article
tags:
- git
- github
- pathogen
- Vim Links
- vimrc
excerpt: Pathogen basically modifies the vim runtime paths which tell vim where to find plugins.
---

<% vimb="**.vim/bundle**{:.file}" %>

If you are an avid Vim user, you know that the one thing that is lacking is a good plugin management system.  With the
default vim installation, plugins are scattered through various folders in your .vim folder and its a nightmare to
enable/disable or remove plugins once you have installed them.  There are numerous tools out there that claim to make
plugin management easier, but frankly, I haven't tried any of them since they all seem to involve having to install a
separate tool to do the task. But this neat plugin by Tim Pope called Pathogen, is a little different. It uses vim
itself to mange the plugins properly. With Pathogen, plugin management is now a breeze.

Using [Pathogen][pathogen]
--------------------------

Pathogen basically modifies the vim runtime paths which tell vim where to find plugins.  The standard directory used by
Pathogen to install vim plugins is <%= vimb %>.  When used appropriately (with the correct line of code in 
your vimrc) Pathogen looks for a **bundle**{:.file} folder in **.vim**{:.file} and adds the subdirectories of it to
the runtime path.  This essentially means each subdirectory of **bundle**{:.file} looks like a **.vim/**{:.file} folder
to vim.

[![Pathogen Runtime Path Modification](/assets/images/pathogen_large-1024x742.png)](/assets/images/pathogen_large.png)

Taking the plugin fuzzyfinder as an example, the following table shows the path translations

>.vim/bundle/fuzzyfinder/doc >> .vim/doc/
>
>.vim/bundle/fuzzyfinder/plugin >> .vim/plugin
>
>.vim/bundle/fuzzyfinder/autoload >> .vim/autoload

This makes using Pathogen and Vim plugins really easy. All you have to do to install a plugin is to get the plugin from
wherever, and put it in its own folder in .<%= vimb %> To remove the plugin, just delete the folder from
<%= vimb %>. You can even use symlinking techniques as used in many Linux programs to mange their configuration
files. Create a **.vim/bundle-available**{:.file} folder, put all the plugins in there and create symlinks from <%= vimb %>
pointing to the corresponding plugin in **.vim/bundle-available**{:.file}.

There are a few things to note for the proper operation of Pathogen. The folder that you create inside <%= vimb %>
would need to have the proper subdirectory organization depending on what types of files the plugin contains. That is, you 
can't just drop the .vim file in the main folder of the plugin. If the file a is a plugin type, it needs to go
to the **plugin**{:.file} folder, if it's an autoload type, it needs to go the "autoload" folder. To continue with our
fuzzyfinder example, this plugin has a file called **fuf.vim**{:.file} in its
**plugin**{:.file}(**.vim/bundle/fuzzyfinder/plugin/**{:.file}) folder. If we placed this **fuf.vim**{:.file} file in the main **fuzzyfinder**{:.file}
folder (**.vim/bundle/fuzzyfinder/fuf.vim**{:.file}) , the plugin will not work properly since it will look just like
**.vim/fuf.vim**{:.file} to Vim. Moreover, don't make the mistake I made and name the **bundle**{:.file} folder "bundles", it won't
work.

The proper usage instructions for Pathogen can be found on the plugin's page
[http://www.vim.org/scripts/script.php?script_id=2332](http://www.vim.org/scripts/script.php?script_id=2332). But to
summarize:

1. Install Pathogen by putting **pathogen.vim**{:.file}  in **.vim/autoload**{:.file} folder.
2. Add ``call pathogen#runtime_append_all_bundles()`` to your **.vimrc**{:.file}
3. Create a **.vim/bundle**{:.file} and put your plugins it. Each plugin needs its own folder.


[pathogen]: http://www.vim.org/scripts/script.php?script_id=2332
