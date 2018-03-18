---
layout: post
title:  "Express4 Route Handler"
date:   2018-03-18
banner_image: 
tags: [nodejs, express]
comments: true
---

Express4 + Node.js 환경의 라우팅을 관리하는 방법에 대해 소개 합니다.

<!--more-->

```bash
$ npm install express
```

처음 Node.js + Express 를 접한다면 아래와 같은 코드를 만나게 됩니다.
 - ([http://expressjs.com/en/starter/hello-world.html](express start link))

```javascript
const express = require('express');
const app = express();

app.get('/home', (req, res, next) => {
    res.status(200).end('home');
});

app.listen(8080);
```

가장 베이스가 되는 express 코드 입니다.

이제 개발자는 저 영역에서 코드를 구현하게 되는데, 지속적인 api 증가시 아래와 같은 상황에 직면하게 됩니다.

```javascript
const express = require('express');
const app = express();

app.get('/home', (req, res, next) => {
    res.status(200).end('home');
});

app.get('/home/test', (req, res, next) => {
    res.status(200).end('home test');
});

app.get('/data', (req, res, next) => {
    res.status(200).end('data');
});

app.get('/internal', (req, res, next) => {
    res.status(200).end('internal');
});

app.listen(8080);
```

한개의 파일에서 증가되는 코드를 막기 위해 이제 Route 를 적용해 봅시다.

- index.js
```javascript
const express = require('express');
const app = express();

app.use(require('./route/homeRoute'));

app.listen(8080);
```

- route/homeRoute.js
```javascript
const express = require('express');
const route = express.Router();

route.get('/home', (req, res, next) => {
    res.status(200).end('home');
});

route.get('/home/test', (req, res, next) => {
    res.status(200).end('home test');
});

module.exports = route;
```


파일도 나누어졌고, 그나마 좀 괜찮아졌네요.

실무 및 대부분의 개발자는 위와 같은 방법을 쓰게 됩니다.

이 방법도 사실 문제 없습니다.

목적에 따른 라우팅을 파일로 분기해서 관리하기 때문에 문제가 전혀 없죠.

하지만 라우트 폴더 안에서 파일이 증가하고, URL 을 바꾼다거나 하는 경우

길어진 소스코드 안에서 찾아야 하는 문제만 직면하는 상황만 생길 뿐 입니다.

이제 여기서 한가지 더 생각해서 나누어 보겠습니다.

route url 만 관리하고 소스코드는 타프레임워크처럼 Controller 파일로 나누어서 관리하고 싶다면 어떻게 해야할까요?

클로저를 사용하면 해결이 가능합니다.


- index.js
```javascript
const express = require('express');
const app = express();

app.use(require('./route/homeRoute'));

app.listen(8080);
```

- route/handleAttach.js
```javascript
module.exports = (controllerName, funcName) => {

    if (!controllerName || !funcName) {
        throw "controller and method error."
    }

    const controller = require(`../controllers/${controllerName}`);
    if (!controller) {
        throw "controller require error."
    }

    const method = controller[funcName];
    if (!method) {
        throw "method not found error.";
    }

    return method;
}
```

- route/homeRoute.js
```javascript
const express = require('express');
const route = express.Router();
const handleAttach = require('./handleAttach');

route.get('/home', handleAttach('HomeController', 'Home'));
route.get('/home/test', handleAttach('HomeController', 'HomeTest'));

module.exports = route;
```


- controllers/HomeController.js
```javascript
class HomeController {
    
    constructor() {

    }

    static Home(req, res) {
        res.status(200).end('Home!');
    }

    static HomeTest(req, res) {
        res.status(200).end('Home Test!');
    }
}

module.exports = HomeController;
```

handleAttach 라는 함수 하나가 생겼습니다.

handleAttach의 역할은 라우트에서 지정한 Controller이름, Controller 의 함수이름 두가지 명시에 따라서,

해당 URL 라우트가 어떤 컨트롤러에 있는 함수를 실행할것인지 에 대한 액션을 취하게 됩니다.

기존 익명 람다에 구현한 방법과 위 Controller로 나누어서 사용했을때의 이점이 보이시나요?

이제 개발자는 Route 폴더에서 URL, Controller 명칭만 관리하면 되고,

실제 액션에 대한 코드는 Controller Class 에서 관리하면 되겠네요.

어떤 방법을 쓰든지 간에 자신이 편한 방법으로 쓰면 되겠지만

개인적으로 프로젝트의 규모가 커지고, 수정이 비번하게 이루어지는 상황에서는 이 방법이 가장 효율적이었습니다.