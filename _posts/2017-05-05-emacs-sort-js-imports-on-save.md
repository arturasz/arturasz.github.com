---
layout: post
title:  "Sorting js imports on file save emacs"
categories: coding
tags: javascript wallabyjs fake-wallaby mocha
---

Sorts your js imports by assigning weights

{% highlight emacs-lisp %}

  (defun sort-js-imports ()
    (interactive)
    (sort-js-imports-region nil (point-min) (get-end-of-js-import))
    )

  (defun get-end-of-js-import ()
    (save-excursion
      (goto-char (point-max))
      (search-backward-regexp "import")
      (word-search-forward "from" nil t)
      (line-end-position)
      )
    )

  (defun sort-js-imports-region (reverse beg end)
    (interactive "P\nr")
    (save-excursion
      (save-restriction
        (narrow-to-region beg end)
        (goto-char (point-min))
        (let ;; To make `end-of-line' and etc. to ignore fields.
            ((inhibit-field-text-motion t))
          (sort-subr reverse
                     'forward-line
                     (lambda () (search-forward "from" nil t) (end-of-line))
                     (lambda ()
                       (let (
                             (skip-amount (save-excursion (search-forward "from" nil t)))
                             (skip-amount-end (line-end-position))
                             )
                         (cons skip-amount skip-amount-end)
                         )
                       )
                     nil
                     (lambda (a b)
                       (let ((s1 (buffer-substring (car a) (cdr a)))
                             (s2 (buffer-substring (car b) (cdr b))))
                         (message (format "[%s] [%s] [%s] [%s] [%s]\n" s1 s2 a b (calculate-string-priority s1 s2)))
                         (calculate-string-priority s1 s2)
                       )
                     ))))))

  (defun calculate-string-priority (s1 s2)
    (< (+ (calculate-js-import-priority s1) (if (string< s1 s2) -1 1)) (calculate-js-import-priority s2)))

  (defun calculate-js-import-priority (import)
    (catch 'val
      (cond
       ((> (or (string-match "lodash" import) -2) -1) (throw 'val 0))
       ((> (or (string-match "'react'" import) -2) -1) (throw 'val 10))
       ((> (or (string-match "/constants/" import) -2) -1) (throw 'val 30))
       ((> (or (string-match "/actions/" import) -2) -1) (throw 'val 40))
       ((> (or (string-match "/components/" import) -2) -1) (throw 'val 50))
       ((> (or (string-match "/containers/" import) -2) -1) (throw 'val 60))
       ((> (or (string-match "/services/" import) -2) -1) (throw 'val 70))
       ((> (or (string-match "/selectors/" import) -2) -1) (throw 'val 80))
       ((> (or (string-match "/hoc/" import) -2) -1) (throw 'val 90))
       ((> (or (string-match "scss" import) -2) -1) (throw 'val 100))
       ((> (or (string-match "../" import) -2) -1) (throw 'val 110))
       (t (throw 'val 20)))
      )
    )

  (add-hook 'js-mode-hook
            (lambda () (add-hook 'before-save-hook #'sort-js-imports nil 'local)))

{% endhighlight %}

