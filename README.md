# .emacs
```elisp
;; -*- lexical-binding: t; -*-

;; ==================================================
;; 一、基础环境与编码
;; ==================================================
(set-language-environment "UTF-8")
(set-default-coding-systems 'utf-8)

;; 关闭欢迎页、清空标题
(setq inhibit-startup-screen t)
(setq frame-title-format "")

;; y/n 替代 yes/no
(fset 'yes-or-no-p 'y-or-n-p)

;; ANSI 终端颜色映射
(setq ansi-color-faces-vector
      [default default default italic underline success warning error])


;; ==================================================
;; 二、考场必备快捷键
;; ==================================================
;; 稳定重做函数：全版本兼容，连续按不会来回横跳
;(defun my-redo ()
;  (interactive)
;  (when (not (eq last-command 'my-redo))
;    (undo-start))
;  (undo-more 1))

;; 全选 / 撤销 / 重做 / 保存
(global-set-key (kbd "C-a") 'mark-whole-buffer)
(global-set-key (kbd "C-z") 'undo)
;; Ctrl+Shift+Z 重做（全平台通用，终端/GUI都能用）
(global-set-key (kbd "C-S-z") 'undo-redo)
(global-set-key (kbd "C-s") 'save-buffer)

;; macOS GUI 专属：Cmd+Z 撤销，Cmd+Shift+Z 重做
(when (featurep 'ns)
  (global-set-key (kbd "s-z") 'undo)
  (global-set-key (kbd "s-Z") 'undo-redo))

;; 一键编译（F9）
(defun compile-file ()
  (interactive)
  (compile (format "g++ -o %s %s -g -lm -Wall"
                   (file-name-sans-extension (buffer-name))
                   (buffer-name))))
(global-set-key [f9] 'compile-file)

;; GDB 调试（F10）
(global-set-key [f10] 'gud-gdb)

;; 扩大撤销记录上限
(setq-default kill-ring-max 65535)

;; Windows 下可取消注释，将菜单键设为 M-x
;; (define-key key-translation-map [apps] (kbd "M-x"))


;; ==================================================
;; 三、编辑核心：自动括号 + 换行缩进
;; ==================================================
(setq-default indent-tabs-mode nil)
(setq-default tab-width 4)

(electric-indent-mode 1)
(electric-pair-mode 1)
(setq electric-pair-open-newline-between-pairs t)


;; ==================================================
;; 四、C/C++ 专项（CC Mode）
;; ==================================================
(setq c-default-style "linux")

(add-hook 'c-mode-common-hook
          (lambda ()
            (setq c-basic-offset 4)
            (setq c-electric-flag t)
            (setq indent-tabs-mode nil)))


;; ==================================================
;; 五、界面与编辑体验优化
;; ==================================================
;; 显示行号 / 列号 / 文件大小
(global-display-line-numbers-mode t)
(column-number-mode t)
(size-indication-mode t)

;; 标签栏
(tab-bar-mode t)

;; 高亮当前行、高亮匹配括号
(global-hl-line-mode t)
(show-paren-mode t)

;; 全局语法高亮
(global-font-lock-mode t)

;; 光标样式为竖线，关闭闪烁
(setq-default cursor-type 'bar)
(blink-cursor-mode -1)

;; 滚动优化：平滑滚动，保留上下文边距
(setq scroll-preserve-screen-position 1)
(setq scroll-conservatively 101)
(setq scroll-margin 3)
(setq scroll-step 1)

;; 系统剪贴板互通
(setq x-select-enable-clipboard t)

;; 鼠标滚轮支持
(mouse-wheel-mode t)

;; 补全与文件选择增强
(ido-mode t)

;; CUA 模式：开启 C-x/C-c/C-v 剪切复制粘贴
(cua-mode t)

;; 隐藏工具栏
(tool-bar-mode -1)

;; 桌面会话保存
(desktop-save-mode t)
(setq desktop-restore-frames t)

;; 默认字体设置
(set-face-attribute 'default nil
                    :family "Iosevka"
                    :foundry "nil"
                    :slant 'normal
                    :weight 'regular
                    :height 120
                    :width 'normal)

;; Term 终端按键修复
(with-eval-after-load 'term
  (define-key term-raw-map (kbd "<backspace>") 'term-send-del)
  (define-key term-raw-map (kbd "DEL") 'term-send-del)
  (define-key term-raw-map (kbd "<up>") 'term-send-up)
  (define-key term-raw-map (kbd "<down>") 'term-send-down)
  (define-key term-raw-map (kbd "<left>") 'term-send-left)
  (define-key term-raw-map (kbd "<right>") 'term-send-right)
  (setq term-term-name "xterm-256color"))


;; ==================================================
;; 六、Gruber Darker 主题（原版内联版）
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

   ;; Agda2
   `(agda2-highlight-datatype-face ((t (:foreground ,gruber-darker-quartz))))
   `(agda2-highlight-primitive-type-face ((t (:foreground ,gruber-darker-quartz))))
   `(agda2-highlight-function-face ((t (:foreground ,gruber-darker-niagara))))
   `(agda2-highlight-keyword-face ((t ,(list :foreground gruber-darker-yellow
                                             :bold t))))
   `(agda2-highlight-inductive-constructor-face ((t (:foreground ,gruber-darker-green))))
   `(agda2-highlight-number-face ((t (:foreground ,gruber-darker-wisteria))))

   ;; AUCTeX
   `(font-latex-bold-face ((t (:foreground ,gruber-darker-quartz :bold t))))
   `(font-latex-italic-face ((t (:foreground ,gruber-darker-quartz :italic t))))
   `(font-latex-math-face ((t (:foreground ,gruber-darker-green))))
   `(font-latex-sectioning-5-face ((t ,(list :foreground gruber-darker-niagara
                                             :bold t))))
   `(font-latex-slide-title-face ((t (:foreground ,gruber-darker-niagara))))
   `(font-latex-string-face ((t (:foreground ,gruber-darker-green))))
   `(font-latex-warning-face ((t (:foreground ,gruber-darker-red))))

   ;; Basic Coloring
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

   ;; Calendar
   `(holiday-face ((t (:foreground ,gruber-darker-red))))

   ;; Compilation
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

   ;; Custom
   `(custom-state ((t (:foreground ,gruber-darker-green))))

   ;; Diff
   `(diff-removed ((t ,(list :foreground gruber-darker-red+1
                             :background nil))))
   `(diff-added ((t ,(list :foreground gruber-darker-green
                           :background nil))))

   ;; Dired
   `(dired-directory ((t (:foreground ,gruber-darker-niagara :weight bold))))
   `(dired-ignored ((t ,(list :foreground gruber-darker-quartz
                              :inherit 'unspecified))))

   ;; Ebrowse
   `(ebrowse-root-class ((t (:foreground ,gruber-darker-niagara :weight bold))))
   `(ebrowse-progress ((t (:background ,gruber-darker-niagara))))

   ;; Egg
   `(egg-branch ((t (:foreground ,gruber-darker-yellow))))
   `(egg-branch-mono ((t (:foreground ,gruber-darker-yellow))))
   `(egg-diff-add ((t (:foreground ,gruber-darker-green))))
   `(egg-diff-del ((t (:foreground ,gruber-darker-red))))
   `(egg-diff-file-header ((t (:foreground ,gruber-darker-wisteria))))
   `(egg-help-header-1 ((t (:foreground ,gruber-darker-yellow))))
   `(egg-help-header-2 ((t (:foreground ,gruber-darker-niagara))))
   `(egg-log-HEAD-name ((t (:box (:color ,gruber-darker-fg)))))
   `(egg-reflog-mono ((t (:foreground ,gruber-darker-niagara-1))))
   `(egg-section-title ((t (:foreground ,gruber-darker-yellow))))
   `(egg-text-base ((t (:foreground ,gruber-darker-fg))))
   `(egg-term ((t (:foreground ,gruber-darker-yellow))))

   ;; ERC
   `(erc-notice-face ((t (:foreground ,gruber-darker-wisteria))))
   `(erc-timestamp-face ((t (:foreground ,gruber-darker-green))))
   `(erc-input-face ((t (:foreground ,gruber-darker-red+1))))
   `(erc-my-nick-face ((t (:foreground ,gruber-darker-red+1))))

   ;; EShell
   `(eshell-ls-backup ((t (:foreground ,gruber-darker-quartz))))
   `(eshell-ls-directory ((t (:foreground ,gruber-darker-niagara))))
   `(eshell-ls-executable ((t (:foreground ,gruber-darker-green))))
   `(eshell-ls-symlink ((t (:foreground ,gruber-darker-yellow))))

   ;; Font Lock
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

   ;; Flymake
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
      (t (:forground ,gruber-darker-yellow :weight bold :underline t))))
   `(flymake-infoline
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-green)
                   :foreground unspecified
                   :background unspecified
                   :inherit unspecified))
      (t (:forground ,gruber-darker-green :weight bold :underline t))))

   ;; Flyspell
   `(flyspell-incorrect
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-red) :inherit unspecified))
      (t (:foreground ,gruber-darker-red :weight bold :underline t))))
   `(flyspell-duplicate
     ((((supports :underline (:style wave)))
       (:underline (:style wave :color ,gruber-darker-yellow) :inherit unspecified))
      (t (:foreground ,gruber-darker-yellow :weight bold :underline t))))

   ;; Helm
   `(helm-candidate-number ((t ,(list :background gruber-darker-bg+2
                                      :foreground gruber-darker-yellow
                                      :bold t))))
   `(helm-ff-directory ((t ,(list :foreground gruber-darker-niagara
                                  :background gruber-darker-bg
                                  :bold t))))
   `(helm-ff-executable ((t (:foreground ,gruber-darker-green))))
   `(helm-ff-file ((t (:foreground ,gruber-darker-fg :inherit unspecified))))
   `(helm-ff-invalid-symlink ((t ,(list :foreground gruber-darker-bg
                                        :background gruber-darker-red))))
   `(helm-ff-symlink ((t (:foreground ,gruber-darker-yellow :bold t))))
   `(helm-selection-line ((t (:background ,gruber-darker-bg+1))))
   `(helm-selection ((t (:background ,gruber-darker-bg+1 :underline nil))))
   `(helm-source-header ((t ,(list :foreground gruber-darker-yellow
                                   :background gruber-darker-bg
                                   :box (list :line-width -1
                                              :style 'released-button)))))

   ;; Ido
   `(ido-first-match ((t (:foreground ,gruber-darker-yellow :bold nil))))
   `(ido-only-match ((t (:foreground ,gruber-darker-brown :weight bold))))
   `(ido-subdir ((t (:foreground ,gruber-darker-niagara :weight bold))))

   ;; Info
   `(info-xref ((t (:foreground ,gruber-darker-niagara))))
   `(info-visited ((t (:foreground ,gruber-darker-wisteria))))

   ;; Jabber
   `(jabber-chat-prompt-foreign ((t ,(list :foreground gruber-darker-quartz
                                           :bold nil))))
   `(jabber-chat-prompt-local ((t (:foreground ,gruber-darker-yellow))))
   `(jabber-chat-prompt-system ((t (:foreground ,gruber-darker-green))))
   `(jabber-rare-time-face ((t (:foreground ,gruber-darker-green))))
   `(jabber-roster-user-online ((t (:foreground ,gruber-darker-green))))
   `(jabber-activity-face ((t (:foreground ,gruber-darker-red))))
   `(jabber-activity-personal-face ((t (:foreground ,gruber-darker-yellow :bold t))))

   ;; Line Highlighting
   `(highlight ((t (:background ,gruber-darker-bg+1 :foreground nil))))
   `(highlight-current-line-face ((t ,(list :background gruber-darker-bg+1
                                            :foreground nil))))

   ;; line numbers
   `(line-number ((t (:inherit default :foreground ,gruber-darker-bg+4))))
   `(line-number-current-line ((t (:inherit line-number :foreground ,gruber-darker-yellow))))

   ;; Linum
   `(linum ((t `(list :foreground gruber-darker-quartz
                      :background gruber-darker-bg))))

   ;; Magit
   `(magit-branch ((t (:foreground ,gruber-darker-niagara))))
   `(magit-diff-hunk-header ((t (:background ,gruber-darker-bg+2))))
   `(magit-diff-file-header ((t (:background ,gruber-darker-bg+4))))
   `(magit-log-sha1 ((t (:foreground ,gruber-darker-red+1))))
   `(magit-log-author ((t (:foreground ,gruber-darker-brown))))
   `(magit-log-head-label-remote ((t ,(list :foreground gruber-darker-green
                                            :background gruber-darker-bg+1))))
   `(magit-log-head-label-local ((t ,(list :foreground gruber-darker-niagara
                                           :background gruber-darker-bg+1))))
   `(magit-log-head-label-tags ((t ,(list :foreground gruber-darker-yellow
                                          :background gruber-darker-bg+1))))
   `(magit-log-head-label-head ((t ,(list :foreground gruber-darker-fg
                                          :background gruber-darker-bg+1))))
   `(magit-item-highlight ((t (:background ,gruber-darker-bg+1))))
   `(magit-tag ((t ,(list :foreground gruber-darker-yellow
                          :background gruber-darker-bg))))
   `(magit-blame-heading ((t ,(list :background gruber-darker-bg+1
                                    :foreground gruber-darker-fg))))

   ;; Message
   `(message-header-name ((t (:foreground ,gruber-darker-green))))

   ;; Mode Line
   `(mode-line ((t ,(list :background gruber-darker-bg+1
                          :foreground gruber-darker-white))))
   `(mode-line-buffer-id ((t ,(list :background gruber-darker-bg+1
                                    :foreground gruber-darker-white))))
   `(mode-line-inactive ((t ,(list :background gruber-darker-bg+1
                                   :foreground gruber-darker-quartz))))

   ;; Neo Dir
   `(neo-dir-link-face ((t (:foreground ,gruber-darker-niagara))))

   ;; Org Mode
   `(org-agenda-structure ((t (:foreground ,gruber-darker-niagara))))
   `(org-column ((t (:background ,gruber-darker-bg-1))))
   `(org-column-title ((t (:background ,gruber-darker-bg-1 :underline t :weight bold))))
   `(org-done ((t (:foreground ,gruber-darker-green))))
   `(org-todo ((t (:foreground ,gruber-darker-red-1))))
   `(org-upcoming-deadline ((t (:foreground ,gruber-darker-yellow))))

   ;; Search
   `(isearch ((t ,(list :foreground gruber-darker-black
                        :background gruber-darker-fg+2))))
   `(isearch-fail ((t ,(list :foreground gruber-darker-black
                             :background gruber-darker-red))))
   `(isearch-lazy-highlight-face ((t ,(list
                                       :foreground gruber-darker-fg+1
                                       :background gruber-darker-niagara-1))))

   ;; Sh
   `(sh-quoted-exec ((t (:foreground ,gruber-darker-red+1))))

   ;; Show Paren
   `(show-paren-match-face ((t (:background ,gruber-darker-bg+4))))
   `(show-paren-mismatch-face ((t (:background ,gruber-darker-red-1))))

   ;; Slime
   `(slime-repl-inputed-output-face ((t (:foreground ,gruber-darker-red))))

   ;; Tuareg
   `(tuareg-font-lock-governing-face ((t (:foreground ,gruber-darker-yellow))))

   ;; Speedbar
   `(speedbar-directory-face ((t ,(list :foreground gruber-darker-niagara
                                        :weight 'bold))))
   `(speedbar-file-face ((t (:foreground ,gruber-darker-fg))))
   `(speedbar-highlight-face ((t (:background ,gruber-darker-bg+1))))
   `(speedbar-selected-face ((t (:foreground ,gruber-darker-red))))
   `(speedbar-tag-face ((t (:foreground ,gruber-darker-yellow))))

   ;; Which Function
   `(which-func ((t (:foreground ,gruber-darker-wisteria))))

   ;; Whitespace
   `(whitespace-space ((t ,(list :background gruber-darker-bg
                                 :foreground gruber-darker-bg+1))))
   `(whitespace-tab ((t ,(list :background gruber-darker-bg
                               :foreground gruber-darker-bg+1))))
   `(whitespace-hspace ((t ,(list :background gruber-darker-bg
                                  :foreground gruber-darker-bg+2))))
   `(whitespace-line ((t ,(list :background gruber-darker-bg+2
                                :foreground gruber-darker-red+1))))
   `(whitespace-newline ((t ,(list :background gruber-darker-bg
                                   :foreground gruber-darker-bg+2))))
   `(whitespace-trailing ((t ,(list :background gruber-darker-red
                                    :foreground gruber-darker-red))))
   `(whitespace-empty ((t ,(list :background gruber-darker-yellow
                                 :foreground gruber-darker-yellow))))
   `(whitespace-indentation ((t ,(list :background gruber-darker-yellow
                                       :foreground gruber-darker-red))))
   `(whitespace-space-after-tab ((t ,(list :background gruber-darker-yellow
                                           :foreground gruber-darker-yellow))))
   `(whitespace-space-before-tab ((t ,(list :background gruber-darker-brown
                                            :foreground gruber-darker-brown))))

   ;; company-mode
   `(company-tooltip ((t (:foreground ,gruber-darker-fg :background ,gruber-darker-bg+1))))
   `(company-tooltip-annotation ((t (:foreground ,gruber-darker-brown :background ,gruber-darker-bg+1))))
   `(company-tooltip-annotation-selection ((t (:foreground ,gruber-darker-brown :background ,gruber-darker-bg-1))))
   `(company-tooltip-selection ((t (:foreground ,gruber-darker-fg :background ,gruber-darker-bg-1))))
   `(company-tooltip-mouse ((t (:background ,gruber-darker-bg-1))))
   `(company-tooltip-common ((t (:foreground ,gruber-darker-green))))
   `(company-tooltip-common-selection ((t (:foreground ,gruber-darker-green))))
   `(company-scrollbar-fg ((t (:background ,gruber-darker-bg-1))))
   `(company-scrollbar-bg ((t (:background ,gruber-darker-bg+2))))
   `(company-preview ((t (:background ,gruber-darker-green))))
   `(company-preview-common ((t (:foreground ,gruber-darker-green :background ,gruber-darker-bg-1))))

   ;; Proof General
   `(proof-locked-face ((t (:background ,gruber-darker-niagara-2))))
   ))

(provide-theme 'gruber-darker)

;; 启用主题
(enable-theme 'gruber-darker)

;; 同步启动窗口配色，避免启动时颜色闪烁
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
```
