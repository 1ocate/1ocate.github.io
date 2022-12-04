---
extends: _layouts.post
section: content
title: Fish shell 세팅
date: 2022-12-04
description: This is your first blog post.
cover_image: 
---

# 설치(Silicon Mac)

```bash

brew install fish

# 쉘 등록
sudo echo '/opt/homebrew/bin/fish' >> sudo /etc/shells > /dev/null

# 기본 쉘 변경

chsh -s /opt/homebrew/bin/fish

# fish 경로 추가
fish_add_path /opt/homebrew/bin

#fisher 설치 
curl -sL https://git.io/fisher | source && fisher install jorgebucaran/fisher

#powerline theme
fisher install IlanCosman/tide@v5

#fzf 플러그인 의존성 해결
brew install fd
brew install bat
brew install fzf

#fzf 플러그인 설치
fisher install PatrickF1/fzf.fish







```

### 참고
[sudo echo 명령어 사용시 Permission denied 문제 해결하기](https://www.lesstif.com/lpt/sudo-echo-permission-denied-89556053.html)  
[Installing Fish shell on MacOS (Intel and M1) using brew
](https://gist.github.com/gagarine/cf3f65f9be6aa0e105b184376f765262)

