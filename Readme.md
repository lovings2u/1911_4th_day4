# 20191111 Day04

- 지난주 Remind

  - Chrome 개발자 도구 Network 탭 보는법
  - Network탭과 Elements 탭을 분석해서 여러 사이트 크롤링하는 방법
    - 파라미터 만들어서 서버로부터 원하는 자료 가져오기
    - 크롤링 할 때 일반 클라이언트인 척 해보기
    - CSRF Token -> POST
    - 요청보내서 받은 데이터를 다시 파싱해서 새로운 URL로 요청보내기

- 지난주 과제

  1. 사람인 크롤링 했던 거 세부정보까지 크롤링하기

  2. 다음웹툰도 웹툰 세부정보 크롤링하기

     - 각 요일별 웹툰 데이터 -> 월, 화, 수... -> 각 요일 웹툰 리스트 -> 해당 웹툰의 세부정보

     - ```python
       #app.py
       from flask import Flask, request, render_template
       import requests
       import json
       app = Flask(__name__)
       
       # 1. 전체 요일 적혀있는 페이지
       @app.route('/')
       def index():
           days = {
               '월요일': 'mon',
               '화요일': 'tue',
               '수요일': 'wed',
               '목요일': 'thu',
               '금요일': 'fri',
               '토요일': 'sat',
               '일요일': 'sun'
           }
           return render_template('index.html', days=days)
       # 2. 각 요일에 대한 데이터 요청하는 곳
       @app.route('/<day>')
       def day_webtoon_list(day):
           url = f'http://webtoon.daum.net/data/pc/webtoon/list_serialized/{day}'
           response = requests.get(url)
           data = response.json()
           webtoons = {}
           for toon in data["data"]:
               webtoon_title = toon["title"]
               webtoon_nickname = toon["nickname"]
               webtoon_introduction = toon["introduction"]
               webtoon_artists = []
               for artist in toon["cartoon"]["artists"]:
                   webtoon_artists.append(artist["name"])
       
               webtoons[webtoon_title] = [webtoon_nickname, webtoon_introduction, ", ".join(webtoon_artists)]
           return render_template('day_webtoon_list.html', webtoon_dic=webtoons)
       # 3. 각 웹툰에 대한 세부 데이터 요청하는 곳
       
       @app.route('/webtoon/<nickname>')
       def webtoon_info(nickname):
           url = f'http://webtoon.daum.net/data/pc/webtoon/view/{nickname}'
           return requests.get(url).json()
       ```

     - ```html
       <!-- index.html -->
       <!DOCTYPE html>
       <html lang="en">
       <head>
           <meta charset="UTF-8">
           <meta name="viewport" content="width=device-width, initial-scale=1.0">
           <meta http-equiv="X-UA-Compatible" content="ie=edge">
           <title>Document</title>
       </head>
       <body>
           {% for key, value in days.items(): %}
               <a href="/{{ value }}">{{ key }}</a><br>
           {% endfor %}
       </body>
       </html>
       ```

     - ```html
       <!-- day_webtoon_list.html -->
       <!DOCTYPE html>
       <html lang="en">
       <head>
           <meta charset="UTF-8">
           <meta name="viewport" content="width=device-width, initial-scale=1.0">
           <meta http-equiv="X-UA-Compatible" content="ie=edge">
           <title>Document</title>
       </head>
       <body>
           {% for key, value in webtoon_dic.items(): %}
               <a href="/webtoon/{{value[0]}}">{{ key }}</a> / {{ value[1] }} / {{ value[2] }}<br/>
           {% endfor %}
       </body>
       </html>
       ```

- Form 데이터 처리하기

  - path param

- Django 입문

  - 이번주 아이디어톤 전까지는 장고의 파일구조 살펴보기
    - 프로젝트 > 어플리케이션
  - ORM 빼고 계속 새로운 프로젝트로 재미난거 만들기
    - 신이 나를 만들때
    - 로또 번호 생성기
    - ...
  - 이번주 아이디어톤 이후에 CRUD -> ORM
  - 다음주 내내 CRUD 가지고 재밋는거 ~~ 예에

  