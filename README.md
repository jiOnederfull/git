# git
## Situation 1
1. A 브랜치에서 111111 commit 찍은 후 222222 commit 찍음
2. 222222 commit을 main에 merge
3. 다른 사람이 B 브랜치를 main에 merge
### A 브랜치의 첫 번째 커밋이었던 111111을 main에 merge하고 싶다면?
#### solution
```sh
# 다른 사람이 B 브랜치로 merge한 내용을 업데이트 하기 위해 main의 최신 버전을 다운로드
git checkout main && git pull
# 기존의 브랜치를 local에서 git branch -d A로 지우지 않았으면 local에 브랜치가 있을 것이므로
# git checkout -b A가 아니라 git checkout A를 사용해서 A 브랜치 바라보기
git checkout A
# A의 local 커밋을 main 원격 브랜치의 최신 커밋 위로 재배치
# main의 최신 내용을 A 브랜치로 덮어쓰기 한다고 생각하면 될 듯 (여기서 충돌나는 건 어떻게 해결하는지 확인해보기)
git rebase main
```
> `git rebase BRANCH`는 현재 브랜치의 커밋들을 다른 브랜치(BRANCH)의 최신 상태 위로 재배치하는 명령어
- 111111 commit 을 A 브랜치에 적용하는 명령어인 cherry-pick을 쓰면 다음과 같은 에러가 나옴
```sh
git cherry-pick 111111
```
> `git cherry-pick COMMITHASH`는 특정 커밋 하나를 선택하여 현재 브랜치에 적용하는 명령어로, 원하는 변경 사항만 선택적으로 반영할 때 사용
```sh
Auto-merging {111111과 22222에서 차이나는 파일}
CONFLICT (add/add): Merge conflict in {111111과 22222에서 차이나는 파일}
error: could not apply 111111... {111111과 22222에서 차이나는 파일}
hint: After resolving the conflicts, mark them with
hint: "git add/rm <pathspec>", then run
hint: "git cherry-pick --continue".
hint: You can instead skip this commit with "git cherry-pick --skip".
hint: To abort and get back to the state before "git cherry-pick",
hint: run "git cherry-pick --abort".
```
- {111111과 22222에서 차이나는 파일} 을 예를 들어 "PICK" 이라는 파일이라고 했을 때
- 해당 파일을 열면 아래처럼 나올 것
```sh
<<<<<<< HEAD
현재 브랜치의 내용
=======
cherry-pick 하려는 커밋의 내용
>>>>>>> commit_hash
```
- 아래처럼 필요한 부분만 정리하면 됨
```sh
현재 브랜치의 내용 중 원하는 내용
cherry-pick 하려는 커밋의 내용 중 원하는 내용
```
- 충돌난 파일 스테이징 다시 해주고, cherry-pick 이어서 진행 후 push 하면 끝!
```sh
git add PICK
git cherry-pick --continue
git push
```
