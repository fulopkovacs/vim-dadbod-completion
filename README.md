# vim-dadbod-completion

Database auto completion powered by [vim-dadbod](https://github.com/tpope/vim-dadbod).
Supports:
* [coc.nvim](https://github.com/neoclide/coc.nvim)
* [deoplete.nvim](https://github.com/Shougo/deoplete.nvim)
* [completion-nvim](https://github.com/haorenW1025/completion-nvim)
* [nvim-compe](https://github.com/hrsh7th/nvim-compe)
* [nvim-cmp](https://github.com/hrsh7th/nvim-cmp)
* [ddc.vim](https://github.com/Shougo/ddc.vim)
* Built in `omnifunc`

![coc-db](https://user-images.githubusercontent.com/1782860/78941173-717f6680-7ab7-11ea-91b3-18bf178b3735.gif)

## Install

**Dependencies**:
* [vim-dadbod](https://github.com/tpope/vim-dadbod)

For [coc.nvim](https://github.com/neoclide/coc.nvim)
```
:CocInstall coc-db
```

For `deoplete`, `completion-nvim`, `nvim-compe`, `ddc` and `omnifunc`, install it with your favorite plugin manager.

```vimL
function! PackagerInit() abort
  packadd vim-packager
  call packager#init()
  call packager#add('kristijanhusak/vim-packager', { 'type': 'opt' })
  call packager#add('tpope/vim-dadbod')
  call packager#add('kristijanhusak/vim-dadbod-completion')

  call packager#add('Shougo/deoplete.nvim')
  "or
  call packager#add('haorenW1025/completion-nvim')
  "or
  call packager#add('hrsh7th/nvim-compe')
  "or
  call packager#add('vim-denops/denops.vim')
  call packager#add('Shougo/ddc.vim')
endfunction

" For built in omnifunc
autocmd FileType sql setlocal omnifunc=vim_dadbod_completion#omni

" hrsh7th/nvim-compe
let g:compe.source.vim_dadbod_completion = v:true

" hrsh7th/nvim-cmp
  autocmd FileType sql,mysql,plsql lua require('cmp').setup.buffer({ sources = {{ name = 'vim-dadbod-completion' }} })

" For completion-nvim
augroup completion
  autocmd!
  autocmd BufEnter * lua require'completion'.on_attach()
  autocmd FileType sql let g:completion_trigger_character = ['.', '"', '`', '[']
augroup END

" Shougo/ddc.vim
call ddc#custom#patch_filetype(['sql', 'mysql', 'plsql'], 'sources', 'dadbod-completion')
call ddc#custom#patch_filetype(['sql', 'mysql', 'plsql'], 'sourceOptions', {
\ 'dadbod-completion': {
\   'mark': 'DB',
\   'isVolatile': v:true,
\ },
\ })

" Source is automatically added, you just need to include it in the chain complete list
let g:completion_chain_complete_list = {
    \   'sql': [
    \    {'complete_items': ['vim-dadbod-completion']},
    \   ],
    \ }
" Make sure `substring` is part of this list. Other items are optional for this completion source
let g:completion_matching_strategy_list = ['exact', 'substring']
" Useful if there's a lot of camel case items
let g:completion_matching_ignore_case = 1
```

## Features
* Autocomplete table names, with automatic quoting where needed. Works for all schemes that [vim-dadbod](https://github.com/tpope/vim-dadbod) supports.
* Autocomplete table columns, context aware. Also knows to read aliases (`select * from mytable tbl where tbl.id = 1`). Currently works for `PostgreSQL`, `MySQL`, `Oracle`, `SQLite` and `SQLserver/MSSQL`.
* Out of the box integration with [vim-dadbod-ui](https://github.com/kristijanhusak/vim-dadbod-ui)

## How it works
* If an sql buffer is created by [vim-dadbod-ui](https://github.com/kristijanhusak/vim-dadbod-ui), it reads all the configuration from there. It should work out of the box.
* If `vim-dadbod-ui` is not used, [vim-dadbod](https://github.com/tpope/vim-dadbod) `g:db` or `b:db` is used. If you want, you can also add `b:db_table` to limit autocompletions to that table only.

## Settings
Default mark for completion items is `[DB]`. To change it, add this to vimrc:
```
let g:vim_dadbod_completion_mark = 'MYMARK'
```

## Commands
This plugin caches the database tables and columns to leverage maximum performance. If you want to clear the cache at any point just run:

```
:DBCompletionClearCache
```

## Todo
* [ ] Integration for column autocompletion with more database types
