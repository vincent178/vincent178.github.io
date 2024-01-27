---
layout: post
title: "Nvim Setup For Golang"
date: 2020-02-01T00:00:00+08:00
category: go
---


> check help: lsp for more info.

Nvim released lots of very useful features recently, `0.5.0` version brings LSP client into its core, and `0.6.0` adds virtual text support. 
Those features make Nvim a powerful IDE like text editor. With some settings, now it's my daily editor for all my work espicially for Golang.

## Version
The latest stable version is `0.6.0`, we can check version by command `:version` inside Nvim.

## Gopls
Before goes into Nvim configuration, we need install `gopls`, the LSP server. We can `go install golang.org/x/tools/gopls@latest` to install `gopls`.

## Nvim configuration

```
Plug 'neovim/nvim-lspconfig'
```



## Autocomplete and Go to definition

### Autocomplete

### Go to definition

### Diagonostics

### Refactor

## Auto Import package

## Auto Formatting
