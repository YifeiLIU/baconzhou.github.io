---
layout: post
title:  "How to install software without sudo permission"
date:   2017-03-14 11:41:49 -0600
comments: false
categories: tutorial
---

Last time I want to install the latest [emacs](https://www.gnu.org/software/emacs/]) on our linux server, cause Emacs can do a lot of things,  write code, run R [Ess](https://ess.r-project.org/), sending email, and [tutorial](https://www.gnu.org/software/emacs/tour/)

It is easy to use **sudo** to handle the problem

{% highlight ruby %}
sudo apt-get install build-essential
sudo apt-get build-dep emacs24
{% endhighlight %}

Unfortunately,  it is always not that easy to install something on a public server, so that you need to build the package from source code.

## Build emacs

- Download latest [Index of /gnu/emacs](http://mirrors.ocf.berkeley.edu/gnu/emacs/) . Choose one of the version you like, end with tar.gz
- Untar your downloaded file.
    ```shell
    tar -zxvf *.tar.gz # * is the name you your file
    ```

- change to the directory, where you just unpack your emacs, usually have the same name as your downloaded file. `cd *`
	- configure your building configuration. 
    ```shell
    ./configure —prefix=/home/baconzhou/software/emacs —bindir=/home/baconzhou/bin
    ```

	- it will install all the eamcs’s support files under `/home/baconzhou/software/emacs`, and create the emacs executables in `/home/baconzhou/bin`
	- build the package
        ```shell
        make && make install
        ```
- [Emacs Reference Card](https://www.gnu.org/software/emacs/refcards/pdf/refcard.pdf)

## Install other packages

- [ESS](http://ess.r-project.org/Manual/ess.html#Installation) ESS stands for Emacs Speaks Statistics, Most useful package, which can call R from emacs.
- [MELPA](https://melpa.org/#/) MELPA stands for Milkypostman’s Emacs Lisp Package Archive, you can find lots of packages on it for emacs.  It is just like an cran on R, you can follow the instruction here [Getting statred](https://melpa.org/#/getting-started)
- [stan-mode: Emacs mode for Stan.](https://github.com/stan-dev/stan-mode) I write [stan](mc-stan.org) for data analysis, it is really powerful.

