---
layout: post
title: "socket.io의 기본 개념 및 구조 in nodejs"
date: 2017-05-14
categories:
  - socket.io
  - node.js
description: 
image: https://unsplash.it/800/600?image=1
image-sm: https://unsplash.it/500/300?image=1
---
<a href="https://socket.io/">socket.io</a>는 서버와 클라이언트가 양방향 통신을 할 수 있도록 도와주는 웹소켓 API의 하나로써, 넓은 브라우저 지원성과 편리한 사용성 때문에 node.js와 함께 널리 사용되고 있다.

이 글에서는 socket.io의 기본적인 구조와 이벤트 전송 흐름을 설명하려고 한다.
<br>
<h2>1. socket.io의 구조</h2>

socket.io는 Namespace와 Room이라는 두 가지 방식으로 이벤트 전송의 도메인을 구분한다.

<br>
<h4>Namespace</h4>

Namespace는 path를 통해 소켓의 도메인을 구분하는 단위이다.
Namespace는 다음과 같이 설정할 수 있다.

``` javascript 
const io = IO(server, {path: '/namespace_A'});
```

<h5>*네임스페이스간의 종속 관계에 대해서 조금 찾아봤는데 path가 '/'인 경우에만 종속 관계를 갖는 것 같다. (사실 잘 모르겠다)
참고 : https://github.com/socketio/socket.io/issues/2124
</h5>
<br>
<br>
<h4>Room</h4>

Room은 네임스페이스의 안에서 소켓들을 구분하는 단위이다. 일종의 그룹이라고 생각하면 된다.
특정 room에 들어가는 것과 나가는 코드는 다음과 같다.

``` javascript 
socket.join('room1');
socket.leave('roon1');
```

<img style="margin-bottom:0" src="/assets/image/2017-05-14-nodejs-socketio-concept-img1.png" width="80%">
<center>&lt;소켓 connection 구조(Namespace, Room)&gt;</center>
<br>
<br>
<h3>2. Socket.io 이벤트 전송 흐름</h3>

``` javascript
// 1) Socket의 Namespace와 함께 소켓 인스턴스를 생성한다.
const io = IO(server, {path: '/namespace_A'});

// 2) Socket connection 
io.on('connection', function(socket) {
	console.log('connected');

	// 3) connection의 콜백함수 안에 이벤트 등록
	socket.on('join', (params) => {
        console.log('Someone joined');
        socket.join('room1');

		// 나를 포함한 같은 Namespace 안의 소켓에게 이벤트 전송 
		io.sockets.emit('event name', message);

		// 나를 제외한 같은 Namespace 안의 소켓에게 이벤트 전송 
		socket.broadcast.emit('event name', message);

        // 나를 포함한 'room1' Room 안의 소켓에게 이벤트 전송
        io.sockets.in('room1').emit('event name', message);

        // 나를 제외한 'room1' Room 안의 소켓에게 이벤트 전송
        socket.broadcast.to('roon name').emit('event name', message);
    });
}
```


