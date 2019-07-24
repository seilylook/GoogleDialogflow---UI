how to implement
================
* google dialogflow agent 생성

* 원하는 디렉터리에 파일 생성. 예) weather-bot

* index.js 파일 생성. 

* terminal 창에서 필요한 모듈들 모두 설치. 예) npm install init

* 날씨 정보를 가져올 홈페이지  https://developer.worldweatheronline.com/api/ 로 접근하여 key 값을 받아온다

* 생성된 키 값을 index.js 파일의 20 번째 줄, functions에 넣어준다. 

* 코드 작성
  * 코드는 다음과 같다.
  ```
     use strict';

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
      
      
* terminal 상에서 npm install -g firebase-tools

* terminal 상에서 firebase login

* terminal 상에서 firebase [project ID] 를 입력
  * project ID는 dialogflow의 설정에 들어가면 확인 가능하다.
  * 왜 프로젝트 아이디와 같은 해주는가? firebase를 연결할 때 편리하게 하기 위해서이다. 
  
* terminal 상에서 firebase init 그리고 firebase deploy 해준다.

* 해당 작업까지 모두 완료한다면 firebase의 functions 카테고리에 들어가면 url이 생성된다. 

* dialogflow 의 console -> fulfilment -> webhook을 enalbe 해준다.

* 앞서 만들어진 firebase의 url 주소를 복사해서 dialowflow의 url 주소 창에 넣어주고 save 해준다.

* 위치를 찾기위해서는 google map을 이용해야 하는데, 아쉽게도 이는 현재 유료상태이다.
  하지만 걱정하지 말자. 개발자들을 위해 구글 측에서 무료로 일정 기간 동안 사용이 가능하도록 제공한다.
  이후에 더 사용을 원할 경우에만 비용을 추가하면 된다.
  
* 구글 지도를 이용하기 위한 방법.
  1. dialogflow console -> settings -> google cloud의 project ID 클릭 -> google cloud platform의 menu에서 billing을 활성화해준다. 
  
  


