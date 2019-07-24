how to implement
================
* google dialogflow agent 생성

<img width="1680" alt="스크린샷 2019-07-24 오후 3 33 50" src="https://user-images.githubusercontent.com/48667219/61772749-fe04ce80-ae2d-11e9-8955-2c34f95f9c44.png">


* 화면 좌측의 intent 카테고리에 들어가 새로운 intent를 만들어 준다.

* 생성된 intent에 접근하여 'Training phrases' 에 새로운 명령문장을 만들어준다. 
~~~
weather for seoul
~~~
~~~
weather for New-york
~~~
~~~
what is the weather for Tokyo
~~~

* 만들어준 명령문에서 '도시' 이름을 추출해서 인식할 수 있도록 'action and parameter' 를 만들어준다. 
  1. 상단의 'training phrases' 에서 본인이 입력한 문장에서 '도시 이름'을 드래그한다
  2. 검색에 @sys.geo-city 를 클릭한다. 
  


* 원하는 디렉터리에 파일 생성. 예) weather-bot

* index.js 파일 생성. 

<img width="888" alt="스크린샷 2019-07-24 오후 4 20 54" src="https://user-images.githubusercontent.com/48667219/61773205-11fd0000-ae2f-11e9-85bd-839e4c5d7ce4.png">

* terminal 창에서 필요한 모듈들 모두 설치. 예) npm init

<img width="578" alt="스크린샷 2019-07-24 오후 4 18 38" src="https://user-images.githubusercontent.com/48667219/61773076-b894d100-ae2e-11e9-9a00-be8409cc9cca.png">

* 날씨 정보를 가져올 홈페이지  https://developer.worldweatheronline.com/api/ 로 접근하여 key 값을 받아온다

<img width="794" alt="스크린샷 2019-07-24 오후 4 24 40" src="https://user-images.githubusercontent.com/48667219/61773419-9d769100-ae2f-11e9-874d-aa75176b2657.png">

* 생성된 키 값을 index.js 파일의 20 번째 줄, functions에 넣어준다. 

<img width="1680" alt="스크린샷 2019-07-24 오후 4 26 08" src="https://user-images.githubusercontent.com/48667219/61773604-078f3600-ae30-11e9-9145-bad2bd4cf948.png">

* 코드 작성
  * 코드는 다음과 같다.
  ```
     use strict;

      const http = require('http');
      const functions = require('firebase-functions');

      const host = 'api.worldweatheronline.com';
      const wwoApiKey = 'e2aa6c8795b8493eb6971830191107';

      exports.dialogflowFirebaseFulfillment = functions.https.onRequest((req, res) => {
        // request로부터 도시와 날짜를 받아온다
        let city = req.body.queryResult.parameters['geo-city']; // city is a required param

        // Get the date for the weather forecast (if present)
        let date = '';
        if (req.body.queryResult.parameters['date']) {
          date = req.body.queryResult.parameters['date'];
          console.log('Date: ' + date);
        }

        // weather API를 호출
        callWeatherApi(city, date).then((output) => {
          res.json({ 'fulfillmentText': output }); // dialogflow에 weather api 값을 돌려준다
        }).catch(() => {
          res.json({ 'fulfillmentText': `I don't know the weather but I hope it's good!` });
        });
      });
      
      function callWeatherApi (city, date) {
        return new Promise((resolve, reject) => {
          // weather 에 대한 얻은 값을 토대로 HTTP path를 만들어준다. 
          let path = '/premium/v1/weather.ashx?format=json&num_of_days=1' +
            '&q=' + encodeURIComponent(city) + '&key=' + wwoApiKey + '&date=' + date;
          console.log('API Request: ' + host + path);

          // weather를 얻기 위한 HTTP requset
          http.get({host: host, path: path}, (res) => {
            let body = ''; // var to store the response chunks
            res.on('data', (d) => { body += d; }); // store each response chunk
            res.on('end', () => {
              // 모든 데이터가 들어온 후에 JSON으로 넘겨준다. 
              let response = JSON.parse(body);
              let forecast = response['data']['weather'][0];
              let location = response['data']['request'][0];
              let conditions = response['data']['current_condition'][0];
              let currentConditions = conditions['weatherDesc'][0]['value'];


              let output = `Current conditions in the ${location['type']} 
              ${location['query']} are ${currentConditions} with a projected high of
              ${forecast['maxtempC']}°C or ${forecast['maxtempF']}°F and a low of 
              ${forecast['mintempC']}°C or ${forecast['mintempF']}°F on 
              ${forecast['date']}.`;

              // 출력값을 약속된 형태로 돌려준다. 
              console.log(output);
              resolve(output);
            });
            res.on('error', (error) => {
              console.log(`Error calling the weather API: ${error}`)
              reject();
            });
          });
        });
      }
      
      
* terminal 상에서 

~~~
npm install -g firebase-tools
~~~

* terminal 상에서 

~~~
firebase login
~~~


* terminal 상에서 firebase [project ID] 를 입력
  * project ID는 dialogflow의 설정에 들어가면 확인 가능하다.
  * 왜 프로젝트 아이디와 같은 해주는가? firebase를 연결할 때 편리하게 하기 위해서이다. 
  
  
* terminal 상에서 

~~~
firebase init
~~~

* 다음의 선택 사항이 터미널에 출력된다. 

~~~
세번째 카테고리를 선택하여 firebase function 에 deploy 해준다.
선택은 스페이스바 & enter 키
~~~

* 다음으로 firebase project 를 저장할 default directory 를 정해주어야 한다.
~~~ 
weather-bot-vbwyps (weather-bot) 으로 정해준다. (마찬가지로 스페이스바 & 엔터)
~~~

* Cloud functions 에 사용할 언어에 javascript 를 선택한다


* ! 다음으로 등장하는 enlist (디버그를 위한) 를 사용할 것인지를 묻는 질문에 'N' 를 입려해준다.
  ! No 를 입력하는 이유는, enlist 를 사용할 시 사소한 syntax error 를 지나치게 예민하게 반응하기에 사용하지 않는 것이다.
  
  
* 마지막으로 npm 의 dependencies 를 설치할 것이냐는 질문에 'y' 를 입력해준다.


<img width="570" alt="스크린샷 2019-07-24 오후 4 40 04" src="https://user-images.githubusercontent.com/48667219/61774395-bbdd8c00-ae31-11e9-8acd-b8784bc7b11a.png">


* 해당 작업까지 모두 완료한다면 firebase의 functions 카테고리에 들어가면 url이 생성된다. 

* dialogflow 의 console -> fulfilment -> webhook을 enalbe 해준다.

<img width="1680" alt="스크린샷 2019-07-24 오후 4 44 30" src="https://user-images.githubusercontent.com/48667219/61774842-b765a300-ae32-11e9-91ee-24d13ccdeb8e.png">

* 앞서 만들어진 firebase의 url 주소를 복사해서 dialowflow의 url 주소 창에 넣어주고 save 해준다.

* 위치를 찾기위해서는 google map을 이용해야 하는데, 아쉽게도 이는 현재 유료상태이다.
  하지만 걱정하지 말자. 개발자들을 위해 구글 측에서 무료로 일정 기간 동안 사용이 가능하도록 제공한다.
  이후에 더 사용을 원할 경우에만 비용을 추가하면 된다.
  
* 구글 지도를 이용하기 위한 방법.
  1. dialogflow console -> settings -> google cloud의 project ID 클릭 -> google cloud platform의 menu에서 billing을 활성화해준다. 
  
  


