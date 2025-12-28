---
title: "Building a Static Site With Hugo (1): Theme Setup and Site Generation"
description: ""
date: "2025-12-27T22:47:12+09:00"
thumbnail: ""

categories:
  - "DevLog"
tags:
  - "Hugo"
  - "Static Site"
  - "Theme Setup"
  - "Blog Setup"

draft: false
---

<!--more-->
***



# Hugo로 정적 사이트 만들기 (1)

Hugo를 이용해 static site를 만들고, 그것을 GitHub 이용해 인터넷에 host 및 deploy 한 과정에 대한 글.
- (1) Hugo로 테마 적용하여 static site 만들기
- (2) GitHub Pages로 host 및 deploy 하기

\
처음에는 챗지피티에게 물어보며 만들려 했는데, 챗지피티가 이 부분을 잘 모르는 건지 내 프롬프팅 문제인 건지 오류가 고쳐지질 않았다. 이후에는 다시 처음부터 공식 문서를 읽으며 진행했다.

Hugo는 static site generator로, 말 그대로 정적인 사이트를 만들어 준다.
\
Hugo에서 정한 규칙에 따라서 커맨드를 입력해 site 폴더를 생성하고, 그 안에 들어가는 파일들도 규칙에 맞춰 입력을 잘 해주면 Hugo가 그 site 폴더를 읽고 해석해서 사이트로 만들어주는 방식인 듯하다.
\
다른 사용자들이 만든 Hugo 테마를 가져와 활용하면 이미 꾸며진 레이아웃을 이용해 나만의 사이트를 만들 수 있다.

나는 Mainroad 라는 테마를 적용했다. 공식 문서에 예시로 나오는 테마가 아니라서, 커맨드를 쓰고 사이트를 만드는 데에서 고민한 부분이 있었다.
\
또한 Hugo, yaml, toml 등 익숙지 않은 부분이 많았어서 블로그를 만든 과정을 쭉 정리해 두면 좋을 듯해 글을 적어 본다.

 

---

Hugo 공식 사이트의 문서와, 내가 적용하고자 하는 테마의 공식 사이트 문서를 적절히 보면서 진행해야 한다.

## 초기 설정

Git 설치

- 이미 설치되어 있어서 따로 설치 과정을 거치지 않았다. 생략.

Hugo 설치

- https://gohugo.io/installation/ 을 보고 진행.
- Git Bash를 켜고, winget install Hugo.Hugo.Extended 입력하여 Hugo를 설치했다.
\
(아래에서 언급하는 Quick start 페이지에, 윈도우 사용자가 커맨드 입력 시 사용해야 하는 커맨드 어플리케이션이 적혀 있다. 이에 따라 Git Bash를 사용했다.)

 

## 테마 지정하여 Hugo로 새로운 site 생성

- https://gohugo.io/getting-started/quick-start/#add-content 와 https://mainroad-demo.netlify.app/docs/getting-started/ 를 보며 진행.
- Git Bash를 켜고, 아래의 커맨드를 차례로 입력.
- hugo new site <사이트 폴더 이름> : Hugo 규칙에 맞는 사이트 폴더가 생성된다. 여러 가지 폴더와 파일이 안에 들어 있다.
- cd <사이트 폴더 이름> : current directory를 설정.
- git init : 현재 작업 디렉토리에서 깃을 사용하기 위해 입력.
- git submodule add <github 링크> themes/<테마 이름> : 테마를 적용하는 코드.
\
테마가 다르면 그 테마의 공식 사이트를 보며 진행하는 것이 좋은 듯하다.
\
git submodule 코드는, 사용하려는 테마의 github 페이지에서 theme에 해당하는 부분을 가져와서 쓰는 방식으로 작동하는 것 같다. 아마도 git clone (복제) 비슷한 역할을 하는 듯.
    ```bash
    hugo new site mysite2
    cd mysite2
    git init
    git submodule add https://github.com/vimux/mainroad.git themes/mainroad
    ```
    
- VSCode로 mysite2 폴더를 열고, hugo.toml 파일을 열어서 아래의 한 줄을 추가.
\
(configuration 관련 내용은 아래에서 더 설명.)
\
theme이라는 변수의 값을 변경하는 작업인 듯하다. 테마가 다르면 “mainroad”라는 문자열 대신 그 테마에서 지정한 문자열을 써주면 될 듯.
    
    ```bash
    theme = "mainroad"
    ```
    
- 여기까지 하면 사이트 뼈대를 완성한 것.
\
다시 git bash로 돌아가서 (cd가 mysite2인 상태에서) 아래의 커맨드를 입력하면, Hugo가 일시적으로 static site를 만들어준다.
    
    ```bash
    hugo server
    ```
    
- 안내 문구로 localhost:1313이라는 링크가 나오는데, 이 링크를 Chrome 등의 웹브라우저로 열면 만들어진 사이트를 볼 수 있다.
\
테마까지 잘 반영되어 있는 것이 확인된다.
\
hugo server의 작동을 멈추고 다음 커맨드를 입력하기 위해서는 Ctrl+C를 누르면 된다.
\
(참고로 로컬에서만 돌아가는 사이트인 것 같고, 웹에 게시된 상태는 아니다.)

 

## Configuration과 Archetypes 설정하기

Quick Start에 따르자면 다음 단계는 내 사이트에 글을 작성하여 올려보는 단계인데…
\
넘어가기 전에 먼저, 사이트를 조금 더 손보고(configuration), 글을 작성할 새 파일을 생성할 때 자동으로 반영되게 하는 양식(archetype)을 만들어 보자.

**Configuration**

- hugo.toml이라는 파일은 site 폴더를 만들 때 자동으로 Hugo가 생성해주는 파일이다. 이 파일을 VSCode로 열고, 여러 변수들을(즉 configuration을) 추가/수정해서 사이트의 기본 세팅을 바꿀 수 있다.
\
(위에서 수정했던 theme이 configuration 중 하나였던 것.)
- configuration은 2종류인데, 어떤 테마에서든지 적용되는 Hugo 자체의 configuration이 있고, 각 테마마다의 configuration이 있다. 
\
전자는 https://gohugo.io/configuration/all/#all-configuration-settings 를, 후자는 각 테마의 공식 문서를 참고.
- 내가 사용한 테마인 Mainroad는, https://mainroad-demo.netlify.app/docs/faq/ 의 “Is there a list of all possible configuration options?**”** 부분을 보면 configuration으로 설정할 수 있는 변수들을 확인할 수 있다.
- 주로 [https://github.com/Vimux/Mainroad#configtoml-example](https://github.com/Vimux/Mainroad#configtoml-example) 를 보면서 내가 필요한 부분을 hugo.toml 파일에 추가했다.
- 위 링크에 deprecated라고 적혀 있지 않은데 hugo server 실행하면 오류가 나는 부분들이 있었다. hugo 업데이트가 이루어졌는데 테마에는 반영이 안 된 부분 같다. 실행시켜보면서 오류 나는 부분은 삭제했다. (paginate, Author 등)

**Archetypes**

- 내 사이트에 글을 작성하기 위해서 커맨드로 hugo new content …(생략) 를 입력하면 Hugo가 해당 디렉토리에 지정한 이름과 형식의 파일을 만들어준다. 그 파일에 제목, 날짜, 카테고리, 등등을(front matter를) 내가 직접 적어서 지정해주어야 한다.
\
이때 archetype을 설정하면, 그 세팅대로 파일이 만들어진다. 매번 front matter를 작성하는 수고를 덜 수 있다. front matter 아래의 글을 적는 부분 또한 고정된 양식이 자동으로 적히게 만들 수 있다.
파일을 만든 시간이 자동으로 적히도록 설정할 수도 있다는 점도 굉장히 편해 보였다.
- Hugo 공식 문서에서, theme 폴더 안의 archetype/default.md보다, 내 사이트 폴더 안의 archetype/default.md가 더 우선순위가 높다는 것을 읽었다. (Hugo가 어떤 default.md를 archetype으로써 사용해서 파일을 만들어 줄 것인지 결정하는 우선순위.)
이에 따라, 자동으로 만들어져 있던 내 사이트 폴더의 archetype/default.md를 수정했다.
- Front matter에 categories와 tags를 추가. 글을 쓰는 부분도 테마에 맞게 수정.
    
    ```html
    ---
    title: "{{ replace .File.ContentBaseName '-' ' ' | title }}"
    description: ""
    date: "{{ .Date }}"
    thumbnail: ""
    
    categories:
      - ""
    tags:
      - ""
    
    draft: true
    ---
    
    <!--more-->
    ***
    ```
    
- 블로그에 표시되는 날짜 및 시간 형식은 기본적으로 ‘January 02, 2006’였다. 이것을 바꾸고 싶었기 때문에, (Hugo 공식 문서를 참고하여) 처음에는 date 부분을 {{ .now.Format ‘06-01-02 15:04’ }} 로 고쳤었다. 하지만 이렇게 고친다면 블로그에 표시되는 시간이 아니라, 새로운 content 파일을 만들었을 때 파일에 자동으로 적히는 시간이 2006-01-02 15:04의 형식이 된다. 이 형식은 Hugo가 적절히 해석하지 못하기 때문에 오류가 난다.
\
블로그에 표시되는 날짜 및 시간 포맷을 고치고 싶다면, 이 부분이 아니라 configuration의 dateformat 변수를 고쳐야 한다.
\
(사실 configuration에 있는 줄 모르고 테마 설정을 바꾸어야 한다고 생각해서 themes/mainroad/layouts/partials/post_meta/date.html 파일을 열었다. 그런데 수정하려고 코드를 해석하다 보니 .Site.Params.dateformat 이라는 변수가 있다는 걸 발견하고 다시 configuration으로 돌아갈 수 있었고, 결국 놓치고 넘어갔던 것임을 알게 됐다…)

 

## 새로운 글 작성하기

- Git Bash를 켜고 (cd가 mysite2인 상태에서)
hugo new content content/<폴더 이름 등 상위 디렉토리>/<파일 이름>.md
를 입력.
\
상위 폴더가 존재하지 않는 상태면 Hugo가 해당 이름의 폴더를 content 폴더 안에 만들어주며, 그 안에 지정한 이름의 파일을 만들어준다. 이때 markdown 말고도 다른 형식의 파일도 만들 수 있다고 한다. HTML 등.
- 설정한 Archetype에 따라서 파일이 만들어진다.
이때 <파일 이름> 부분은 하이픈(-)을 넣어서 적어주면 편하다. Archetype의 {{ replace .File.ContentBaseName '-' ' ' | title }} 코드에 의해 하이픈 대신 띄어쓰기가 들어가게 바뀌기 때문.
\
나는 content/examples/example-1.md 를 입력했고, 이는 생성된 파일에서 title: Example 1 으로 바뀌었다.
- 위에서 ‘글’이라고 언급한 것을 공식적으로는 page라고 부른다.
\
각 page마다 글 맨 위에 yaml, toml 등의 형식으로 Front matter 라는 것을 적어줘야 한다.
- draft라는 것이 front matter 중 하나인데, draft: true 로 두면 hugo server -D 를 이용해 나만 일시적으로 확인 가능하며, 실제 사이트에서는 보이지 않게끔 숨길 수 있는 설정이다.
\
인터넷에서 확인할 수 있게 게시하기 위해서는 게시할 page들의 front matter들을 draft: false 로 고쳐준 후 진행해야 한다.

 

## GitHub Pages로 Host 및 Deploy하기

다음 글에서 계속.

 

## 기타 배운 점

- html, css, markdown은 이름만 들어보았지 문법에 친숙하지 않았었는데, 블로그를 만들면서 조금 더 익숙해질 수 있었다.
- yaml, toml은 완전히 처음 들어보았다. markdown 파일 안에서 둘 중 하나를 마음대로 선택해서 쓸 수 있는 듯하고, 둘의 문법의 비슷한 부분과 다른 부분을 조금 알게 되었다.
테마 공식 문서에서 yaml인지 toml인지 명시하지 않은 글이 있는데 그런 부분도 적당히 읽을 수 있게 되었다.
- 커맨드 사용과 VScode 사용에 더 친숙해졌다.
- 생성형 AI에게 묻는 것보다 공식 문서를 읽는 것이 더 정확할 수 있다는 걸 다시금 깨달았다.
- 내가 현재 적용한 테마가 아닌 다른 테마로도 블로그를 만들 수 있겠다는 자신이 생겼다.

 
