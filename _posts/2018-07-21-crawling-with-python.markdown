---
layout: post
title:  "크롤링 with python"
date:   2018-07-21 04:00:00 +0530
categories: ["home","develog"]
author: "minkyung"
---

# **[python] 파이썬으로 크롤링하기[1]**



### 크롤링(Crawling)이란 무엇인가?

웹 페이지에 방문해서 모든 자료들을 긁어온 후, 데이터를 추출하는 행위를 크롤링이라고 한다. 보통 크롤링이라고 많이 쓰지만, Scraping 이라는 단어도 많이 쓰인다.

일반적으로 python을 이용한 크롤링이 많이 쓰이며, 본인은 그 중 beautifulsoup라는 library를 사용할 것이다.
jupyter notebook을 이용했다.





### BeautifulSoup을 이용한 간단한 크롤링

#### 크롤링 하기 전 알아보자!

#### **1. BeautifulSoup 이란?**

beautifulsoup은 html 및 xml 파일에서 데이터를 가져오는 python 라이브러리이다. beautifulsoup을 이용하면 html문서를 Document Objec Model(DOM) 구조로 쉽게 해석할 수 있다. [DOM 구조는 객체지향 모델로써 구조화된 문서를 표현하는 방식이다. 잘 구조화된 문서는 DOM을 이용해 "트리구조"의 형태로 나타낼 수 있다.]

#### **2. 크롤링 방법**

1. 원하는 웹 페이지에 요청(request)해 페이지의 html을 받는다.
2. 받은 html을 파싱(parsing)한다.
3. 필요한 정보를 추출한다.

** 여기에서 **파싱(parsing)**이란, <u>어떤 Data를 원하는 Form으로 만들어 내는 것</u>을 의미한다. 특정 문서를 읽어와 다른 프로그램이나 서브루틴이 사용할 수 있는 내부의 표현방식으로 변환시켜 주는 것이다. 



#### **3. 저작권은요? (feat. robots.txt)**

크롤링을 통해 이 세상의 모든 웹 페이지의 내용을 가져올 수 있는 것은 아니다. 개인 정보가 들어있는 사이트들이 있을 수 있고, 무분별한 크롤링을 통해 피해를 보는 사람 등이 생길수도 있기 때문이다. 
 큰 검색 엔진 사이트들은 검색 속도를 높이기 위해 robot이라는 웹 크롤러를 만들어 자동으로 웹 페이지를 크롤링한다. 1994년 6월에 이런 robot들의 접근을 제어하기 위한 규약을 만들었다. 그것이 robots.txt이다. (하지만 권고안이라 꼭 지킬 의무는 없다.) robots.txt는 웹 사이트의 최상위 경로 (ex) naver.com/robots.txt)에 있다. 



robots.txt에는 아래와 같은 내용이 들어있다.

```
User-agent: 제어할 로봇의 User-Agent
Allow: /allow/here/
Disallow : /disallow/here/
```

Example)

```
User-agent: *  # 모든 로봇(robot)들에 적용합니다
Disallow: /  # 모든 페이지들의 색인(indexing)을 금지합니다
```



크롤링하기 전에, 크롤링 하고싶은 웹 사이트의 최상위 링크에 들어가 robots.txt를 확인해보고 크롤링하는 것이 좋다.
(만일 무시하고 크롤링해 해당 웹사이트에 피해를 입혔을 경우, 크롤링당한 웹사이트에서 당신을 고소할 수도 있다 ㅠㅠ 뭐 그럴 일은 거의 없겠지만) 마지막으로 사진, 논문, 책, 신문기사 같은 저작권이 있는 것들은 크롤링 할 때 더더욱 조심해야 한다. (저작권 문제가 있기 때문에)

[robots.txt](https://support.google.com/webmasters/answer/6062608?hl=ko)에 관한 내용을 더 보려면 클릭!

---

---

---

### 본격적인 크롤링



#### **Requests, BeautifulSoup 설치**

python에는 `requests` 라는 http request 라이브러리가 내장되어 있다. 아래의 코드로 설치가 가능하다.

```terminal
pip install requests
```

`BeautifulSoup` 라이브러리는 html을 의미있는 (python이 이해하는)객체구조로 만들어준다. 즉, BeautifulSoup은 html을 python이 이해하는 구조로 **파싱(parsing)** 해준다고 보면 된다. 이를 통해 우리는 '의미있는' 정보를 추출해 낼 수 있다.

두 가지의 방법을 이용해 `BeautifulSoup` 를 설치할 수 있다.

1. 직접 설치

   ```terminal
   pip install beautifulsoup
   ```

2. wrapper library를 통해 설치 (더 쉽고 안전함)

   ```terminal
   pip install bs4
   ```

**[wrapper library](https://ko.wikipedia.org/wiki/%EB%9E%98%ED%8D%BC_%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC)란?



#### **시작하기**

> 본인은 [노랑풍선 사이트의 미주/남미 테마추천상품](https://www.ybtour.co.kr/product/locMain.do?menu=fit&did=345) 의 title들을 가져올 것이다. (모든 페이지가 크롤링 가능하기 때문에)

**1. 위에서 설치한 library들을 import해준다.** 

    ```python
    import requests
    import bs4 from BeautifulSoup as bs # BeautifulSoup을 import해주고, 이후로는 bs라고 쓸 것이다.
    ```

**2. 위 페이지의 html을 가져온다.**

    ```python
    url = "https://www.ybtour.co.kr/product/locMain.do?menu=fit&did=345"
    html_response_code = reqests.get(url) # .get(url)을 하게되면 응답 코드가 나오게 된다.
    html = html_response_code.text # 그러므로 .text를 통해 text로 변환시키고 출력해야 한다.
    html # html 소스가 잘 불러와졌나 확인
    ```

requests.get을 통해 http get request 응답 코드를 불러오고,
requests.get.text를 통해 HTML 소스를 불러올 수 있다. 

**3. `BeautifulSoup` 으로 html소스를 python객체로 변환한다.**

   ```python
   soup = BeautifulSoup(html, 'html.parser') 
   	# html을 파싱하고 그 결과를 BeautifulSoup 객체로 반환한다.
       # 이 코드는 아래와 같이 쓸 수 있다. 
       # BeautifulSoup(html소스코드, 어떤 parser를 이용할 지)
   ```

   이제 soup에서 원하는 정보를 찾아낼 수 있게 되었다.

**4. `BeautifulSoup` 에서 제공하는 `select` 를 이용해 원하는 정보를 찾아낸다.**

   `select`는 CSS Selector를 이용해 조건과 일치하는 모든 객체들을 `list` 형태로 반환해준다.

   웹페이지 소스보기(Chrome 검사도구)를 통해 원하는 내용이 어떤 tag들로 구성되어있는 지 알 수 있다.

   ![chrome-source](/assets/chrome-source.png)

   내가 원하는 소스는 `h3 class="tit" ` 로 구성되어있다는 것을 알 수 있다. 

   아래와 같은 코드를 통해 같은 태그를 가진 내용들을 모두 가져올 수 있다. (두 가지의 방법이 있다.)

   1. class 명을 통해 가져오기

       ```python
       titles1 = soup.select('h3[class="tit"]')
       titles1 # 확인하기
       ```

   2. `copy selector` 를 통해 가져오기 (현재 요소의 위치를 정확하게 가져올 수 있다.)

      본인은 아래와 같이 나왔다. 

      >  div.pdt_list.list_thema > ul:nth-child(1) > li > a > div.box > h3

      ```python
      titles2 = soup.select('div.box > h3')
      titles2 # 확인하기
      ```

      그러면 내가 원하는 내용들이 나오는 것을 볼 수있다.

      ```python
      [<h3 class="tit">
       								[뉴욕에서 만나자] 미동부&amp;캐나다 6...</h3>, <h3 class="tit">
       								[뉴욕에서 만나자] 오후에 합류하는 ...</h3>, <h3 class="tit">
       								[내마음대로 하와이] 홀리데이 인 익...</h3>, <h3 class="tit">
       								[6일간 살아보기] 로스앤젤레스 자유...</h3>, <h3 class="tit">
       								[6/7일간 살아보기] 샌프란시스코 ...</h3>, <h3 class="tit">
       								[뉴욕 맛보기] 뉴욕 1박2일PKG ...</h3>, <h3 class="tit">
       								[내마음대로 하와이] 하얏트 센트릭 ...</h3>, <h3 class="tit">
       								[하와이] 빅아일랜드+오아후 &lt;로얄코...</h3>, <h3 class="tit">
       								[하와이] 마우이+오아후 &lt;마우이 비...</h3>, <h3 class="tit">
       								[6일간 살아보기] 초이스 뉴욕 자유...</h3>]
      ```

      이렇게 뽑아낸 titles2 는 soup 객체들의 list형이다. 그러므로 태그의 속성들도 이용할 수 있다. 예를 들어, h3의 class명을 뽑아낸다던지 (쓸데없긴 하지만 하핫)~~! 5번에서 뽑아내 보겠다.

**5. 필요없는 tag들을 지워버리고, 우리가 원하는 title 값만 뽑아내보기 (feat. for함수)**

   ```python
   for title in titles2:
       print(title.text) # titles2안의 title의 text들만 뽑아온다.
       print(title.get('class')) # titles2 안의 title의 각 class명을 가져온다. 
   ```

   결과)

   ```python
   
   								[뉴욕에서 만나자] 미동부&캐나다 6... # 잘 뽑혔다.
   ['tit'] # title.get('class')로 나온 값. h3의 클래스 명이 'tit'이라는 것을 알 수 있다.
   
   								[뉴욕에서 만나자] 오후에 합류하는 ...
   ['tit']
   
   								[내마음대로 하와이] 홀리데이 인 익...
   ['tit']
   
   								[6일간 살아보기] 로스앤젤레스 자유...
   ['tit']
   
   								[6/7일간 살아보기] 샌프란시스코 ...
   ['tit']
   
   								[뉴욕 맛보기] 뉴욕 1박2일PKG ...
   ['tit']
   
   								[내마음대로 하와이] 하얏트 센트릭 ...
   ['tit']
   
   								[하와이] 빅아일랜드+오아후 <로얄코...
   ['tit']
   
   								[하와이] 마우이+오아후 <마우이 비...
   ['tit']
   
   								[6일간 살아보기] 초이스 뉴욕 자유...
   ['tit']
   ```

   우리가 원하는 문자열을 뽑아왔지만, 앞뒤로 공백이 너무 많다는 것을 알 수 있다.

   그럼 공백을 지워보자. 그 전에, <u>빈 array를 하나 만들어 title들을 넣어준다.</u> 

**6. 뽑아낸 title들을 array로 만들기**

   ```python
   data = [] # 빈 list 생성
   for title in titles2:
       data.append(title.text) #.append함수를 통해 순차적으로 뽑은 title들을 넣어줌
   data # 확인 
   ```

    ['\r\n\t\t\t\t\t\t\t\t[뉴욕에서 만나자] 미동부&캐나다 6...',
        '\r\n\t\t\t\t\t\t\t\t[뉴욕에서 만나자] 오후에 합류하는 ...',
        '\r\n\t\t\t\t\t\t\t\t[내마음대로 하와이] 홀리데이 인 익...',
        '\r\n\t\t\t\t\t\t\t\t[6일간 살아보기] 로스앤젤레스 자유...',
        '\r\n\t\t\t\t\t\t\t\t[6/7일간 살아보기] 샌프란시스코 ...',
        '\r\n\t\t\t\t\t\t\t\t[뉴욕 맛보기] 뉴욕 1박2일PKG ...',
        '\r\n\t\t\t\t\t\t\t\t[내마음대로 하와이] 하얏트 센트릭 ...',
        '\r\n\t\t\t\t\t\t\t\t[하와이] 빅아일랜드+오아후 <로얄코...',
        '\r\n\t\t\t\t\t\t\t\t[하와이] 마우이+오아후 <마우이 비...',
        '\r\n\t\t\t\t\t\t\t\t[6일간 살아보기] 초이스 뉴욕 자유...']


   title 들만 뽑힌 것을 알 수 있다. 하지만 우리는 앞에 있는 더러운 저 \r, \n, \t들을 지워 줄 예정이다. 
   strip함수와 replace함수를 사용할 수 있는데, 조금 더 편한 strip()함수를 사용하겠다.

   ```python
   data.strip() # 에러코드
   ```

   위와 같이 strip() 함수를 바로 쓰게되면, data는 list 형이기 때문에 error가 나게 된다. 

   그러므로 data 안에 들어있는 list들 하나 하나를 돌며 공백을 없애주기 위해 아래와 같이 코드를 작성해야 한다.

   ``` python
   data1 = [item.strip() for item in data] # data 안의 item들을 하나 하나 돌며 strip()해 준다.
   data1
   ```

   이렇게 하면 바로 우리가 원하는 데이터들을 리스트형으로 뽑아낼 수 있게 된다.

   결과)

   ```
   ['[뉴욕에서 만나자] 미동부&캐나다 6...',
    '[뉴욕에서 만나자] 오후에 합류하는 ...',
    '[내마음대로 하와이] 홀리데이 인 익...',
    '[6일간 살아보기] 로스앤젤레스 자유...',
    '[6/7일간 살아보기] 샌프란시스코 ...',
    '[뉴욕 맛보기] 뉴욕 1박2일PKG ...',
    '[내마음대로 하와이] 하얏트 센트릭 ...',
    '[하와이] 빅아일랜드+오아후 <로얄코...',
    '[하와이] 마우이+오아후 <마우이 비...',
    '[6일간 살아보기] 초이스 뉴욕 자유...']
   ```

**7. 추가) Array, Tuple, Dictionary**

   ```python
   # Array: 배열을 만들 때! [] 사용 / (), {}는 쓰이지 않음
   array = []
   array1 = [1,2,3,4,5,6,7,8,9]
   array1[4] # 배열의 5번째 원소에 접근할 때 
   
   # Tuple: 튜플을 만들 때! () 사용 / []는 튜플의 원소에 접근할 때 / {}는 쓰이지 않음
   # 튜플의 값은 리스트자료형과는 다르게 내용을 변화시킬 수 없다. 
   tuple = ()
   tuple1 = (1,2,3,4,5,6,7,8,9)
   tuple1[4] #튜플의 5번째 원소에 접근할 때 
   
   # Dictionary: 딕셔너리 형식을 만들 때! key: value 형식으로 나타내어진다.
   # {} 사용 / []는 value 값에 접근할 때, key에 대응하는 value를 할당할 때에 쓰임 
   dictionary = {}
   dictionary1 = {"hi":1, "hello":2}
   dictionary1["hi"] # key인 "hi"를 사용해 그에 대응하는 value값 1에 접근할 수 있다.
   dictionary1["anyong"] = 3 # 새로운 key("anyong")와 value(3)를 생성해준다. 
   ```

   

BeautifulSoup을 이용한 간단한 웹 크롤링 끝!