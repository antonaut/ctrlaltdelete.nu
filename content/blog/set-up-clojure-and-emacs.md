+++
title = "Set up Clojure with Emacs on Arch Linux"
author = ["Anton Erholt"]
description = "It's all about 42, (defn foobarbaz []) and cheeseburgers."
date = 2018-11-11T06:35:00+01:00
lastmod = 2018-11-11T07:54:07+01:00
tags = ["clojure", "emacs"]
categories = ["blog"]
draft = false
weight = 2001
+++

Since I use Arch Linux I decided to use the distro package management
to help out with the install. When starting out, I already had emacs
installed (If I had not, a **pacman -S emacs** would have done the job).

The last time I used Clojure, I used [lein](https://leiningen.org/) for doing project
management. I guess I'll start from there this time too.

I installed an AUR helper **yay** which allows me to install
non-official Arch packages similar to how **pacman** does it. NOTE: You
REALLY need to read through the PKGBUILD file for the packages you
install, so that they do not do anything fishy (at least read the
commments on AUR).

Having **yay** installed, it was a breeze to get the packages **clojure**
and **leiningen** installed. I chose the current openjdk (version
11.0.1) as the backing JVM.

I looked at [braveclojure](https://www.braveclojure.com/basic-emacs/) and [ebzzry](https://ebzzry.io/en/emacs-pairs/#basics) when setting up my Emacs
configuration. The biggest thing I changed was the slurping/barfing
where **C-&lt;left/right&gt;** acts forward and **M-&lt;left/right&gt;**
acts backward.

Starting out, I created this file, but later when I ran **M-x
cider-jack-in**, I got some weird error so I removed it. I guess some
stuff has happened since the last time I did this.

-   **~/.lein/profiles.clj** <- DON'T add this file

```clojure
{:user {:plugins [[cider/cider-nrepl "0.8.1"]]}}
```

-   **~.emacs**

```clojure
(use-package clojure-mode
  :ensure t)

(use-package cider
  :ensure t)

(use-package smartparens
  :ensure t
  :bind (
	 ("C-M-a" . sp-beginning-of-sexp)
	 ("C-M-e" . sp-end-of-sexp)

	 ("C-<down>" . sp-down-sexp)
	 ("C-<up>"   . sp-up-sexp)
	 ("M-<down>" . sp-backward-down-sexp)
	 ("M-<up>"   . sp-backward-up-sexp)

	 ("C-M-f" . sp-forward-sexp)
	 ("C-M-b" . sp-backward-sexp)

	 ("C-M-n" . sp-next-sexp)
	 ("C-M-p" . sp-previous-sexp)

	 ("C-S-f" . sp-forward-symbol)
	 ("C-S-b" . sp-backward-symbol)

	 ("C-<right>" . sp-forward-slurp-sexp)
	 ("C-<left>" . sp-forward-barf-sexp)
	 ("M-<left>"  . sp-backward-slurp-sexp)
	 ("M-<right>"  . sp-backward-barf-sexp)

	 ("C-M-t" . sp-transpose-sexp)
	 ("C-M-k" . sp-kill-sexp)
	 ("C-k"   . sp-kill-hybrid-sexp)
	 ("M-k"   . sp-backward-kill-sexp)
	 ("C-M-w" . sp-copy-sexp)
	 ("C-M-d" . delete-sexp)

	 ("M-<backspace>" . backward-kill-word)
	 ("C-<backspace>" . sp-backward-kill-word)
	 ([remap sp-backward-kill-word] . backward-kill-word)

	 ("M-[" . sp-backward-unwrap-sexp)
	 ("M-]" . sp-unwrap-sexp)

	 ("C-x C-t" . sp-transpose-hybrid-sexp)

	 ("C-c ("  . wrap-with-parens)
	 ("C-c ["  . wrap-with-brackets)
	 ("C-c {"  . wrap-with-braces)
	 ("C-c '"  . wrap-with-single-quotes)
	 ("C-c \"" . wrap-with-double-quotes)
	 ("C-c _"  . wrap-with-underscores)
	 ("C-c `"  . wrap-with-back-quotes))
  :diminish smartparens-mode
  :config
  (require 'smartparens-config)
  (smartparens-global-mode 1))

(use-package rainbow-delimiters
  :ensure t)
```

That is about it for getting Clojure and running with Emacs on Arch
Linux. Now you can do **lein new <project-name>** to generate a new
project. To get a REPL running, simply open the **project.clj** and **M-x
cider-jack-in**.
