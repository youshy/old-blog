---
layout: post
title: Rust - Vim Setup For Dummies
---

Before any Gopher will start yell at me - no, I'm not bailing on Go, I'm just checking what other options I can have to build VSTs instead of C++...

## Getting Vim to work with Rust

I'll try to be as quick as possible here:

1. Get Vim (no doy...)
2. Install [Rust](https://www.rust-lang.org/learn/get-started)
3. Install [coc](https://github.com/neoclide/coc.nvim) with your favourite [plugin manager](https://github.com/junegunn/vim-plug)
4. Install [Rust-analyzer](https://rust-analyzer.github.io/manual.html#rust-analyzer-language-server-binary)
5. Run in Vim: `CocInstall coc-rust-analyzer` - more info [here](https://rust-analyzer.github.io/manual.html#vimneovim)
6. Add `let g:rustfmt_autosave = 1` to your `.vimrc` to run `rustfmt` (autoformatting) on save

At step 5, Vim might ask you to install some extra stuff - just roll with it!

If you have any questions, suggestions or anything else - grab me on [linkedin](https://www.linkedin.com/in/arturkondas/)!

