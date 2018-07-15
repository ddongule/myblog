---
layout: post
title:  "jekyll을 이용해 Github 정적 blog 만들기(Mac)"
date:   2018-07-15 19:00:00 +0530
categories: ["home","develog"]
author: "minkyung"
---

# jekyll을 이용해 Github 정적 blog 만들기(Mac) 

## Github Pages란?

[Github Pages](https://pages.github.com/) 는 github의 프로젝트 페이지들을 만들 수 있게 github에서 제공하는 호스팅 서비스이다. `git push`하는 것만으로도 쉽게 웹사이트를 만들 수 있다. 서버 구축이 필요없고, 비용도 들지 않으며 네트워크 설정이 따로 필요 없으며 markdown과 html을 지원한다. 그리고 Github Pages는 Jekyll을 자체 지원 (내부적으로 Jekyll에 의해 작동됨)하고 있기 때문에 Jekyll을 통해 블로그를 만들기 적합하다.



## Jekyll(지킬)이란?

`jekyll`은 markdown형식의 txt파일을 html파일로 변환해주는 하나의 변환 도구이다. 사용자가 글을 올릴 때 markdown 형식으로 작성하면 알아서 html로 변환해주는 도구라고 보면 된다. 즉, 사용자가 블로그에 글을 쓸 때, html을 신경쓰지 않고 markdown 으로 쉽게 글을 올릴 수 있다는 장점이 있다. Jekyll이 하나의 Ruby gem이기 때문에, Jekyll을 사용하려면 Ruby가 필요하다. 설치 방법은 후에 기술하겠다.



## 개설 환경 구축하기 (Github, Ruby, Jekyll, Xcode)



### Github Repository 만들기

1. Github pages에 **username.github.io**라는 Repository name으로 project 생성하기

   반드시 username에 자신의 아이디를 넣어 username.github.io라는 이름으로 Repository를 만들어야 생성된다.



### Jekyll 설치하기

Jekyll을 설치하려면, 먼저 Ruby를 설치해야 한다. Mac에는 Ruby(1.9.3ver)가 내장되어있기 때문에 Ruby를 따로 설치할 필요는 없지만, Ruby Version을 업데이트 해주어야 한다. Github에서 사용하는 Ruby 버전은 2.1.1이다. (Version 2 이상이면 된다.) 

1. <u>Ruby 설치</u>

  `terminal`에 `ruby -v`를 입력하면 Ruby 버전이 나온다. version이 2 이상이라면 그대로 사용해도 좋다. 만약 아니라면 아래의 코드를 실행한다.

  1. Ruby Version Manager 설치하기

     Ruby에는 다양한 버전들이 존재하는데, Ruby Version Manager(이하 RVM)는 Ruby의 다양한 버전, gem등을 관리해준다. 아래와 같은 코드로 설치할 수 있다.

     ``` 
     curl -sSL https://get.rvm.io | bash -s stable --ruby  
     ```

  2. Ruby 설치

     *Ruby Version 2.1.1 설치*

     ``` 
     rvm install 2.1.1
     ```

     *Ruby 2.1.1을 기본으로 설정*

      ``` 
     rvm --default use 2.1.1
      ```

2. <u>Xcode 설치</u> 

   앱스토어에 들어가서 Xcode를 설치한다. Mac에서 Xcode를 설치하지 않으면 Jekyll을 사용할 수 없다. :(

3. <u>Jekyll 설치하고 새 파일 생성하기</u>

   터미널에 아래 코드를 입력하면 jekyll이 다운로드 된다.

   ```
   gem install jekyll
   ```

   jekyll을 이용해 username.github.io라는 페이지 생성

   ```
   jekyll new ddongule.github.io
   ```

   만든 폴더로 이동

   ``` 
   cd ddongule.github.io
   ```

4. 서버 구동해보기

   ``` 
   jekyll serve --watch
   ```

   위의 코드를 실행하고, 주소창에 **localhost:4000**을 통해 페이지가 구동되는지 확인한다. 

5. Github에 연결하고 올리기

   ``` 
   git init
   git remote add origin <url>
   git add .
   git commit -m "first commit"
   git push -u origin master
   ```

이제 <username>.github.io로 된 나만의 블로그가 완성되었다!



---



## 블로그 Customizing하기

다양한 방법으로 나만의 블로그를 꾸밀 수 있다. CSS에 능숙한 사람이라면 CSS파일을 직접 수정해 지킬 블로그를 수정해 보는 것도 좋은 방법이다. 하지만 이 방법이 익숙하지 않은 사람이라면 능력자분들이 미리 만들어 놓은 테마를 적용할 수도 있다. 

[Jekyll Themes](http://jekyllthemes.org/ )에 들어가 테마를 구경하고, 직접 다운로드 하거나 github repository를 fork해 오는 등의 방법으로 내 블로그에 테마를 넣을 수 있다. 그런데 위 사이트에 들어가서 원하는 테마를 다운받아 넣는다고 해도 <u>안되는 테마들이 많으니</u> 조심할 것 .. (한숨) 그리고 원하는 테마를 넣고 수정해서 git push를 해도 내 github page에 올라가는 속도가 느리니 그것도 감안해야 한다. 

여기서 본인은 Jekyll theme을 local에 다운로드 한 후, git push 하는 방식으로 테마를 넣었기 때문에 그렇게 진행하겠다.

1. 위의 사이트에 들어가 원하는 테마를 다운로드 한 후, 압축을 풀고 local의 내 username.github.io 디렉토리에 모두 붙여넣는다. Jekyll Theme을 넣으려면 ruby의 bundler gem이 필요하므로 `terminal` 에 밑의 코드를 실행시킨다.

    ```
    gem install bundler
    ```

2. 아래의 코드 중 하나를 실행시켜 다시  `localhost:4000`서버를 구동시켜보면, 테마 적용이 되었다는 것을 알 수 있다. (ruby gem을 설치한 후에는 서버를 껐다 다시 켜야한다.)

   ``` 
   bundle exec jekyll serve
   jekyll serve
   ```



## 블로그 Posting 하기

Posting을 하려면 두 가지만 알면 된다. 
Jekyll은 `_post` 폴더 안의 파일들만 블로그 포스트로 인식하기에 반드시 `_post` 폴더 안에 markdown 파일을 넣어야 한다. 그리고 포스트의 파일 명은 항상 YYYY-MM-DD-post-name.md 형식이어야 한다. 

markdown 포스트에는 파일의 제일 앞부분에 밑과 같은 형식으로 Front Matter를 적어야 한다. 

```
---
layout: post
title:  "Welcome to My Post"
date:   2016-03-30 19:45:31 +0530
categories: jekyll
---

글 내용
```

여기에서 date나 categories는 필수가 아니지만, **layout과 title은 꼭 들어가야 한다.**
글을 넣고, git add, commit, push하면 이제 글이 블로그에서 보이게 된다.

















