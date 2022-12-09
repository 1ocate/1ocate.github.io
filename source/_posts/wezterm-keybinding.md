---
extends: _layouts.post
section: content
title: Wezterm에서 Vim 비주얼 블럭모드(ctrl + q) 한번에 입력이 안될 경우
date: 2022-12-09
description: 
cover_image: 
---

[wezterm](https://wezfurlong.org/wezterm/)은 윈도우, 리눅스, 맥에서 사용할 수 있는 러스트기반의 터미널이다. 

Vim에서 비주얼모는 일반모드에서 'v'키를 입력하여 진입 가능하고 
커서를 움직여서 텍스트를 선택 할 수 있다.

비주얼 블럭모드는 ```ctrl + v``` 또는 ```ctrl + q```를 통해서 진입 할 수 있는데 
여러줄을 공백 또는 블럭으로 모양을 만들어서 편집이 가능하다.
나는 붙여넣기로 ```ctrl + v```를 사용하고 있어서 ```ctrl + q```를 통해 비주얼 블럭모드로 진입한다.

오늘 맥에서 wezterm을 사용하여 Vim으로 작업해보니
비주얼모드로 진입하기 위해 ```ctrl + q```를 두번 입력해야 했는데 찾아보니 이슈가 있었다.

[CTRL-Q needs to be pressed twice to register in macOS #2630
](https://github.com/wez/wezterm/issues/2630)

맥의 최신 릴리즈에서 CTRL키가 맥의 입력기 IME를 통해 제어 되면서
wezterm의 최신 나이틀리 버전에 `use_ime` 옵션이 생기게 되었고 기본으로 활성화 ```use_ime=true
```  된다고 한다.

# 해결
wezterm 설정 ```wezterm.lua``` 파일에 다음을 추가 
```lua
use_ime=false 
```
또는 use_ime 설정을 바꾸고 싶지 않다면

```lua
{ mods = "CTRL", key = "q", action=wezterm.action{ SendString="\x11" } },
```
을 추가하여 해결 할 수 있다.

본인은 ```use_ime=true``` 설정을 사용하기 위해 아래의 방법대로 설정하였다.   
[설정 예시](https://github.com/1ocate/dotfiles/blob/main/.wezterm.lua)

### 참고
[CTRL-Q needs to be pressed twice to register in macOS #2630
](https://github.com/wez/wezterm/issues/2630)   
[chatGPT](https://chat.openai.com/chat) (번역 참고)
