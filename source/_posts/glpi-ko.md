---
extends: _layouts.post
section: content
title: Glpi에서 pdf로 리스트 다운로드 할때 한글이 '???'등으로  깨질 경우 확인 사항
date: 2022-10-29
modify: 2022-10-31
description: This is your first blog post.
cover_image: /assets/img/post-cover-image-3.png
---
[Glpi](https://glpi-project.org/)는 헬프데스크 및 IT 자산을 관리하기 위한 오픈 소스 도구다.

Glpi의 자산리스트에서 내보내기(Export) 할때 PDF, SLK, CSV등의 파일로 저장할 수 있는데,   
만약 한글로 작성한 데이터가 있다면 아래의 이미지와 같이 한글이 '???'로 나오게 된다.

![export_pdf_korean_problem](/assets/img/post-3-1.png)

### 해결방법

1) 우측 상단에 프로필 누르고 "내 설정" 클릭
![mysetting](/assets/img/post-3-2.png)

2) "PDF" 내보내기 글꼴 선택 후 "Sung"을 입력해서 나온 한글폰트 2개 중 적절한 폰트 선택
![select_pdf_font](/assets/img/post-3-3.png)

3) 선택 후 출력 테스트
![solve_problem](/assets/img/post-3-4.png)

