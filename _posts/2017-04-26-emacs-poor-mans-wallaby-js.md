---
layout: post
title:  "While wallaby is not support emacs or vim, here is a temp solution with mocha"
categories: coding
tags: javascript wallabyjs fake-wallaby mocha
---

I am using spacemacs <a href="http://spacemacs.org/" target="_blank">http://spacemacs.org/</a>
You will need to install mocha package and add this config to your .spacemacs config or adjust code to whatever you are using.
Basically it switches to a test file and executes tests in that file when you click SPC+m+T

{% highlight emacs-lisp %}
  (require 'projectile)
  (defun atz-exec-if-in-spec-or-switch-and-then-exec (cmd)
    (let ((current-file-name (file-name-nondirectory (buffer-file-name))))
      (if (string-match "spec" current-file-name)
          (funcall cmd)
        (projectile-toggle-between-implementation-and-test)
        (funcall cmd)
        )
      )
    )

  (with-eval-after-load 'js2-mode
    (evil-leader/set-key "mT"
      '(lambda()
         (interactive)
         (atz-exec-if-in-spec-or-switch-and-then-exec 'mocha-test-file)
         )
      )
    )
{% endhighlight %}

