---
layout: post
title: "navication에서 router.url 사용시 / 만 나오는 문제"
date: 2017-04-02
categories:
  - Angular2
description: 
image: https://unsplash.it/2000/1200?random
image-sm: https://unsplash.it/500/300?random
---
router.url을 출력했는데 항상 / 값만 나오는 경우가 있다.

app.component.html에 다음과 같이 코드를 구성한 경우,

{% highlight html %}
<navigation></navigation>
<router-outlet></router-outlet>
{% endhighlight %}

navigation의 component에서 router.url을 사용하면 어느 페이지건 항상 결과값이 '/' 값으로 나온다.

한참 삽질을 해본 후의 결론은 이렇다. 

<b>router.url은 해당 component가 위치한 곳의 url을 반환한다.</b>
<br><br>
<h3>해결방안</h3>
<br>
<h4>1. document.location.href 사용</h4>
document.location.href를 사용하여 직접 주소창의 문자열을 가져와서 검사하는 방법이다.
{% highlight typescript %}
if((document.location.href).search('/myurl') !== -1) 
  console.log('It\'s myurl');
else 
  console.log('It is not myurl');
{% endhighlight %}

<h4>2. router.events 사용</h4>
구글링을 하다가 우연히 알아낸 방법인데, (순수한)javascript 문법을 사용하는게 싫다면 사용해 볼만한 방법이다.

``` javascript
router.events.subscribe(event => {
  console.log(event.url);
});
```
router.events.subscribe를 사용하면 router 값이 변하는 때에 이벤트를 발생시키고 함수를 실행하게 되는데 이 함수 안에서 event.url을 사용하게 되면 router.url에 해당하는 값이 원하는 대로 잘 나온다.


