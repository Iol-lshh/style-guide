# 깃 원격 레포지터리
- 로컬 레포지터리의 브랜치들을 원격에 저장해두는 것
- fetch: 원격 레포지터리의 브랜치를 내 로컬 레포지터리에 땡겨오기
- merge, rebase: 로컬 레포지터리 -> 로컬 디렉터리
- pull: 
    - git pull = git fetch(로컬 레포지터리 적용) + git merge(로컬 디렉터리 적용)
    - git pull --rebase = git fetch + git rebase
- 동기화 = git pull + git push


# git merge vs git rebase
0. 두 브랜치 통합시
```
- o - o - H - A - B (작업중 브랜치)
        |
          P - Q - R (병합할 브랜치)
```

1. git merge
- 두 브랜치 내용을 합치고, merge 커밋을 생성한다.
```
- o - o - H - A - B - X
        |           |
          P - Q - R 
```

2. git rebase
- 커밋 시점과 관계 없이, merge 하는 브랜치의 커밋을 맨 뒤에 붙인다.
```
- o - o - H - A - B - P' - Q' - R'
```

## rebase의 충돌 원인
### 1. 작업 내용 충돌
- 파일 내용 작업
    1. 충돌 부분 수정하고, 리베이스 재진행
        - `git rebase --continue`
    2. 리베이스 원복
        - `git rebase --abort`

### 2. rebase로 인한 충돌
0. 브랜치 통합시,
```
              b1 - b2 (b)
            | 
- o - o - H - A - B (메인)
        |
          a1 - a2 - a3 (a)
```

1. 메인에서 a 브렌치를 rebase
```
              b1 - b2 
            x 
- o - o 
        |
          a1 - a2 - a3 - H' - A' - B' (메인)
```
- b 브렌치는 main에서 떨어져나가버렸다.. (H가 없어져버렸다..)
- rebase는 충돌시, rebase 명령 도중에 멈춘다. 충돌 노드 이후, 커밋 갯수 만큼 오류가 난다.
    - (H-b1-b2 vs a1-a2-a3-H'-A'-B')

## Fast-forward merge
- rebase 이후, merge하여, H' 를 H로 만들어 주는 것
    - (H가 살아 있어, b 브랜치에 문제가 발생하지 않는다.)
    - 끼워넣기인 셈..
1. a에서 메인을 리베이스
```
              b1 - b2 
            | 
- o - o - H - A - B
        |
          a1 - a2 - a3 - H' - A' - B' 

```
2. 메인에서 a를 머지
```
                             b1 - b2 
                           | 
- o - o                  H - A - B
        |              |
          a1 - a2 - a3
```
3. b에서 메인을 리베이스
```
                             b1 - b2 - A' - B' 
                           | 
- o - o                  H - A - B
        |              |
          a1 - a2 - a3
```
4. 메인에서 b를 머지
```
                             b1 - b2  
                           |         |
- o - o                  H             A - B
        |              |
          a1 - a2 - a3
```
