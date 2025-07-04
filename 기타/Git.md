Version control system
- centralized
	- Single point of failure
	- subversion
	- Team foundational server
- distributed system
	- Can save snapshot locally on machine
	- Git
	- Mercurial

**Using git**
- Command line
- VS Code

System
- Settings apply to all computer
Global
- 


**Staging Area**
- Upon commit, staging area does not go empty
- ex) git add file1, file2 
	1. make changes to file1 and file2
	2. The files in staging area contains the older version of file1 and file2
- ex) we deleted the original file2
	- git add file2
		- Removes file2 from staging area

**Each commit**
- ID
- Message
- Date/time
- Author
- Complete Snapshot

**Git storing logic**
- Compress the content
- Does not store duplicate content



----
git config --global core.editor "code --wait"
- tell terminal window to wait until we open a new vscode window

git config --global core.autocrlf true
- for mac/linux/windows syntax agreement

```
.gitconfig

[user]
	name = AAA
	email = asdf@gmail.com
[core]
	autocrlf = true # true : windows,  input: mac/linux
	editor = code --wait
	
```


---

**목적 : Local Git 폴더와 Github Repository 를 1:1로 연동 (Sync)
- Local Git / Github Repository 중 둘중 하나를 빈칸으로 두고 진행해야 충돌이 나지 않음
- ITStudy 폴더 안에 00_git 생성
- Github 에 빈 repository 생성 (Readme X)

1. vscode 에서 00_git 폴더 열기

```
git init
git add .
git commit -m "first commit"
git push origin main
```

![[Pasted image 20250702095955.png]]
### Git Command

**git add**
- local -> Staging area
**git commit**
- Staging Area -> Local Repository
**git clone**
- clone all files, as well as all the commited history and create a local repository
- generates .git folder and it sets that as the working directory
**git pull**
- fetch the latest update from the remote repository
**git push**
- Local Repository -> Remote Repository (Github)
**git fetch**
- retrieves changes (branches, tags, commits) but does not merge them into your current branch
- this updates your local view of origin/main without touching your working directory or current branch
**git branch {name}**
- Create new branch
**git checkout {name}**
- change working branch to {name}
**git status**
- list all files that has been "changed" on 
- -s : 변경된 파일 이름 표시
- -b : 브랜치 이름 표시

**git revert {input}**
- input : 
	- HEAD, 
	- HEAD~1
		- one commit before head
	- HEAD~2
		- two commit before head
	- a1b2c3d (specific commit ID)
		- can find commit id via "git log"
	- HEAD~3...HEAD
		- HEAD~3, ~2, ~1, HEAD

**파일관리 상태**
- Untracked
	- 관리 대상이 아님
- Tracked
	- Unmodified
		- 변경이 없음
	- Modified
		- 변경된 파일
	- Staged
		- 스테이지에 올라간 파일


**브랜칭**
- 원래 코드에서 브랜치하고 작업을 다른 사람과 분리할 수 있음
- 추후 버전 병합 시 도움이 됨
- 각 작업 (버그 수정, 새로운 기능 등) 에 대해 새 브랜치를 만드는 것이 일반적
- branching does not create a new repository, it just creates a new pointer that points to the same "head"
	- if one makes any new commit, the pointer then points to that new commit, whereas main stays the same as where we left off

**Switching Branches**
- matches the working tree to the version stored in the branch you wish to switch to
- what happens?
	- update working directory
	- branch pointer to lastest commit of the branch you switched to
	- codebase state (files, directoreis, contents)
	- uncommited changes : if there are uncommited changes that conflict with the branch you are switching to, it will prevent the switch until you commit, stash or discard

**Each branch in Git is essentially a pointer to a specific commit in your repository history**

![[Pasted image 20250702111411.png]]


**Stashing Branches**
- temporarily shelving changes that you made to your working directory **without committing those changes to your branch**
- if you have uncommitted changes in your working tree, and switch to a new branch, those uncommitted changes will also carry to the new branch
- changes that you commit will be committed to the new branch
- however, if there is a conflict between uncommitted changes, you are not allowed to switch

```
git stash

or

git stash save "working on feature X"
```
- saves your modified files and revert your working directory to a clean state

```
# list existing stashes
git stash list

#apply stash later (latest stash, but does not remove it)
git stash apply

#apply specific one
git stash apply stash@{1}

#apply and remove it
git stash pop
```

**Pulling Remote Branches**

**Case 1**
Remote
A -> B -> X -> Y (origin/main)

Local
A -> B (main)

Merge
A -> B -> X -> Y (main, oring/main)

**Case 2**
Remote
A -> B -> X -> Y (origin/main)

Local
A -> B -> C -> D

- if there is a conflict, you will have to resolve it and commit the merge manually

**Case 3**
Remote
A -> B -> X -> Y (origin/main)

Local
A -> B -> C -> D

Merge
A -> B -> X -> Y (origin/main) -> E (main)
       -> C -> D 

- merge commit is automatically created in the local repository when executing a pull
- if there is no conflict, the commit merge automatically

**Fetch**
- download changes from the remote that do not yet exist on your local branch

Remote
A -> B -> X -> Y (origin/main)

Local
A -> B -> C -> D (main)

Fetched
A -> B -> X -> Y (origin/main)
       -> C -> D (main)

can apply the fetched "changes" to your local repository via merging FETCH_HEAD or executing a pull

```
git switch main # branch 선택
git fetch origin # fetch update
git merge origin/main # 현재 branch 에 origin/main merge

or

git merge FETCH_HEAD # merge most recently fetched commit
```

Merged
A -> B -> X -> Y (origin/main) -> E (main)
       -> C -> D

**Pushing Branches to remote**
- when you push your local branch to remote, it will do a fast-forward merge to the destination repository
- else, it will decline your push from overwriting

----
**Workflow Type**

1. Centralized
	- Single central repository serving as "Source of truth"
	- clone repo -> make changes on local branch -> push changes directly to the central repository

2. Feature Branch
	- Each feature is developed on its own branch
	- new branch -> work on it -> merge it back to main branch

3. Gitflow
	- branching model with defined branches for features, releases and hotfix

4. Forking Workflow
	- fork central repository to their personal repositories
	- changes are proposed to central repository through "pull" requests






















