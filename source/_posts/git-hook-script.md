---
extends: _layouts.post
section: content
title: 특정 브랜치에 특정 커밋 메세지를 포함 하지 않는 경우 푸시 제어하기
date: 2022-11-01
description: This is your first blog post.
cover_image: 
---

현재 회사에서 크게 3가지 단계로 검증 후 운영서버에 배포하고 있다.

Local 브랜치 > Stage 브랜치 > Deploy 브랜치 > 운영서버 배포

작업을 진행할 때 Local 브랜치 및 Stage 브랜치에서는 Git 컨벤션에 맞게 커밋 메세지를 만들고,
배포전 단계인 Deploy 브랜치에 커밋을 생성할 때 해당 커밋 메세지를 수정하여 Deploy를 넣어준다.   
예) Deploy : xxx 항목 제거

이후 변경된 파일 내역과 함께 배포요청을 하면, 담당자가 운영서버로 배포하게 된다.


나는 보통 Local 브랜치에서 커밋 메세지를 만들고, Stage에 cherry-pick을 한다음 검수가 완료되면
Deploy에 동일하게 cherry-pick을 한 후에 git commit --amend 하여 "Deploy" 단어를 추가하고 커밋해서 푸시하였다.

``` 
이번에는 그냥 배포하지만 다음부터는 Git 컨벤션을 지켜주시겠어요?
```

나름 내부의 룰을 잘 지킨다고 생각하고 있었는데, 배포 담당자에게 Deploy 브랜치 커밋에 "Deploy"라는 단어가 포함되지 않았다는 이야기를 들었다.   

확인해보니, cherry-pick 이후에 커밋 메세지 수정을 잊었던 경우가 종종 있었고, 반복되다보니 담당자가 이야기를 해준 것이였다.

- 수정중

### 참고
[Git맞춤 - 정책 구현하기](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-%EC%A0%95%EC%B1%85-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)  
