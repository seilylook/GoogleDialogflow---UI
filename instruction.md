how to implement
================
* google dialogflow agent 생성

* 원하는 디렉터리에 파일 생성. 예) weather-bot

* index.js 파일 생성. 

* terminal 창에서 필요한 모듈들 모두 설치. 예) npm install express

* 코드 작성
  * 코드는 다음과 같다.
  ```
    * 'use strict';

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
      ```


