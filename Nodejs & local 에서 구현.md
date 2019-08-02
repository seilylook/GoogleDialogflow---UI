How to Implement
================

1. 기존에 존재한 dialogflow 의 프로젝트를 선택하거나, 새로운 프로젝트를 dialogflow 에 생성한다. 

  
  <img width="925" alt="스크린샷 2019-08-02 오후 3 07 08" src="https://user-images.githubusercontent.com/48667219/62348235-64c56e80-b537-11e9-977e-b2e2071671dd.png">


2. dialogflow 의 setting 에 들어가 'project ID' 를 클릭한다. 

  <img width="918" alt="스크린샷 2019-08-02 오후 3 10 48" src="https://user-images.githubusercontent.com/48667219/62348488-c685d880-b537-11e9-8534-1def1af19698.png">

  -> 클릭할 시, 'google cloud platform' 으로 이동한다. 
  -> 만약 기존에 다른 프로젝트를 진행하는데, billing을 설정해 놓았다면, 자동으로 enable 될 것이다. 기존에 진행했던 프로젝트가 존재하지 않는다면, 새롭게            billing 을 해주어야 한다. 
  
  
3. Dialogflow API 를 enalble 시켜 주어야 한다. 

  <img width="1680" alt="스크린샷 2019-08-02 오후 3 20 42" src="https://user-images.githubusercontent.com/48667219/62348994-62fcaa80-b539-11e9-88e5-5e2a3108d74b.png">
  
  <img width="763" alt="스크린샷 2019-08-02 오후 3 23 21" src="https://user-images.githubusercontent.com/48667219/62349068-8e7f9500-b539-11e9-95a2-cc84eb4ea29b.png">
  
  <img width="759" alt="스크린샷 2019-08-02 오후 3 24 33" src="https://user-images.githubusercontent.com/48667219/62349142-d8687b00-b539-11e9-9266-e27a75739eec.png">


  -> 해당 작업을 하는 이유는, local 의 node js 에서 google dialogflow API library 를 사용할 때 사용허가를 해주기 위해서다.
  -> 이 작업을 해주지 않는다면, permission error 가 발생하여 API 를 사용할 수 없다. 
  
  
4. https://cloud.google.com/docs/authentication/getting-started 으로 이동하여 API 접근을 위한 key 값을 생성하고 이를 path 환경 설정을 통해
   설정해 주어야 한다. 
   
   * 이에 대한 작업은 https://cloud.google.com/storage/docs/reference/libraries 으로 이동한다. 
   
      <img width="879" alt="스크린샷 2019-08-02 오후 3 40 01" src="https://user-images.githubusercontent.com/48667219/62349884-f636df80-b53b-11e9-987a-2008d58a1c9a.png">

      -> json 파일을 다운로드 해주었다면 환경 변수를 설정해 주어야 한다. 
      
      ~~~
      export GOOGLE_APPLICATION_CREDENTIALS = "파일 위치" 
      ~~~
      
      -> 주의할 점: 환경 변수 설정은 현제 terminal 및 shell 세션에만 적용되므로 새로운 섹션을 열었을 경우 변수를 다시 설정해야 한다.
      
      
 5. 이제 nodejs 를 이용하여 코드 작업을 수행한다
 
  가. 먼저 Node js 작업 환경을 설정해준다. 
  
    ~~~
    npm init
    ~~~
    
    ~~~
    npm install express
    ~~~
  
    ~~~
    npm install --save dialogflow
    ~~~
    
    
  나. 
  
  
      
      
  
