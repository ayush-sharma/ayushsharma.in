---
layout: post
title:  "SublimeText 3 setup for Jekyll development"
number: 72
date:   2018-09-17 0:00
categories: development
---
[SublimeText 3](https://www.sublimetext.com/) is one of the most popular IDEs available today. It’s free, powerful, feature-rich, and highly extensible. While the built-in funtionality is good enough for most development, we can make working with Jekyll easier and more productive by installing a few packages.

## Package Control

To set up SublimeText 3 for Jekyll, we’re going to need to install a few packages. One way to install them is to use the built-in package manager helpfully called **Package Control**. Open the command palette by hitting `CTRL/COMMAND + SHIFT + P`, and begin typing `Install Package Control`. Select it from the drop-down and hit `Enter`. SublimeText will then begin the installation process.

With Package Control installed, we can now install other packages. From the command palette, select `Package Control: Install Package`, select a package to install from the drop-down list, and hit `Enter` to install it. We’re going to use this approach to install the rest of the packages listed below.

<video width="730" height="500" preload="auto" muted controls="controls">
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-1-package_control.webm" type="video/webm">
Your browser does not support this video.
</video> 

## 1. Icons and Themes

Installing icons and themes for SublimeText 3 is as easy as selecting them from the packages list.

The file icons in the sidebar can be improved by installing [A File Icon](https://github.com/ihodev/a-file-icon) package from the list. The new icons should become updated as soon as the package is installed.

The [Ayu](https://github.com/dempfi/ayu) theme can be installed the same way. Once installed, the new theme can be changed by selecting it from the `UI: Select Theme` and `UI: Select Color Scheme` option in the command palette.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-2-theme.webm" type="video/webm">
Your browser does not support this video.
</video>

## 2. Sidebar Enhancements

Right-clicking the files and folders in SublimeText 3’s sidebar only provides a few basic options. To add some additional options to the right-click menu, we can install the [SidebarEnhancements](https://github.com/titoBouzout/SideBarEnhancements/tree/st3) package.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-3-sidebar.webm" type="video/webm">
Your browser does not support this video.
</video>

## 3. Git

Installing the [Git](https://github.com/kemayo/sublime-text-git) package allows us to execute `git` commands from within SublimeText 3 itself.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-4-git.webm" type="video/webm">
Your browser does not support this video.
</video>

## 4. GitGutter

The [GitGutter](https://github.com/jisaacks/GitGutter) package can show the `git` status of the file currently being modified in the left gutter. It supports showing the following statuses: lines inserted, lines updated, deleted region borders, ignored files, and untracked files. Hovering over the icon in the gutter will also show a helpful diff popup.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-5-git_gutter.webm" type="video/webm">
Your browser does not support this video.
</video>

## 5. AutoFileRename

The [AutoFileName](https://packagecontrol.io/packages/AutoFileName) package can autocomplete filenames based on the directory path. Its very useful for specifying local files in `<script>` and `<img`> HTML tags.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-6-autofilename.webm" type="video/webm">
Your browser does not support this video.
</video>

## 6. Emmet

Emmet is another amazing productivity hack. Once installed, it allows using a shorter syntax for HTML and CSS that it can then expand using the `CTRL/COMMAND + E` shortcut. For example, type `html:5` and hit `CTRL/COMMAND + E` and Emmet will expand that into the HTML5 Doctype declaration.

Similarly, nest HTML elements like `div>ul>li` and it will be expanded to the corresponding nested HTML tag structure. This package can help save a lot of time and effort when writing HTML in our Jekyll markdown files.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-7-emmet.webm" type="video/webm">
Your browser does not support this video.
</video>

## 7. Jekyll

The [Jekyll](https://packagecontrol.io/packages/Jekyll) plug-in makes managing posts and templates a lot easier. It requires some configuration, but once set up, it can make the process of creating new posts and templates a breeze.

<video width="730" height="500" preload="auto" muted controls>
  <source src="{{ site.videos-path | prepend: site.baseurl | prepend: site.url }}sublime-text-3-jekyll-8-jekyll.webm" type="video/webm">
Your browser does not support this video.
</video>

<div class="mt-4">
	&nbsp;
</div>

The packages I've listed above will make working with SublimeText and Jekyll a lot more fun. You can also use the command palette and the packages drop-down list to explore more packages on your own. The packages are also published on the [Package Control website](https://packagecontrol.io/).

Have fun, and keep coding :)