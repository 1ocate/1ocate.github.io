---
extends: _layouts.post
section: content
title: WSL에서 Vim의 레지스터 "0 (Yank register)와 윈도우 클립보드와 연동하기
date: 2022-10-18
modify: 2022-11-09
description: This is your first blog post.
cover_image: 
---

터미널을 통해서 Vim을 사용할 때 라인이 길어지게  되면 드레그 복사 하기가 번거로워진다.

Vim의 레지스터 "0(yank)와 윈도우의 Clipboard를 연결 하는 [방법](https://hidekuma.github.io/vim/wsl/synchronize-system-clipboard-vim-on-WSL/)은 검색을 통해 쉽게 찾을 수 있다.

다만 한가지 아쉬운 점이 있는데, Yank 할때만 클립보드에 연동되는 것이 아니라  
삭제할 때 Vim 레지스터 ""를 갱신하게 되면서 **의도하지 않은 클립보드 갱신**이 일어나게 되었다.

고민 중에 [기계인간](https://johngrib.github.io/)님의 [Vim 숫자레지스터 쉬프터 만들기](https://johngrib.github.io/wiki/vim/numbered-register-shift/)라는 글을 보게 되었다.

기계인간님은 내가 겪었던 의도하지 않은 레지스터 갱신을 구별 하기 위해, 특정 레지스터의 기존 값을  
캐시로 변수에 등록하여 이벤트가 발생했을 때 
해당 레지스터와 값을 비교하여 실제적으로 이벤트가 일어났는지 확인 처리 하셨다.

이를 바탕으로 코드를 조합하였다.

다음은 완성된 코드이다.  
[github에서 보기](https://github.com/1ocate/dotfiles/blob/main/nvim/init.vim)

```shell

"F9를 토글하여 Vim 레지스터 '"@'와 윈도우 클립보드 연동 활성여부 선택 (기본 활성)
nnoremap <F9> :call <SID>WSLYank_toggle()<CR>

augroup WSLYank_autocmd

    " 초기화
    let g:wsl_clipboard_enble = 1
    let s:global_yank_cache_0 = @0
    let s:clip = '/mnt/c/Windows/System32/clip.exe' 

    if executable(s:clip)
            autocmd TextYankPost * :call s:WSLYank()
    end
    
    function! s:WSLYank_toggle()
        let g:wsl_clipboard_enble = ! g:wsl_clipboard_enble
        echom "시스템 클립보드 사용 여부 " . g:wsl_clipboard_enble
        let s:clip = ''
    endfunction

    function! s:save_cache()
        let s:global_yank_cache_0 = @0
    endfunction

    function! s:WSLYank()
            if ! v:true == g:wsl_clipboard_enble
                return
            endif
            if s:global_yank_cache_0 != @0
                call system('echo '.shellescape(join(v:event.regcontents, "\<CR>")).' | '.s:clip)
                call s:save_cache()
                return
            endif
    endfunction

augroup END
```

### 참고
[Vim: WSL에서 클립보드에 복사하는 방법](https://hidekuma.github.io/vim/wsl/synchronize-system-clipboard-vim-on-WSL/)  
[Vim 숫자 레지스터 쉬프터 만들기](https://johngrib.github.io/wiki/vim/numbered-register-shift/)

