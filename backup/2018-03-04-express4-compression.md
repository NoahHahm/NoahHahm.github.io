---
layout: post
title:  "Express4 HTTP Compression"
date:   2018-03-04
banner_image: 
tags: [nodejs, express]
comments: true
---

gzip 을 통한 Express4 + Node.js 환경의 성능을 이끌어내는 방법을 소개 합니다.

<!--more-->

HTTP compression 을 사용하는 이유는 클라이언트의 요청 본문을 압축되지 않은 상태에서 받게 되면 그만큼 용량과 속도 측면에서 비효율적으로 다운로드 합니다.



기본적으로 Node.js 환경에서 Express4 Framework 사용시, HTTP compression 을 제공하지 않습니다.

```bash
$ npm install compression
```

