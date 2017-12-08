---
layout: post
title:  "Interaction Scroll Motion"
date:   2015-12-17
banner_image: 
tags: [css, javascript, interaction, UI]
comments: true
---

보통 데스크탑 환경에서는 스크롤 모션을 넣는 경우는 드물지만,
모바일 환경에서는 많은 앱, 웹에서 스크롤 모션을 구현하여 사용 하고 있습니다.
네이버 블로그 모바일 화면에서도 스크롤을 통한 모션이 들어가 있고,
카카오에서 제작한 brunch(브런치) 앱에서도 스크롤 모션이 메인으로 사용되고 있습니다.

이러한 스크롤 모션을 모바일 웹브라우저 에서 구현하는 방안에 대해서 알아보겠습니다.

<!--more-->


먼저, 아래 스크롤 모션을 먼저 봐주시기 바랍니다.

{% include center_image_caption.html imageurl="/assets/171107/img01.gif" caption="네이버 모바일 블로그에 적용된 스크롤 모션." %}

{% include center_image_caption.html imageurl="/assets/171107/img02.gif" caption="brunch 와 동일한 스크롤 모션을 구현할 최종 결과물" %}


앞서 말씀 드렸던 것 처럼, brunch 와 동일한 스크롤 모션을 구현하기 위해서 우선 어떻게 접근 해야 할지 생각해봅시다.

1. 모든 모바일(아이폰, 안드로이드) 브라우저 에서 바운스백 효과를 포함한 동일한 환경으로 보여주어야 한다.
2. 스크롤시 이미지 영역은 위로 서서히 올리고, 하단 내용 영역을 위로 겹쳐서 올린다.

첫번째, 모바일 OS 마다 스크롤 이벤트의 동작이 다릅니다.

IOS 의 경우, 스크롤시 이벤트가 지속적으로 들어오는 반면, 안드로이드 에서 스크롤 시작, 종료 타이밍에만 이벤트가 들어오기 때문에 실시간으로 스크롤시 각 영역에 이벤트를 줄 수 없습니다.

이런 문제를 해결하기 위해, IScroll ([http://iscrolljs.com](http://iscrolljs.com)) 라이브러리를 사용해야 합니다.

IScroll, IScroll Probe 를 사용하면 바운스백 효과와 더불어 어떤 모바일 OS 환경에서도 IOS 처럼 실시간으로 Scroll 이벤트를 감지할 수 있습니다.

두번째, 먼저 해당 영역을 나누어 봅시다.

크게 이미지 영역이 있을것 이고, 하단 컨텐츠 내용이 있을겁니다.

{% include center_image_caption.html imageurl="/assets/171107/content.png" %}

검정색의 이미지 영역, 전체를 감싸고 있는 wrap 영역, 그리고 내용을 가지는 contents 영역

이렇게 3개의 영역이 필요합니다.

주황색의 wrap 영역은 투명 처리하고, 이미지의 Height 영역 만큼의 padding-top 값을 줘야 합니다.

사실상 주황색 wrap 영역은 이미지 영역을 모두 채워버렸지만, 배경색이 없기 때문에 뒤에 image 영역이 노출되어 보여 집니다.

그리고 wrap 영역 안에 contents 영역은 빨간색 영역까지 의 padding 값 때문에 아래에 있어 보이는 효과를 가지게 됩니다.

실질적으로 스크롤 하는 영역이 바로 wrap 영역 입니다.

스크롤을 하면 당연히 contents 영역은 위로 올라갈 것인데, 이때 이미지의 y 위치를 위로 올리면 

만들고자 하는 모션이 완성되게 됩니다.

한 가지 주의해야 하는 건, padding 값은 이미지의 세로 크기를 반드시 알아야 하기 때문에 

image를 로딩 하는 시점에 height 값을 불러와 계산하셔야 합니다.

### 소스코드

{% highlight javascript %}
$(function() {
    
    var mScroll = new IScroll('#wrapper', { 
        mouseWheel: true,
        scrollbars: true,
        probeType: 3
    });
    
    mScroll.on('scroll', function() {
        // 스크롤 이벤트 발생시 이미지 wrap 영역을 y/2 만큼 위로 올림.
        $('#img-wrap').css('transform', "translate(0, "+this.y/2+"px)");
    });
    
    // 화면 리사이징 되는 경우.
    $(window).resize(function() {
        resize();
    });
    
    $(window).load(function() {
        resize();
    });
    
    function resize() {
        
        // 이미지 wrap 의 세로 크기를 구해와서 padding-top 셋팅.
        var height = $('#img-wrap').height();
        $('.content-wrap').css('padding-top', height + "px");
        
        // 스크롤 재계산.
        mScroll.refresh();
    }
    
});
{% endhighlight %}

화면 리사이징 에 대한 처리도 필요 합니다. (위 소스코드 참고)

{% highlight html %}
<div id="img-wrap">
    <img src="./img/01.jpg">
</div>
<div id="wrapper">
    <div id="scroller">
        <div class="content-wrap">
            <ul class="contents">
                <li>Pretty row 1</li>
                <li>Pretty row 2</li>
                <li>Pretty row 3</li>
                <li>Pretty row 4</li>
                <li>Pretty row 5</li>
            </ul>
        </div>
    </div>
</div>
{% endhighlight %}

위와 같이 레이아웃을 잡아야 합니다.

[SampleMotions.zip](/assets/171107/SampleMotions.zip) 