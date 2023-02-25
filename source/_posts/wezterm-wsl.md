---
extends: _layouts.post
section: content
title: WSL사용시 Wezterm설정 Windows와 공유하는법
date: 2023-02-25
description: Wezterm 설정 공유
cover_image: 
---

[wezterm](https://wezfurlong.org/wezterm/)은 윈도우, 리눅스, 맥에서 사용할 수 있는 러스트기반의 터미널이다. 

Windows에서 Wezterm 사용시 Windows에서 설정을 관리하기에 WSL에서 관리가 안되는 문제가 있다.
WSL에서 먼저 'git clone' 이후 수동으로 복사해서 Windows 현재 사용자 디렉토리에 복사해서 넣어줘야 
다른 기기에서 수정된 설정을 사용할 수 있다.

## 해결
mklink는 Windows 심볼릭 링크 명령어이다.
WSL 디렉토리를 네트워크 경로를 통해 접근 가능하므로 네트워크 경로의 파일을
심볼릭 링크 걸어주면, git 으로 관리중인 설정파일에 접근이 가능하다.

Widnows cmd에서 아래 명령 실행

```DOS

# Windows 홈 디렉토리로 이동 (.wezterm.lua 경로)
cd %userprofile% 
rename ".wezter.lua" ".wezterm.lua_bak"
mklink  .wezterm.lua  \\wsl$\Ubuntu\home\locate\.dotfiles\.wezter.lua

```
