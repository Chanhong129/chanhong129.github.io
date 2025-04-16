Github blog 를 만들면서 겪은 시행착오를 정리하고, 처음부터 다시 해보면서 정리 글을 작성하였습니다.
 - Window 11 에서 진행

# 테마 쇼핑하기
Github Blog 를 제작하기 위해서 첫 번째로 해야 할일은 블로그 테마를 고르는 일이었다. 

http://jekyllthemes.org/ 에 들어가면 여러가지 블로그 테마를 구경할 수 있다. 

꽤 많은 스타일이 있어서 가지각색의 사람들의 취향을 웬만하면 걸리지 않을까..?

그 중에서 나는 Chirpy 테마가 가장 마음에 들어서 선택했다.

# Fork 하기
**\* Fork 를 하기 위해선 github 에 가입을 해야한다.**

![Chirpy](https://i.ibb.co/8HdqddK/image.png)
Hompage 를 클릭하면 jekyll-hteme-chirpy github page 로 들어가지는데 여기서 fork 를 진행한다.

fork 는 우리가 아는 식사를 할 때 사용하는 그 포크를 말하는 것인데
포크질을 해서 케이크를 갈라 내 접시로 가져오듯이 코드를 포크질 하여 나의 공간에 가져오는 행위를 말한다.

어쨋든 Fork 를 누르고 Repository name 을 username/username.github.io 로 변경하여 create fork 를 한다.
필자의 경우 chanhong129/chanhong129.github.io 이다.

혹시 이름을 변경하지 않고 Fork 했더라도 나의 Repository 에서 포크한 프로젝트를 선택하고 Setting - General 에 들어가 이름을 변경할 수 있다.

# Clone 하기
**\* git 이 설치 되어있다고 생각하고 진행**
https://git-scm.com/ 에서 설치 할 수 있습니다.

Github 의 chanhong129.github.io Repository 에서 code 를 눌러 url 을 copy 한다.
윈도우에서 원하는 폴더 위치에서 우클릭 후 git bash here 을 하여
git clone 을 치고 붙여넣기 하여 코드를 복사 한다.
```git
git clone (복붙)
```

# Ruby 설치
https://rubyinstaller.org/downloads/ 에서
-   [Ruby+Devkit 3.3.8-1 (x64)](https://github.com/oneclick/rubyinstaller2/releases/download/RubyInstaller-3.3.8-1/rubyinstaller-devkit-3.3.8-1-x64.exe)
저는 이 버전을 설치하였어요.
**3.1.X** 이상의 버전을 설치하면 되는 것 같습니다.

```shell
> ruby -v
ruby 3.3.8 (2025-04-09 revision b200bad6cd) [x64-mingw-ucrt]
```
가 나오면 정상 설치된 것 입니다.

다음으로는 jekyll 를 설치합니다.
```shell
> gem install jekyll
> gen install bundler
```
```
> jekyll -v
> bundler -v
```
역시 버전 확인으로 정상 설치를 확인 합니다.

# chirpy 초기화
Git repository 를 clone 했던 폴더 위치에서 git bash 를 연다.
```
tools/init.sh
```
를 입력하여 아래 파일들을 삭제하고 초기화 하여준다.
- .travis.yml
- _posts 폴더 하위의 파일들
- docs 폴더

```
> bundle
```
을 입력하여 페이지를 열기 위한 필요한 모듈들을 설치하여 줍니다.
로컬에서도 실행이 가능해집니다.
```
jekyll serve
```
를 입력한 후 http://127.0.0.1:4000 에 접속하면 블로그를 볼 수 있습니다.
이후 Ctrl +  C 를 눌러서 종료

# URL 설정하기
clone 한 폴더에서 _config.yml 을 찾아 편집기로 엽니다.
그리고 url:"" 을 찾아
url:"username.github.io" 로 변경합니다.
필자의 경우 url:"chanhong129.github.io"
저장 후 닫아줍니다.

# git commit, push 하기
git bash 에서 commit, push 를 진행합니다.

수정한 모든 파일을 추가 하는 작업
```
git add .
```

커밋을 진행하는 작업
```
git commit -m 'first commit'
```

푸시를 진행하는 작업
```
git push -u origin +master
```

이후 git repository 에 Setting - Pages 에서 Visist site 로 방문하면 내가 만든 Github Blog 에 접속 가능!

**\* Git commit error message 발생 시**
```
$ git commit -m 'first post'
⧗   input: first post
✖   subject may not be empty [subject-empty]
✖   type may not be empty [type-empty]

✖   found 2 problems, 0 warnings
ⓘ   Get help: https://github.com/conventional-changelog/commitlint/#what-is-commitlint

husky - commit-msg script failed (code 1)
```

> rm .husky/commit-msg

를 이용해서 커밋 규칙을 삭제한다.