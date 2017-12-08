---
layout: post
title: "Base64, Blob URL Image Binding"
date: 2016-01-31
banner_image: 
tags: [html, javascript]
comments: true
---
Blob (Binary Large Object), Base64 를 사용하여 img 태그에 

데이터를 바인딩 하는 방법에 대해 알아보겠습니다.

<!--more-->

간혹 웹페이지에 있는 이미지들을 특수 목적에 의해 이미지를 불러와 base64 로 바인딩 하는 경우가 종종 있습니다. 

용량이 크고 많은 이미지가 아니라면 base64 로 바인딩해서 사용하는 부분에 큰 문제가 되지 않지만, 용량이 크고 많은 이미지를 base64 로 바인딩 사용한다면,

base64 String 에 의해 DOM 문서 크기가 상당히 커지면서 성능에 대한 저하가 있습니다.

따라서, 이런 문제를 해결하기 위해 blob 객체로 바꾸고, blob URL 을 사용하여 이미지를 바인딩한다면,

이런 성능 저하 문제를 어느 정도 해결 할 수 있습니다.

{% include center_image_caption.html imageurl="/assets/160131/base64img.png" caption="base64 string 으로 이미지를 바인딩한 DOM 문서" %}

{% include center_image_caption.html imageurl="/assets/160131/blobimg.png" caption="blob URL 을 통한 이미지를 바인딩한 DOM 문서" %}

[SampleImageBinding.html](/assets/160131/SampleImageBinding.html) 