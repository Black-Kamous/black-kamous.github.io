---
title: 配置EverParse & F* & Emacs with fstar-mode开发环境
date: 2024-10-12
categories: [EverParse]
tags: [configuration]
description: 配置一个EverParse好用的开发环境
---

## 依赖安装

> apt install build-essential git wget patch opam

安装 OCaml

> opam init --compiler=4.14.0

安装完成后更新环境

> eval $(opam env --switch=4.14.0)

clone everest代码，其中包含一个配置脚本，执行脚本安装相关依赖

> ./everest opam

## 编译

首先准备python可执行文件，在/usr/bin中链接到python3即可
clone everparse代码，在其根目录中执行`make everparse`自动完成依赖代码下载和编译，总编译时间略长...

## emacs准备

ubuntu 22.04 apt emacs版本27.1，满足要求了(>=24.3)

在.emacs中使用见附录的配置文件，里面包括了fstar相关配置，fstar和Z3的路径配置，以及lowparse等库的引用路径配置

也可在.bashrc中添加以下代码配置路径

> export PATH=/root/everparse/karamel:/root/everparse/z3:/root/everparse/FStar/bin:$PATH
> export KRML_HOME=/root/everparse/karamel

### public key相关配置

执行`M-x package-refresh-contents`时出现错误

```
Failed to verify signature archive-contents.sig:
No public key for AAAAAAAAAAAAAA created at 2020-09-08T10:05:02+0100 using RSA
Command output:
gpg: WARNING: unsafe permissions on homedir '/home/xxx/.emacs.d/elpa/gnupg'
gpg: Signature made Tue 08 Sep 2020 10:05:02 BST
gpg:                using RSA key XXXXXXXXXXXXXXXXXXXXXXXX
gpg: Can't check signature: No public key
```

是公钥配置错误，执行`gpg --homedir ~/.emacs.d/elpa/gnupg/ --keyserver hkp://keyserver.ubuntu.com --receive-keys AAAAAAAAA`

重启emacs，执行`M-x package-refresh-contents`，不再次报错即为成功

### 安装fstar-mode

启动emacs，输入`M-x package-install RET fstar-mode RET`

### 其他安装

use-package counsel

## 验证安装

到everparse/tests/sample下执行`make`，成功说明一切畅通~

## 附录

```
(require 'package)
(add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
(package-initialize)

(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(custom-enabled-themes '(tango-dark))
 '(package-selected-packages
   '(company-c-headers undo-tree ace-window amx counsel swiper use-package ivy mwim gnu-elpa-keyring-update fstar-mode)))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )

(electric-pair-mode t)            ; 自动补全括号
(add-hook 'prog-mode-hook #'show-paren-mode) ; 编程模式下，光标在括号上时高亮另一个括号
(column-number-mode t)            ; 在 Mode line 上显示列号
(global-auto-revert-mode t)         ; 当另一程序修改了文件时，让 Emacs 及时刷新 Buffer
(delete-selection-mode t)          ; 选中文本后输入文本会替换文本（更符合我们习惯了的其它编辑器的逻辑）
(setq inhibit-startup-message t)       ; 关闭启动 Emacs 时的欢迎界面
(setq make-backup-files nil)         ; 关闭文件自动备份
(add-hook 'fstar-mode-hook #'hs-minor-mode)  ; 编程模式下，可以折叠代码块
(global-display-line-numbers-mode 1)     ; 在 Window 显示行号

(setq-default fstar-executable "/root/everparse/FStar/bin/fstar.exe")
(setq-default fstar-smt-executable "/root/everparse/z3/z3")

(global-set-key (kbd "C-a") 'mwim-beginning)
(global-set-key (kbd "C-e") 'mwim-end)


(use-package ivy
 :ensure t)

(use-package ivy
 :ensure t
 :init
 (ivy-mode 1)
 (counsel-mode 1)
 :config
 (setq ivy-use-virtual-buffers t)
 (setq search-default-mode #'char-fold-to-regexp)
 (setq ivy-count-format "(%d/%d) ")
 :bind
 (("C-s" . 'swiper)
  ("C-x b" . 'ivy-switch-buffer)
  ("C-c v" . 'ivy-push-view)
  ("C-c s" . 'ivy-switch-view)
  ("C-c V" . 'ivy-pop-view)
  ("C-x C-@" . 'counsel-mark-ring)
  ("C-x C-SPC" . 'counsel-mark-ring)
  :map minibuffer-local-map
  ("C-r" . counsel-minibuffer-history)))
  
(use-package amx
  :ensure t
  :init (amx-mode))

(use-package ace-window
  :ensure t
  :bind (("C-x o" . 'ace-window)))

(use-package undo-tree
  :ensure t
  :init (global-undo-tree-mode)
  :custom
  (undo-tree-auto-save-history nil))

(use-package flycheck
  :ensure t
  :init (global-flycheck-mode))

(setq comment-style 'extra-line)       ;多行注释
  ;; (define-key global-map (kbd "M-;") 'comment-line)     ; 多行注释
  ;; (define-key global-map (kbd "C-x C-m") 'comment-dwim) ; 单行行尾注释
  (defun qiang-comment-dwim-line (&optional arg)
    "Replacement for the comment-dwim command.
  If no region is selected and current line is not blank and
  we are not at the end of the line, then comment current line.
  Replaces default behaviour of comment-dwim,
  when it inserts comment at the end of the line. "
    (interactive "*P")
    (comment-normalize-vars)
    (if (and (not (region-active-p)) (not (looking-at "[ \t]*$")))
        (comment-or-uncomment-region (line-beginning-position) (line-end-position))
      (comment-dwim arg)))

(global-set-key "\M-;" 'qiang-comment-dwim-line)

(defun my-kill-ring-save (beg end &optional region)
        (interactive (list (mark) (point)
                           (prefix-numeric-value current-prefix-arg)))
        (if (region-active-p)
            (kill-ring-save beg end region)
          (progn
            (message "Copied line")
            (kill-ring-save (line-beginning-position) (line-end-position)))))

      (global-set-key [remap kill-ring-save] 'my-kill-ring-save)

(setq fstar-subp-prover-args '("--include" "/root/everparse/src/lowparse" "--include" "/root/everparse/karamel/krmllib" "--include" "/root/everparse-master/karamel/krmllib/obj"))
```
