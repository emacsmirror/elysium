<div align="center">
  <img src="https://img.shields.io/badge/license-GPL_3-green.svg" />
  <img src="https://img.shields.io/badge/Supports-Emacs_27.1–29.4-blueviolet.svg?style`flat-square&logo`GNU%20Emacs&logoColor`white" />
  <img src="https://github.com/lanceberge/elysium/actions/workflows/ci.yml/badge.svg" />

# elysium

</div>

<p align="center"><img src="image/minotaur.png" width=300px /></p>

  This package lets you automatically apply AI-generated changes as you code. Call M-x `elysium-query`
  and request a set of changes. They will automatically be merged into your code buffer.

![](./image/demo.gif)

You can make queries on a region without leaving the the code buffer

![](./image/demo2.gif)

# Installation

### Using `straight.el`

<details><summary>Toggle</summary>

```emacs-lisp
(use-package elysium
  :straight
  (:host github :repo "lanceberge/elysium" :branch "main" :files ("*.el")
  ...)
```

</details>

### Using `elpaca`

<details><summary>Toggle</summary>

```emacs-lisp
(use-package elysium
  :ensure (:host github :repo "lanceberge/elysium")
  ...)
```

</details>

### Using `package.el`

<details><summary>Toggle</summary>

```emacs-lisp
(use-package elysium
  :vc (:fetcher github :repo lanceberge/elysium)
  ...)
```

</details>

# Customization

```emacs-lisp
(use-package elysium
  :custom
  ;; Below are the default values
  (elysium-window-size 0.33) ; The elysium buffer will be 1/3 your screen
  (elysium-window-style 'vertical)) ; Can be customized to horizontal

(use-package gptel
  :custom
  (gptel-model "claude-3-5-sonnet-20240620")
  :config
  (defun read-file-contents (file-path)
    "Read the contents of FILE-PATH and return it as a string."
    (with-temp-buffer
      (insert-file-contents file-path)
      (buffer-string)))
  (defun gptel-api-key ()
    (read-file-contents "~/secrets/claude_key"))
  (setq
   gptel-backend (gptel-make-anthropic "Claude"
                   :stream t
                   :key #'gptel-api-key)))
```

Use `smerge-mode` to then merge in the changes

```emacs-lisp
(use-package smerge-mode
  :ensure nil
  :hook
  (prog-mode . smerge-mode))
```

# Usage

| Function                                | Description                             |
| --------------------------------------- | --------------------------------------- |
| `elysium-query`                         | send a query to the `gptel` backend     |
| `elysium-keep-all-suggested-changes`    | keep all of the AI-suggested changes    |
| `elysium-discard-all-suggested-changes` | discard all of the AI-suggested changes |
| `smerge-next`                           | go to the next conflicting hunk         |
| `smerge-previous`                       | go to the next conflicting hunk         |
| `smerge-keep-other`                     | keep this set of changes                |
| `smerge-keep-mine`                      | discard this set of changes             |
| `elysium-toggle-window`                 | toggle the chat window                  |

# Notes

  `elysium` uses [gptel](https://github.com/karthink/gptel) as a backend. It supports any of the models supported by `gptel`, but currently (9/24)
  Claude 3-5 Sonnet seems to be the best for generating code.

  If there is a region active, then `elysium` will send only that region to the LLM. Otherwise, the entire code buffer will be sent. If you're using `Claude`, then I recommend only ever sending a region to avoid getting rate-limited.

# Planned Features

- Implementing [Prompt Caching](https://docs.anthropic.com/en/docs/build-with-claude/prompt-caching) with Anthropic to let us send more queries before getting rate-limited