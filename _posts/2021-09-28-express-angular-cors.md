---
layout: post
title:  "[Web] Express/Node.js CORS 에러 해결"
date:   2021-09-28 20:30:00 +0900
categories: web nodejs express angular cors
---

Express를 사용한 Node.js에서 CORS 에러를 해결하기 위해 아래와 같은 설정 추가
```javascript
let express = require('express');

...

// Middlewares
let app = express();
app.use(function (req, res, next) {
    res.header('Access-Control-Allow-Origin', '*');  // 와일드카드(*) 대신 실제 도메인 지정
    res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
    res.header('Access-Control-Allow-Headers', 'content-type, x-access-token'); //1
    next();
});

...

// Server
let server = app.listen(8080, ()=>{
    logger.info('Server is running');
});

// export the server to make tests work
module.exports = server;
```

front-end 개발 시 CORS 에러가 자주 발생하는데 서버측에서 CORS 에러에 대한 처리를 위와 같이 해주었다고 해도 localhost로 개발 및 테스트 과정에서 계속 문제가 발생할 수 있음.     
따라서 아래와 같은 설정이 필요 (front-end 개발 시 Angular를 사용함)
1. proxy.conf.json 파일 생성 (프로젝트 최상위 디렉토리 아래)
```json
{
  "/api": {
    "target": "http://localhost:8080",
    "pathRewrite": {
      "^/api": "/api"
    },
    "changeOrigin": true,
    "logLevel": "debug"
  }
}
```
2. package.json에서 start 명령 수정
```json
{
  "name": "프로젝트 이름",
  "version": "0.0.0",
  "scripts": {
    ...
    "start": "ng serve --proxy-config proxy.conf.json",
    ...
  },
  ...
}
```
위와 같이 하지 않고 ng serve 명령을 그대로 쓰고 싶은 경우에는 아래와 같이 angular.json 파일을 수정
```json
"projects": {
    "프로젝트 이름": {
      ...
      "architect": {
        ...
        "serve": {
          ...
          "options": {
            "browserTarget": "bcheck-web:build",
            "proxyConfig": "proxy.conf.json"
          },
          ...
        },
        ...
      },
    }
}
```
