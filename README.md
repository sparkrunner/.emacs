# .emacs
```elisp
;; -*- lexical-binding: t; -*-

;; ==================================================
;; 1. 包管理器与国内镜像
;; ==================================================
(setq package-archives '(("gnu"    . "https://mirrors.zju.edu.cn/elpa/gnu/")
                         ("nongnu" . "https://mirrors.zju.edu.cn/elpa/nongnu/")
                         ("melpa"  . "https://mirrors.zju.edu.cn/elpa/melpa/")))

(package-initialize)

;; 自动安装 use-package
(unless (package-installed-p 'use-package)
  (package-refresh-contents)
  (package-install 'use-package))

;; 全局自动安装插件
(setq use-package-always-ensure t)

;; ==================================================
;; 2. 基础环境与全局行为
;; ==================================================
(set-language-environment "UTF-8")
(set-default-coding-systems 'utf-8)

(setq inhibit-startup-screen t)
(setq frame-title-format "")

(fset 'yes-or-no-p 'y-or-n-p)

(setq ansi-color-faces-vector
      [default default default italic underline success warning error])

;; 系统剪贴板互通
(setq x-select-enable-clipboard t)

;; 撤销历史上限
(setq-default kill-ring-max 65535)

;; 桌面会话保存
(desktop-save-mode t)
(setq desktop-restore-frames t)

;; ==================================================
;; 3. 编辑核心：缩进、括号、自动行为
;; ==================================================
(setq-default indent-tabs-mode nil)
(setq-default tab-width 4)

(electric-indent-mode 1)
(electric-pair-mode 1)
(setq electric-pair-open-newline-between-pairs t)

;; CUA 模式：C-x/C-c/C-v 剪切复制粘贴
(cua-mode t)

;; 文件补全增强
(ido-mode t)

;; ==================================================
;; 4. C/C++ 专项配置
;; ==================================================
(setq c-default-style "linux")

(add-hook 'c-mode-common-hook
          (lambda ()
            (setq c-basic-offset 4)
            (setq c-electric-flag t)
            (setq indent-tabs-mode nil)))

;; ==================================================
;; 5. 通用快捷键（全平台生效）
;; ==================================================
;; 全选 / 撤销 / 重做 / 保存
(global-set-key (kbd "C-a") 'mark-whole-buffer)
(global-set-key (kbd "C-z") 'undo)
(global-set-key (kbd "C-S-z") 'undo-redo)
(global-set-key (kbd "C-s") 'save-buffer)

;; 一键编译 F9
(defun compile-file ()
  (interactive)
  (compile (format "g++ -o %s %s -g -lm -Wall"
                   (file-name-sans-extension (buffer-name))
                   (buffer-name))))
(global-set-key [f9] 'compile-file)

;; GDB 调试 F10
(global-set-key [f10] 'gud-gdb)

;; ==================================================
;; 6. 平台专属快捷键
;; ==================================================
;; macOS：Cmd 键撤销/重做
(when (eq window-system 'ns)
  (global-set-key (kbd "s-z") 'undo)
  (global-set-key (kbd "s-Z") 'undo-redo))

;; ==================================================
;; 7. 界面：行号、高亮、标签栏
;; ==================================================
(global-display-line-numbers-mode t)
(column-number-mode t)
(size-indication-mode t)

(tab-bar-mode t)

(global-hl-line-mode t)
(show-paren-mode t)

(global-font-lock-mode t)

(setq-default cursor-type 'bar)
(blink-cursor-mode -1)

;; 隐藏工具栏
(tool-bar-mode -1)

;; ==================================================
;; 8. 滚动优化（通用 + 平台专属）
;; ==================================================
;; 通用竖向滚动逻辑
(setq scroll-preserve-screen-position nil)
(setq scroll-conservatively 101)
(setq scroll-step 0)
(setq scroll-up-aggressively nil)
(setq scroll-down-aggressively nil)

(setq mouse-wheel-scroll-amount '(3 ((shift) . 1) ((control))))
(setq mouse-wheel-progressive-speed nil)
(mouse-wheel-mode t)

;; macOS NS 专属：像素平滑滚动 + 触控板惯性 + 横向滚动
(when (eq window-system 'ns)
  (pixel-scroll-precision-mode t)
  (setq ns-use-mwheel-momentum t)
  (setq ns-use-mwheel-acceleration t)
  (setq ns-mwheel-line-height 18)

  ;; 横向触控板滚动
  (setq mouse-wheel-tilt-scroll t)
  (setq mouse-wheel-flip-direction t))

;; ==================================================
;; 9. Ctrl+滚轮缩放字体（跨平台兼容）
;; ==================================================
(when (display-graphic-p)
  (cond
   ;; macOS：wheel 事件
   ((eq window-system 'ns)
    (global-set-key (kbd "C-<wheel-up>")   'text-scale-adjust)
    (global-set-key (kbd "C-<wheel-down>") 'text-scale-adjust))
   ;; Linux GTK / X11：mouse-4 / mouse-5 事件
   ((eq window-system 'gtk)
    (global-set-key (kbd "C-<mouse-4>") 'text-scale-adjust)
    (global-set-key (kbd "C-<mouse-5>") 'text-scale-adjust))))

;; ==================================================
;; 10. 插件配置
;; ==================================================
(use-package company
  :hook (prog-mode . company-mode)
  :config
  (define-key company-active-map (kbd "RET") #'company-complete)
  (define-key company-active-map (kbd "<tab>") #'company-complete-selection)
  (setq company-minimum-prefix-length 1
        company-idle-delay 0.2
        company-show-numbers t))

(use-package multiple-cursors)

;; ==================================================
;; 11. 字体
;; ==================================================
(set-face-attribute 'default nil
                    :family "Iosevka"
                    :foundry "nil"
                    :slant 'normal
                    :weight 'regular
                    :height 120
                    :width 'normal)

;; ==================================================
;; 12. Gruber Darker 主题（内联版）
;; ==================================================
(deftheme gruber-darker "Gruber Darker color theme")

(let ((gruber-darker-fg        "#e4e4ef")
      (gruber-darker-fg+1      "#f4f4ff")
      (gruber-darker-fg+2      "#f5f5f5")
      (gruber-darker-white     "#ffffff")
      (gruber-darker-black     "#000000")
      (gruber-darker-bg-1      "#101010")
      (gruber-darker-bg        "#181818")
      (gruber-darker-bg+1      "#282828")
      (gruber-darker-bg+2      "#453d41")
      (gruber-darker-bg+3      "#484848")
      (gruber-darker-bg+4      "#52494e")
      (gruber-darker-red-1     "#c73c3f")
      (gruber-darker-red       "#f43841")
      (gruber-darker-red+1     "#ff4f58")
      (gruber-darker-green     "#73c936")
      (gruber-darker-yellow    "#ffdd33")
      (gruber-darker-brown     "#cc8c3c")
      (gruber-darker-quartz    "#95a99f")
      (gruber-darker-niagara-2 "#303540")
      (gruber-darker-niagara-1 "#565f73")
      (gruber-darker-niagara   "#96a6c8")
      (gruber-darker-wisteria  "#9e95c7"))

  (custom-theme-set-variables
   'gruber-darker
   '(frame-brackground-mode (quote dark)))

  (custom-theme-set-faces
   'gruber-darker

   `(border ((t ,(list :background gruber-darker-bg-1
                       :foreground gruber-darker-bg+2))))
   `(cursor ((t (:background ,gruber-darker-yellow))))
   `(default ((t ,(list :foreground gruber-darker-fg
                        :background gruber-darker-bg))))
   `(fringe ((t ,(list :background gruber-darker-bg-1
                       :foreground gruber-darker-bg+2))))
   `(link ((t (:foreground ,gruber-darker-niagara :underline t))))
   `(link-visited ((t (:foreground ,gruber-darker-wisteria :underline t))))
   `(match ((t (:background ,gruber-darker-bg+4))))
   `(shadow ((t (:foreground ,gruber-darker-bg+4))))
   `(minibuffer-prompt ((t (:foreground ,gruber-darker-niagara))))
   `(region ((t (:background ,gruber-darker-bg+3 :foreground nil))))
   `(secondary-selection ((t ,(list :background gruber-darker-bg+3
                                    :foreground nil))))
   `(trailing-whitespace ((t ,(list :foreground gruber-darker-black
                                    :background gruber-darker-red))))
   `(tooltip ((t ,(list :background gruber-darker-bg+4
                        :foreground gruber-darker-white))))

   `(holiday-face ((t (:foreground ,gruber-darker-red))))

   `(compilation-info ((t ,(list :foreground gruber-darker-green
                                 :inherit 'unspecified))))
   `(compilation-warning ((t ,(list :foreground gruber-darker-brown
                                    :bold t
                                    :inherit 'unspecified))))
   `(compilation-error ((t (:foreground ,gruber-darker-red+1))))
   `(compilation-mode-line-fail ((t ,(list :foreground gruber-darker-red
                                           :weight 'bold
                                           :inherit 'unspecified))))
   `(compilation-mode-line-exit ((t ,(list :foreground gruber-darker-green
                                           :weight 'bold
                                           :inherit 'unspecified))))

   `(custom-state ((t (:foreground ,gruber-darker-green))))

   `(diff-removed ((t ,(list :foreground gruber-darker-red+1
                             :background nil))))
   `(diff-added ((t ,(list :foreground gruber-darker-green
                           :background nil))))

   `(dired-directory ((t (:foreground ,gruber-darker-niagara :weight bold))))
   `(dired-ignored ((t ,(list :foreground gruber-darker-quartz
                              :inherit 'unspecified))))

   `(font-lock-builtin-face ((t (:foreground ,gruber-darker-yellow))))
   `(font-lock-comment-face ((t (:foreground ,gruber-darker-brown))))
   `(font-lock-comment-delimiter-face ((t (:foreground ,gruber-darker-brown))))
   `(font-lock-constant-face ((t (:foreground ,gruber-darker-quartz))))
   `(font-lock-doc-face ((t (:foreground ,gruber-darker-green))))
   `(font-lock-doc-string-face ((t (:foreground ,gruber-darker-green))))
   `(font-lock-function-name-face ((t (:foreground ,gruber-darker-niagara))))
   `(font-lock-keyword-face ((t (:foreground ,gruber-darker-yellow :bold t))))
   `(font-lock-preprocessor-face ((t (:foreground ,gruber-darker-quartz))))
   `(font-lock-reference-face ((t (:foreground ,gruber-darker-quartz))))
   `(font-lock-string-face ((t (:foreground ,gruber-darker-green))))
   `(font-lock-type-face ((t (:foreground ,gruber-darker-quartz))))
   `(font-lock-variable-name-face ((t (:foreground ,gruber-darker-fg+1))))
   `(font-lock-warning-face ((t (:foreground ,gruber-darker-red))))

   `(flymake-errline
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-red)
                   :foreground unspecified
                   :background unspecified
                   :inherit unspecified))
      (t (:foreground ,gruber-darker-red :weight bold :underline t))))
   `(flymake-warnline
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-yellow)
                   :foreground unspecified
                   :background unspecified
                   :inherit unspecified))
      (t (:foreground ,gruber-darker-yellow :weight bold :underline t))))

   `(flyspell-incorrect
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-red) :inherit unspecified))
      (t (:foreground ,gruber-darker-red :weight bold :underline t))))
   `(flyspell-duplicate
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-yellow) :inherit unspecified))
      (t (:foreground ,gruber-darker-yellow :weight bold :underline t))))

   `(ido-first-match ((t (:foreground ,gruber-darker-yellow :bold nil))))
   `(ido-only-match ((t (:foreground ,gruber-darker-brown :weight bold))))
   `(ido-subdir ((t (:foreground ,gruber-darker-niagara :weight bold))))

   `(info-xref ((t (:foreground ,gruber-darker-niagara))))
   `(info-visited ((t (:foreground ,gruber-darker-wisteria))))

   `(highlight ((t (:background ,gruber-darker-bg+1 :foreground nil))))
   `(highlight-current-line-face ((t ,(list :background gruber-darker-bg+1
                                            :foreground nil))))

   `(line-number ((t (:inherit default :foreground ,gruber-darker-bg+4))))
   `(line-number-current-line ((t (:inherit line-number :foreground ,gruber-darker-yellow))))

   `(mode-line ((t ,(list :background gruber-darker-bg+1
                          :foreground gruber-darker-white))))
   `(mode-line-buffer-id ((t ,(list :background gruber-darker-bg+1
                                    :foreground gruber-darker-white))))
   `(mode-line-inactive ((t ,(list :background gruber-darker-bg+1
                                   :foreground gruber-darker-quartz))))

   `(org-agenda-structure ((t (:foreground ,gruber-darker-niagara))))
   `(org-column ((t (:background ,gruber-darker-bg-1))))
   `(org-column-title ((t (:background ,gruber-darker-bg-1 :underline t :weight bold))))
   `(org-done ((t (:foreground ,gruber-darker-green))))
   `(org-todo ((t (:foreground ,gruber-darker-red-1))))
   `(org-upcoming-deadline ((t (:foreground ,gruber-darker-yellow))))

   `(isearch ((t ,(list :foreground gruber-darker-black
                        :background gruber-darker-fg+2))))
   `(isearch-fail ((t ,(list :foreground gruber-darker-black
                             :background gruber-darker-red))))
   `(isearch-lazy-highlight-face ((t ,(list
                                       :foreground gruber-darker-fg+1
                                       :background gruber-darker-niagara-1))))

   `(sh-quoted-exec ((t (:foreground ,gruber-darker-red+1))))

   `(show-paren-match-face ((t (:background ,gruber-darker-bg+4))))
   `(show-paren-mismatch-face ((t (:background ,gruber-darker-red-1))))

   `(company-tooltip ((t (:foreground ,gruber-darker-fg :background ,gruber-darker-bg+1))))
   `(company-tooltip-annotation ((t (:foreground ,gruber-darker-brown :background ,gruber-darker-bg+1))))
   `(company-tooltip-annotation-selection ((t (:foreground ,gruber-darker-brown :background ,gruber-darker-bg-1))))
   `(company-tooltip-selection ((t (:foreground ,gruber-darker-fg :background ,gruber-darker-bg-1))))
   `(company-tooltip-mouse ((t (:background ,gruber-darker-bg-1))))
   `(company-tooltip-common ((t (:foreground ,gruber-darker-green))))
   `(company-tooltip-common-selection ((t (:foreground ,gruber-darker-green))))
   `(company-scrollbar-fg ((t (:background ,gruber-darker-bg-1))))
   `(company-scrollbar-bg ((t (:background ,gruber-darker-bg+2))))
   ))

(provide-theme 'gruber-darker)
(enable-theme 'gruber-darker)

;; 启动窗口参数，避免配色闪烁
(setq default-frame-alist
      `((vertical-scroll-bars . nil)
        (internal-border-width . 0)
        (top . 25)
        (left . 45)
        (width . 120)
        (height . 40)
        (background-color . "#181818")
        (foreground-color . "#e4e4ef")
        (cursor-color . "#ffdd33")
        (mouse-color . "#ffdd33")
        (tool-bar-lines . 0)
        (menu-bar-lines . 1)
        (scroll-bar-lines . 0)
        (right-fringe . nil)
        (left-fringe . nil)))
(setq initial-frame-alist default-frame-alist)

;; ==================================================
;; Custom 自动生成区（不要手动修改）
;; ==================================================
(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(package-selected-packages '(company gruber-darker-theme multiple-cursors)))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 )

```
