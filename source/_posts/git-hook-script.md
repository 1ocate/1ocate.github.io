---
extends: _layouts.post
section: content
title: 특정 브랜치에 특정 커밋 메세지를 포함 하지 않는 경우 푸시 제어하기
date: 2022-11-01
modify: 2022-11-03
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
Deploy에 동일하게 cherry-pick을 한 후에 ```git commit --amend``` 하여 "Deploy" 단어를 추가하고 커밋해서 푸시하였다.

``` 
이번에는 그냥 배포하지만 다음부터는 Git 컨벤션을 지켜주시겠어요?
```

나름 내부의 룰을 잘 지킨다고 생각하고 있었는데, 배포 담당자에게 Deploy 브랜치 커밋에 "Deploy"라는 단어가 포함되지 않았다는 이야기를 들었다.   

확인해보니, cherry-pick 이후에 커밋 메세지 수정을 잊었던 경우가 종종 있었고, 반복되다보니 담당자가 이야기를 해준 것이였다.

### 문제

특정 브랜치에 요구되는 커밋 조건이 있는데, cherry-pick으로 다른브랜치의 커밋을 가져와서 푸시하는 경우 커밋을 수정하는 것을 잊을 때가 많다.

### 해결방법

특정 브랜치에 요구되는 커밋 조건을 확인하여 조건에 부합하지 않으면 푸시 제한을 하게 하면 어떨까?

Git 정책에 [관련](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-%EC%A0%95%EC%B1%85-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)된 예제를 확인해보니
[Git Hook](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-Git-Hooks)으로 각 조건에 따라 스크립트를 사용 할 수 있는 것을 알게 되었다.

이번 문제는 cherry-pick 후 push 할때 스크립트를 실행해야 하므로 pre-push를 사용하였다.

> pre-push 훅은 git push 명령을 실행하면 동작하는데 리모트 정보를 업데이트 하고 난 후 리모트로 데이터를 전송하기 전에 동작한다.  
> 리모트의 이름과 주소를 파라미터로 전달받으며 stdin 을 통해 업데이트 할 해시 리스트를 전달받는다. 
> Push 하기 전에 커밋이 유효한지 확인하는 용도로 사용할 수 있다. 
> 훅에서 0이 아닌 값을 반환하면 Push를 중지시킨다.

그리고 다음의 경로에 아래의 내용을 넣었다. "Project_name/.git/hooks/pre-push"

```bash

#!/bin/bash

branch=(`git branch --show-current`)
zero=$(git hash-object --stdin </dev/null | tr '[0-9a-f]' '0')

if test "$branch" = "DEPLOY"
then
    while read local_ref local_oid remote_ref remote_oid
    do
        # 리모트 서버에 푸시 할 것이 없으면 그대로 종료
        if test "$local_oid" = "$zero"
        then
            # Handle delete
            :
        else
            # 커밋이 1개인지 범위인지 확인
            if test "$remote_oid" = "$zero"
            then
                # New branch, examine all commits
                range="$local_oid"
            else
                # Update to existing branch, examine new commits
                range="$remote_oid..$local_oid"
            fi

            # 커밋 메세지 체크
            SHA1s=(`git rev-list $range`)
            for SHA1 in "${SHA1s[@]}"
            do
                #Hash 값을 통해 커밋 메세지 가져오기
                commit=$(git cat-file commit "$SHA1" | sed '1,/^$/d')
                check_commit=$(echo $commit | grep -i "$branch" )

                # 커밋에 특정 브랜치 푸시에 필요한 내용 체크
                if ! test -n "$check_commit"
                then
                    # 특정문자를 포함하지 않는 커밋의 Hash 저장
                    errors+=("<$SHA1> $commit")
                fi
            done
            
            # 조건에 맞지 않으면 해당 커밋을 출력하고 종료
            if  test -n "$errors"
            then
                
                echo >&2 "Found commit message not include $branch, not pushing"
                for  error in "${errors[@]}"
                do
                    echo "$error"
                done
                exit 1
            fi
        fi
    done
fi

exit 0 

```
이제 특정 브랜치(위의 예시에서는 DEPLOY)에 cherry-pick 후 push 할때 커밋에 특정 문구 (위의 예시에서는 Deploy)가 포함되지 않으면 해당 커밋을 출력해주고 푸시가 되지 않는다.   



### 참고
[Git맞춤 - Git Hooks](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-Git-Hooks) 
[Git맞춤 - 정책 구현하기](https://git-scm.com/book/ko/v2/Git%EB%A7%9E%EC%B6%A4-%EC%A0%95%EC%B1%85-%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0)  
