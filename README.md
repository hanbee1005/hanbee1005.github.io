# hanbee1005.github.io
 https://hanbee1005.github.io/

## 세팅 방법
Mac을 기준으로 작성하였습니다.

### 1. 사전 준비
- git 설치 (https://docs.github.com/en/get-started/quickstart/set-up-git)
- ruby 설치 (https://www.ruby-lang.org/en/documentation/installation/)
	+ Homebrew를 통해 설치
	```shell
	$ brew install ruby
	```
	+ Hemberew 설명 (https://brew.sh/)

### 2. create repository
- <user>.github.io 또는 <organization>.github.io 라는 이름으로 repository 생성

### 3. create site
- clone repository & change directory
```shell
$ git clone https://github.com/USER/REPOSITORY.git
$ cd REPOSITORY-NAME
# Changes the working directory
```
- create a new Jekyll site
```shell
$ jekyll new --skip-bundle .
# Creates a Jekyll site in the current directory
# 권한이 없어서 실행되지 않는 경우 sudo jekyll new --skip-bundle . 로 실행
```
- Gemfile 수정
	+ gem "jekyll" 라인 주석 처리하기
	```
	# gem "jekyll"
	```
	+ gem "github-pages" 라인 수정 (or 추가)
	```
	gem "github-pages", "~> GITHUB-PAGES-VERSION", group: :jekyll_plugins
	```
- Gemfile 저장 후 bundle install
```shell
$ bundle install
# 권한이 없는 경우 sudo bundle install
```
- 원격 저장소에 push
```shell
$ git add .
$ git commit -m "Initial GitHub pages site with Jekyll"

$ git remote add origin https://github.com/USER/REPOSITORY.git
# git remote -v 으로 원격 저장소가 지정되어 있는지 확인

$ git push -u origin BRANCH
```
- remote repository의 Settings > Pages에서 URL 확인

## 페이지 추가

## 테마 적용하기

## 참고
- https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll
- https://poiemaweb.com/jekyll-basics

