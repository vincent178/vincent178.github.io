+++
title = "Lsp"
date = "2021-10-15T10:56:07+08:00"
author = ""
authorTwitter = "" #do not include @
cover = ""
tags = ["", ""]
keywords = ["", ""]
description = ""
showFullContent = false
draft = true
+++

Neovim 0.5.0 实现 lsp 客户端


配置
https://github.com/neovim/nvim-lspconfig


功能
Inline diagnostics are enabled automatically, e.g. syntax errors will be
annotated in the buffer.

auto-completion


lsp-handler
function(err, method, result, client_id, bufnr, config) (result, err)

function(err, method, params, client_id, bufnr, config)

