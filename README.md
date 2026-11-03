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


;; ==================================================
;; 二、考场必备快捷键
;; ==================================================
;; 全选 / 撤销 / 保存
(global-set-key (kbd "C-a") 'mark-whole-buffer)
(global-set-key (kbd "C-z") 'undo)
(global-set-key (kbd "C-S-z") 'undo-redo)
(global-set-key (kbd "C-s") 'save-buffer)

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
;;    解决花括号换行不缩进、不分列问题
;; ==================================================
;; 全局缩进基础规则
(setq-default indent-tabs-mode nil)   ; 统一用空格缩进
(setq-default tab-width 4)            ; Tab 显示宽度

;; 电动缩进：按回车自动根据语法缩进
(electric-indent-mode 1)

;; 自动配对括号、引号
(electric-pair-mode 1)

;; 光标在 {} 中间按回车，自动将右括号挪到下一行（分列效果）
(setq electric-pair-open-newline-between-pairs t)

;; 注意：已删除原配置中 (global-set-key RET 'newline-and-indent)
;; 原因：手动绑定会覆盖 electric 模式的分列能力，导致 {} 回车不分列
;; 如需纯换行不缩进，使用 C-j 即可


;; ==================================================
;; 四、C/C++ 专项（CC Mode）
;; ==================================================
(setq c-default-style "linux")        ; 代码风格：k&r / linux / allman / gnu

(add-hook 'c-mode-common-hook
          (lambda ()
            (setq c-basic-offset 4)   ; 缩进单位 4 空格
            (setq c-electric-flag t)  ; 输入 { } ; 自动重缩进
            (setq indent-tabs-mode nil)))


;; ==================================================
;; 五、界面与编辑体验优化
;; ==================================================
;; 显示行号
(global-display-line-numbers-mode t)

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

(desktop-save-mode t)
(setq desktop-restore-frames t)

;; ==================================================
;; 六、自定义暗色主题（Gruber Darker 风格）
;; ==================================================
;; 安全配色工具：face 不存在时自动跳过，避免报错
(defun my-set-face-foreground (face color)
  (when (facep face)
    (set-face-foreground face color)))

(defun my-set-face-background (face color)
  (when (facep face)
    (set-face-background face color)))

(defun my-set-face-attribute (face &rest attrs)
  (when (facep face)
    (apply #'set-face-attribute face nil attrs)))

;; 色板定义 - Gruber Darker 风格，修改这里即可全局同步
(defvar my-dark-bg          "#161616")   ; 主背景：深炭灰，Gruber标志性低饱和底色
(defvar my-dark-fg          "#e4e4e4")   ; 默认文字：暖米白，低对比度不刺眼
(defvar my-dark-muted       "#808080")   ; 注释/次要文字：中性灰，柔和不抢戏
(defvar my-dark-cursor      "#eade9e")   ; 光标：暖黄色，和函数名同色系
(defvar my-dark-blue        "#96a6c8")   ; 关键字/链接：灰调淡蓝，低饱和不刺眼
(defvar my-dark-green       "#95e454")   ; 字符串：柔和嫩绿色
(defvar my-dark-gold        "#eade9e")   ; 函数名：米黄色，Gruber经典配色
(defvar my-dark-purple      "#cda869")   ; 变量：暖棕黄，替代原紫色更贴合风格
(defvar my-dark-red         "#f43841")   ; 警告/常量：低饱和红
(defvar my-dark-cyan        "#7bb6c2")   ; 类型/内置：灰调青蓝色
(defvar my-dark-mode-bg     "#282828")   ; 活动模式行背景
(defvar my-dark-mode-inact  "#121212")   ; 非活动模式行背景
(defvar my-dark-highlight   "#282828")   ; 行高亮/选区底

;; 窗口全局属性
(setq default-frame-alist
      `((vertical-scroll-bars . nil)
        (internal-border-width . 0)
        (top . 25)
        (left . 45)
        (width . 120)
        (height . 40)
        (background-color . ,my-dark-bg)
        (foreground-color . ,my-dark-fg)
        (cursor-color . ,my-dark-cursor)
        (mouse-color . ,my-dark-cursor)
        (tool-bar-lines . 0)
        (menu-bar-lines . 1)
        (scroll-bar-lines . 0)
        (right-fringe . nil)
        (left-fringe . nil)))
(setq initial-frame-alist default-frame-alist)

;; 基础底色兜底
(my-set-face-foreground 'default my-dark-fg)
(my-set-face-background 'default my-dark-bg)
(my-set-face-background 'fringe my-dark-bg)

;; 语法高亮
(my-set-face-foreground 'font-lock-comment-face       my-dark-muted)
(my-set-face-foreground 'font-lock-doc-face           my-dark-muted)
(my-set-face-foreground 'font-lock-string-face        my-dark-green)
(my-set-face-foreground 'font-lock-keyword-face       my-dark-blue)
(my-set-face-foreground 'font-lock-function-name-face my-dark-gold)
(my-set-face-foreground 'font-lock-variable-name-face my-dark-purple)
(my-set-face-foreground 'font-lock-constant-face      my-dark-red)
(my-set-face-foreground 'font-lock-type-face          my-dark-cyan)
(my-set-face-foreground 'font-lock-builtin-face       my-dark-cyan)
(my-set-face-foreground 'font-lock-warning-face       my-dark-red)
(my-set-face-foreground 'font-lock-preprocessor-face  my-dark-gold)

;; 选区 / 高亮 / 行号
(my-set-face-foreground 'region "#ffffff")
(my-set-face-background 'region "#33415e")
(my-set-face-background 'highlight my-dark-highlight)
(my-set-face-background 'hl-line   my-dark-highlight)

(my-set-face-background 'line-number my-dark-bg)
(my-set-face-foreground 'line-number "#464646")
(my-set-face-foreground 'line-number-current-line my-dark-cursor)
(my-set-face-background 'line-number-current-line my-dark-highlight)

;; 括号匹配
(my-set-face-attribute 'show-paren-match
                    :foreground "#ffffff"
                    :background "#4a4a4a")
(my-set-face-attribute 'show-paren-mismatch
                    :foreground "#ffffff"
                    :background my-dark-red)

;; 搜索与替换
(my-set-face-foreground 'isearch "#ffffff")
(my-set-face-background 'isearch my-dark-gold)
(my-set-face-background 'lazy-highlight "#333333")
(my-set-face-background 'query-replace my-dark-highlight)

;; 模式行
(my-set-face-attribute 'mode-line
                    :foreground my-dark-fg
                    :background my-dark-mode-bg
                    :box nil)
(my-set-face-attribute 'mode-line-inactive
                    :foreground my-dark-muted
                    :background my-dark-mode-inact
                    :box nil)

;; 迷你缓冲区与补全
(my-set-face-foreground 'minibuffer-prompt       my-dark-blue)
(my-set-face-foreground 'completions-common-part my-dark-muted)
(my-set-face-foreground 'completions-first-difference my-dark-gold)

;; 通用界面
(my-set-face-foreground 'link           my-dark-blue)
(my-set-face-foreground 'link-visited   my-dark-purple)

;; 帮助文档
(with-eval-after-load 'help-mode
  (my-set-face-foreground 'help-key-binding        my-dark-gold)
  (my-set-face-foreground 'help-function-arglist   my-dark-muted))

;; 编译输出
(with-eval-after-load 'compile
  (my-set-face-foreground 'compilation-error   my-dark-red)
  (my-set-face-foreground 'compilation-warning my-dark-gold)
  (my-set-face-foreground 'compilation-info    my-dark-green))

;; Dired 文件管理器
(with-eval-after-load 'dired
  (my-set-face-foreground 'dired-directory  my-dark-blue)
  (my-set-face-foreground 'dired-symlink    my-dark-purple)
  (my-set-face-foreground 'dired-executable my-dark-green)
  (my-set-face-foreground 'dired-warning    my-dark-red))

;; 空白字符显示
(with-eval-after-load 'whitespace
  (my-set-face-foreground 'whitespace-space    "#2e2e2e")
  (my-set-face-foreground 'whitespace-tab      "#2e2e2e")
  (my-set-face-foreground 'whitespace-newline  "#2e2e2e")
  (my-set-face-foreground 'whitespace-trailing my-dark-red))


;; ==================================================
;; 七、Custom 自动生成区（请勿手动编辑）
;; ==================================================
(custom-set-variables
 ;; custom-set-variables was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(ansi-color-faces-vector
   [default default default italic underline success warning error])
 '(blink-cursor-mode nil)
 '(column-number-mode t)
 '(cua-mode t)
 '(global-display-line-numbers-mode t)
 '(size-indication-mode t)
 '(tab-bar-mode t)
 '(tool-bar-mode nil))
(custom-set-faces
 ;; custom-set-faces was added by Custom.
 ;; If you edit it by hand, you could mess it up, so be careful.
 ;; Your init file should contain only one such instance.
 ;; If there is more than one, they won't work right.
 '(default ((t (:family "Iosevka" :foundry "nil" :slant normal :weight regular :height 120 :width normal)))))
```
