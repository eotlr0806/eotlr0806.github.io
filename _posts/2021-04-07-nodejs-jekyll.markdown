---
layout: post
title:  What is Node.js?
date:   2021-04-07 23:33:00 +0900
description: 
img: img-post-nodejs.jpg
fig-caption: 
tags: [Node.js, Javascript]
---

   

최근들어 회사에서 Javascript 에 대한 개발 비중이 많이 늘어났다. ES6 이후에 Javascript 에 많은 변화가 일어나기도 했고, Node.js 를 통해 순식간에 CRUD 서버까지 만들어 낼 수 있으니 Javascript 하나만 잘 해도 서비스 하나는 뚝딱 만들 수 있는 시대가 온 것 같다.

물론 Javascript 하나만 잘하기도 힘들다고 생각한다^^

그래서 오늘은 Node.js 에 대해 한번 정리를 해보려고 한다. 지금까지 개발을 하면서 막히는 부분은 구글링을 통해 충분히 찾을 수 있었으나, **"이게 왜 이렇게 동작하지?"** 에 대해 고민하기는 쉽지 않다. 당장 기간안에 결과물을 만들어야하니까.  

구글링을 통해 기능을 구현했으면서 뿌듯해하는 내 자신을 돌아보면 딱밤을 때려주고 싶다..ㅋ

이김에 차근 차근 기초부터 다시 정리해보자.

___

​    

# What is node.js?

[Node.js 홈페이지 메인](https://nodejs.org/ko/) 에 들어가보면, **Node.js®는 [Chrome V8 JavaScript 엔진](https://v8.dev/)으로 빌드된 JavaScript 런타임입니다.** 라고 명시하고 있다.   

Javascript 라는 언어를 해석하기 위해서는 Javascript 엔진이 필요하다. 일상에서 사용하는 브라우저인 Chrome의 경우에도 C++ 로 개발된, Google의 Open source engine인 V8을 사용하고 있다. **이러한 V8 엔진으로 Javascript 를 해석하는 플랫폼을 Node.js** 라고 한다.

​    

부가적인 설명으로, [Node.js 홈페이지의 About](https://nodejs.org/ko/about/)에 들어가보면 Node.js에 대해 아래와 같은 설명을 한다.

**비동기 이벤트 주도 JavaScript 런타임으로써 Node.js 는 확장성 있는 네트워크 애플리케이션을 만들 수 있도록 설계되었습니다.**   

위 설명을 통해서 _Node.js는 동시에 여러 요청을 처리할 수 있는 비동기성 특징이 강한 서버를 만들기에 효율적이다_  라고 생각할 수 있을것 같다.   

사실 이건 내 의견이라기보다, 이미 Node.js + express 를 통해 간편하게 웹 서버를 구축할 수 있다. 이것은 누구나 아는 사실쓰~

___

​          

# An asynchronous event-driven JavaScript runtime



비동기에 대해 정리하기전에 그 반대인 동기방식부터 짚어보자.

우리가 흔히 사용하는 언어로는 Java가 있는데 일반적으로 Java로 구성된 서버는 Client에서 요청이 들어오면 요청이 완료되어야만 다음 작업을 진행할 수 있다. 이러한 방식을 **동기 방식** 이라고 한다. 

동기 방식의 경우 요청한 순서대로 작업이 처리되어 순차적으로 프로세스를 진행할 수 있지만, 요청이 완료될 때 까지 다른 작업을 하지 못한다는 비효율이 존재한다. 또한 해당 작업이 길어지게 될 경우, 더 많은 Thread를 필요로 하기에 서버의 자원을 많이 차지하게 되는 단점도 있다.



잠깐 나의 경험을 이야기해보자면

```html
벚꽃이 지는 시즌이었다.. 대용량 Excel 을 사용자가 다운로드하였고, Excel 로 만들 데이터 양이 너~무 많아 Tomcat이 해당 작업을 처리하느라 바쁘다며 멈춰버렸다 하하. 해당 작업을 처리하는데 3분이 지나도 처리하지 못했고 CPU도 한계를 찍고 내려오질 못하고.. 결국 kill 로 Tomcat을 죽여버린 기억이 있다 ^.^ 

그때 모니터링을 하고있었으니 망정이지 아니었다면 벚꽃과 함께 나도 져버렸을것이다.
다시 생각해보면 Excel을 만드는 로직 자체에도 문제가 있었고 Client 화면에서도 Excel 데이터를 다운받을 때 기간에 대한 제한이 필요했다.
아니면 Excel 데이터만 처리해주는 서비스를 새로 띄워도 되었고 말이다. 적다보니 좋은생각인듯?
```

이 이야기를 통해 말하고 싶은건 내가 바보라는게 아니다.

Tomcat에서 Excel 을 만들기 위해 모든 Thread를 사용하였기에 해당 작업이 끝날때까지 다른 사용자들의 요청을 처리할 수 없었다.

동기에는 이런 문제점이 있을 수 있다 라는걸 말하고 싶었다. 하지만 잘 코딩하면 이런 문제점도 없다. 내가 바보다.

​    

다시 돌아와서.. 그렇다면 **동기 방식** 의 반대는 무엇일까.

그것이 **비동기 방식** 이다. 우리가 현재 이야기하고 있는 Node.js 가 **비동기 방식** 을 주도적으로 사용할 수 있게 만든 플랫폼이라고 할 수 있다.
그렇다면 **Node.js는 Client의 요청이 들어와도 완료될 때 까지 기다리지 않고 다음 요청을 처리할 수 있다는 것인가?** 라고 물으면  **대부분 그렇다**  라고 답할 수 있다. 즉, 동시에 여러 작업을 처리할 수 있도록 **동시성** 을 제공한다.

​    

여기서 하나만 짚고 넘어가면~

```html
Node.js 도 그렇고, Web개발 시 Front-end도 거의 Javascript 기반인데? 

아! Javascript는 비동기를 제공하는 언어구나!!

정말 좋은 언어다! 

하지만 이건 잘못된 생각이다.
말한것처럼 Javascript는 1개의 Main Thread로 구성되어있고, 명령어들은 Javascript엔진의 Stack에 쌓이게 되어 늦게 호출된 것 부터 순서대로 실행되는 Last In First Out 구조를 가지고 있다. 우리가 Javascript를 통해 비동기 통신을 할 수 있는 이유는 브라우저나 Node.js 환경이 비동기 처리가 가능하도록 제공하기 때문이다.
```



Node.js 는 위에 말한것 처럼 **비동기** 라는 것에 굉장히 집중한 모습을 보이고 있다. 비동기를 통해 여러 요청을 동시 다발적으로 처리할 수 있는데 그럴 수 있는 이유는 특정 행위 즉 어떤 이벤트가 발생할 때 마다 호출 할 수 있도록 구조가 설계되어 있다고 감히 말해보겠다. 이런걸 이벤트 주도 라고 한다.

**이벤트 주도** 라하면, **특정 ''이벤트'' 가 발생했을 때 우리가 선언해놓은 함수를 수행** 한다고 생각하면 될 것 같다. 이때 우리는 선언한 함수를 콜백함수라고 부른다.

예를 들어보자

```
홍길동 고객이 100억을 대출을 받고 10년째 안갚고있었다.
나팀장은 너무 너무 화가나서 해당 고객의 담당 직원에게 찾아가 막 화를 냈다.

"이봐 김과장.. 너 홍길동 고객님한테 100억 빨리 다 회수 해!"
하지만 우리의 김과장은 최고의 미드필더 선수였다.
"이대리.. 미안한데 내가 VIP 고객의 요청이 있어서.. 너가 대신 홍길동한테 100억좀 회수해 줘.. 그리고 10억씩 회수할때마다 나한테 꼭 알려줘!"
우리의 성실한 이대리는 홍길동을 독촉해가며 돈을 받아낸다.
"김과장님 10억 회수완료했습니다. 다시 회수해올게요."
"이대리 고생많았어.. 지금 받은 10억은 내 실적으로 올려 놓을게 고마워."
```

적으면서 화가났다.

김과장은 Node.js 의 달인이 분명하다. 여기서 나팀장은 김과장에게 100억을 받아오라고 요청을 했으나, 김과장은 자신이 할 일을 이대리한테 패스해버린다. 그리고 심지어 자기는 VIP 고객의 요청을 해결하러 떠나버렸다. 김과장은 동시성을 갖추고 있는 사람인것이다..

이후, 이대리가 100억 중 10억을 받은 다음 김과장에게 알려준다. 즉, 김과장 입장에서는 10억을 회수했다는 **이벤트** 가 발생한것이다.

이러한 이벤트가 발생하면 김과장은 **10억을 자신의 실적으로 등록한다** 이런 비열한 행동을 우리는 **콜백 함수** 라고 한다.

나도 김씨인데;;



그렇다면 이번에는 코드로 이해해보자.

```javascript
window.onload = function() {
	console.log('로드완료.');
}
```

위 코드는 우리가 웹페이지가 로드 완료된 이후에 작업을 선언하는 코드인데,
여기서 onload는 이벤트가 발생하는지 항상 체크하고있는 이벤트 리스너(김과장)가되고, 해당 이벤트가 발생하면 function으로 선언한 console.log('로드완료.') 가 실행될텐데 이 function이 콜백함수(실적올리기)가 된다.

​    

Node.js 에서 제공하는 많은 API들은 위와 같이 **특정 이벤트가 발생했을 때, 호출하는 콜백함수를 선언할 수 있도록 제공한다.**

콜백 함수를 선언할 수 있다는 말은, "해당 요청이 끝났다면 이벤트를 줘, 나 다른거 하고 있을게" 라고 해석할 수도 있으며, 이러한 점들이 Node.js 가 동시성을 가질 수 있게 한다. 물론 동기로 호출할 경우 "나 아무것도 안하고 기다리고 있을게" 로 바껴버리겠지만~

```
var fileSystem = require('fs');

fileSystem.readdir('.', function (err, files){  
    // callback
});
console.log('1');
```

위 코드는 File System이라는 라이브러리이며, 특정 디렉토리의 파일을 읽는 API이다.
readdir 은 비동기로 호출되기에 readdir 함수의 작업이 완료되지 않더라도, console.log('1') 가 먼저 실행된다.

이렇게 Node.js 는 비동기 호출 API들을 제공하며, 해당 API들은 콜백 함수를 선언할 수 있게하여 API에 요청한 작업이 끝나면 콜백함수를 실행 하도록 한다.   

___

​      

# Understanding Asynchronous Node.js

Node.js 에는 Javascript 를 구동할 v8 엔진과 그 외 여러 라이브러리들로 구성되어있는데, 주로 libuv 라이브러리에 중점을 두고 동작원리에 대해 적어보려 한다.


​     


#### 일반적으로 Javascript는 Single Thread로 알고있는데 어떻게 비동기 처리가 가능할까?


그것을 알기 위해 Javascript 엔진의 메모리 구조에 대해 얕게 이야기해보겠다.

Javascript 엔진에는 메모리를 할당하는 Heap 영역, 코드의 함수등이 호출됨에 따라 해당 함수를 저장하는 Stack 영역이 존재한다.

```javascript
function delay(){
  console.log('delay');
}

console.log(1);
console.log(2);
setTimeout(delay,3000);
console.log(3);
```

위 코드가 실행되면 결과는 아래와 같다.

```
1
2
3
delay
```

여기서 우리가 아는 Single Thread는 하나의 작업이 완료처리되기 전까지 다음 작업을 진행할 수 없으므로, 예상되는 결과는

```
1
2
delay
3
```

이 나와야 할텐데 어떻게 1,2,3이 순서대로 찍힐 수 있었을까?    



Stack 영역에는 순서대로 

```javascript
main() 호출
-> 
console.log(1) 호출
-> 
console.log(2) 호출
-> 
setTimeout(delay,3000) 호출
-> 
console.log(3) 
->
delay() 
-> 
console.log('delay')  
```

순으로 호출이 된다.

**"뭐야 왜 갑자기 delay가 console.log(3) 보다 늦게 호출 돼?"**

그 이유는 **setTimeout() 함수의 Timer 역할은 Javascript 엔진이 setTimeout API 에서 진행하게 된다.** 

setTimeout()의 Timer(3000) 작업은 API 를 처리해주는 라이브러리에 등록이 되고, Stack에서 setTimeout은 빠지게 된다. 

그렇기에 Stack에는 setTimeout()의 바로 다음인 console.log(3) 이 쌓여 호출되게 된다. 



그렇다면 setTimeout이 라이브러리에 등록한 Timer가 시간이 다 되었을 때 어떻게 될까?   

여기서 등장하는 개념이 **Task Queue 와 Event Loop** 이다. 

setTimeout에 등록된 콜백 함수인 delay 가 3초가 지난 후, Task Queue에 등록이 되고, Event Loop 는 Stack에 쌓여있는 함수가 없을 때, Task Queue에 쌓여있는 함수를 Stack으로 올리게된다.   

   

즉, setTimeout은 Javascript 엔진의 Stack에서 호출되면서 "3초 기다렸다가 알려줘" 를 비동기로 던져버린것이다.

3초가 지난 후, Task Queue란 녀석에 delay 함수가 등록되고 Event Loop란 친구가 Javascript 엔진의 눈치를 계속 살피며 일이 없는걸 확인하면 Task Queue의 delay 함수를 Stack에 던져버린다.

**이때 v8의 Stack 영역외에 비동기 처리를 해주는 API 는 웹에서 실행이 될 경우 브라우저가 될 것이고, node.js 의 경우 libuv가 된다.** 
    

​    

#### **Node.js 는 libuv가 있기에 Single Thread임에도 불구하고 비동기 처리를 통해 여러 작업을 처리할 수 있다**

node.js 는 모두가 알고 있는 것처럼, 이벤트 기반 언어이다. 그렇기에 v8 엔진으로만 모든 로직이 처리되는게 아니고, **특정 이벤트의 경우 Node API 를 통해 libuv 로 전달**되게 된다. 이렇게 특정 이벤트의 경우 libuv로 전달되어 처리되므로, v8 엔진은 해당 이벤트가 처리될때까지 기다리지 않고 다음 로직을 처리할 수 있다. libuv는 v8 엔진으로 부터 전달 받은 이벤트를 처리한 후, 지정한 콜백 이벤트를 Task Queue 로 전달한다. 그리고 Event Loop는 Stack 이 비었을 경우, Task Queue 에서 해당 콜백을 꺼내 Stack으로 올려준다. 이렇게 Node.js는 v8과 libuv 를 통해 비동기 처리가 이루어 진다.

대부분의 로직은 libuv를 통해 이뤄지며, 이러한 libuv가 Single Thread 로 동작된다. 그렇게이 Node.js 를 Single Thread 라고 한다.





___

​     

과연 Node.js로 얼마나 큰 서비스까지 구현할 수 있을까? 라는 생각을 가끔 한다.

위에 말한것 처럼, Node 에서 제공하는 API 들은 대부분 비동기를 지원한다. 하지만 우리가 기본적으로 사용하는 조건문이나 반복문은 Javascript 엔진에서 실행되는 로직이므로, 비동기 처리가되지 않는다. 

물론 비동기 형식으로 호출하면 되겠지만 어느 순간에는 데이터를 먼저 처리 해야 다음 작업을 진행할 수 있는 동기 방식도 필요로 할 수 밖에 없기에 서비스 로직이 많은 대형 프로젝트를 Node.js 만으로 구성할 수 있을까? 하는 생각이 든다.

전에 커뮤니티에서 Node.js 로 서버를 구성하였지만, 결국 서비스가 커지면서 Java로 서버를 분리했다는 글을 본적도 있기도 하고..



무튼 Node.js 를 사용하면 코드의 라인도 줄일 수 있고, NPM을 통해 수 많은 라이브러리를 간편하게 이용할 수도 있으며 개발 속도도 굉장히 빠르다고 생각한다. 

하지만! CRUD 작업 외에 복잡한 서비스 로직이 많은 솔루션을 Node.js 로 어떻게 구성해야 할까? 라는 궁금증은 쉽게 해소되지 않는다 ~.~ 뭐 찾아보면 NPM에 다 있어서 비동기를 제공하긴 할테지만~ 

마무리하기전에 갑자기 생각난건데, Node.js 로 개발을 하면서 어려웠던건 구조 설계였다. 찾아 보니 Node.js 3-Layer 구조가 있던데 그걸로 서비스를 개발한 후기를 다음엔 생생하게 적어봐야겠다 후후





#### 참조

----

[Javascript 비동기 원리1](https://medium.com/@vdongbin/javascript-%EC%9E%91%EB%8F%99%EC%9B%90%EB%A6%AC-single-thread-event-loop-asynchronous-e47e07b24d1c)

[Javascript 비동기 원리2](https://new93helloworld.tistory.com/137)

[Node.js 동작 원리1](https://medium.com/@vdongbin/node-js-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC-single-thread-event-driven-non-blocking-i-o-event-loop-ce97e58a8e21)

[Node.js 동작 원리2](https://sjh836.tistory.com/149)

[동기방식과 비동기방식](https://jieun0113.tistory.com/73)

[Node.js Single Thread](https://medium.com/@gwakhyoeun/%EC%99%9C-node-js%EB%8A%94-single-thread-%EC%9D%B8%EA%B0%80-bb68434027a3)

