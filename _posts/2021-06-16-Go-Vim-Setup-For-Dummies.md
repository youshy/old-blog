---
layout: post
title: Go - Vim Setup For Dummies
---

So, I did the [Rust setup](https://akondas.com/blog/Rust-Vim-Setup-For-Dummies) recently, so I've figured out I'll do the one for Go as well (as I work with Go daily).

## Getting Vim to work with Go

1. Get Vim
2. Install [Go](https://golang.org/dl/)
3. Install [coc](https://github.com/neoclide/coc.nvim) and [vim-go](https://github.com/fatih/vim-go)  with your favourite [plugin manager](https://github.com/junegunn/vim-plug)
4. Run in Vim: `CocInstall coc-go` - more info [here](https://github.com/josa42/coc-go)
5. Add to your `.vimrc`:

```bash
"
" Go setup
"

let g:go_fmt_command = "goimports"
let g:go_gocode_propose_source = 0
let g:go_auto_type_info = 1
let g:go_version_warning = 0
```

Do you run your setup differently? Anything you would like to know more about? Hit me up on [linkedin](https://www.linkedin.com/in/arturkondas/)!
