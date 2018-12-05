---
title: Ahea HackDay2018 - Viewer 팀 리뷰
url: 1789.html
id: 1789
categories:
  - 미분류
date: 2018-09-20 16:46:36
tags:
---

https://github.com/devahea/2018hackerton-viewer

목표
==

화면에 nurikabe를 풀고 있는 진행상황을 출력해준다 팀이 2팀이기 때문에 2개 이상의 퍼즐을 실시간으로 보여줘야 한다

설계
==

1.  화면에 출력
    *   누리카베 퍼즐을 어떻게 화면에 출력해야 할까?
    *   웹에서 누리카베 퍼즐을 어떤 기술로 해야 할까?
    *   canvas로 그려보면 어떨까?
2.  실시간으로 메세지를 전송
    *   각 팀에서 보내주는 퍼즐진행 정보를 빠르게 화면으로 보내줘야 한다
    *   websocket이나 socketio를 이용해보면 좋겠다

구현하기
====

화면에 출력하기
--------

우리 프로젝트는 웹 환경으로 제공하려고 했습니다. 이때 웹에서 도형을 그리는 방법은 뭐가 있을까 고민했을때 1도 망설임없이 canvas를 생각했습니다. 첫번째로 네모를 그리는 방법을 찾았습니다 https://www.w3schools.com/tags/canvas_rect.asp \[code lang=javascript\] var c=document.getElementById("myCanvas"); var ctx=c.getContext("2d"); ctx.rect(20,20,150,100); ctx.stroke(); \[/code\] rect를 사용하여 네모를 그리는 방법을 알게 되었습니다 w3school을 보면 paramewter로 x,y,width,height를 받는것을 알수 있습니다 이를 이용하면 내가 원하는 위치에 원하는 크기로 그릴수 있을거 같습니다 두번째로 네모를 여러개를 그리는 방법을 고민했습니다 \[code lang=html\] <!DOCTYPE html> <html> <head> <title>Hello canvas</title> <a href="/webjars/jquery/jquery.min.js">/webjars/jquery/jquery.min.js</a> </head> <body> <div id="main-content" class="container"> </div> </body> function createMap(id, width, height) { $("#main-content").append("") var c = document.getElementById("canvas" + id); var ctx = c.getContext("2d"); ctx.font="15px Arial"; var rectSize = 25; var startPoint ={x:25, y:25}; for (i = 0 ; i </html> \[/code\] 만약 a팀이 1번 퍼즐을 풀기위해 맵을 만든다면 `createMap('a_1', 10, 10)`을 호출한다면 10 X 10짜리 네모들이 출력이 될것입니다 세번째로 네모 안에 텍스트 집어넣기입니다 누리카베는 총 3개의 타입이 있습니다 검은색이 칠해진 block, 숫자가 들어간 number, 점 하나로 이루어진 room입니다 네모를 그리기 위해서는 `rect`함수를 사용했다면 텍스트를 넣기 위해서는 `fillText`, 검은색으로 칠하려면 fillRect를 사용하면 됩니다 \[code lang=javascript\] if (type == 'number') { ctx.fillText(number,startPoint.x + (rectSize * x)- 16, startPoint.y + (rectSize * y)- 6); ctx.stroke(); } else if (type == 'block') { ctx.fillRect(startPoint.x + (rectSize * x), startPoint.y + (rectSize * y), rectSize, rectSize); ctx.stroke(); } else if (type == 'room') { ctx.fillText("o",startPoint.x + (rectSize * x)- 16, startPoint.y + (rectSize * y)- 6); ctx.stroke(); } \[/code\]

통신하기
----

실시간으로 빠르게 server와 client가 데이터를 주고 받으려면 어떻게 할까 고민하는 중 websocket을 이용하면 어떨까 하여 구글링해봤습니다 `spring websocket`이라고 구글링을 하게 되면 https://spring.io/guides/gs/messaging-stomp-websocket/ 링크가 상단에 나오는 것을 확인할 수 있습니다 해당 샘플로 프로젝트를 세팅한 후 기능 동작을 확인한 후 우리의 비즈니스에 맞게 수정하는 작업을 진행하였습니다 (수정할때 기존것을 크게 만지지 않았습니다) 퍼즐 맞추는 팀이 처음 데이터를 보내주는 `create`, 추가적으로 퍼즐을 풀면서 보내는 `putItem`을 하나 만들었습니다 \[code lang=java\] @Autowired SimpMessagingTemplate template; @RequestMapping("/create") public @ResponseBody String create(String name, Integer x, Integer y, String method, Integer number, String message){ JsonObject jsonObject = new JsonObject(); jsonObject.addProperty("name", name); jsonObject.addProperty("x", x); jsonObject.addProperty("y", y); jsonObject.addProperty("method", "create"); System.out.println("jsonObject.toString() " + jsonObject.toString()); template.convertAndSend("/topic/greetings", jsonObject.toString()); return "goods"; } @RequestMapping("/putItem") public @ResponseBody String putMessage(String name, Integer x, Integer y, String method, Integer number, String message){ JsonObject jsonObject = new JsonObject(); jsonObject.addProperty("name", name); jsonObject.addProperty("x", x); jsonObject.addProperty("y", y); jsonObject.addProperty("method", method); jsonObject.addProperty("number", number); jsonObject.addProperty("message", message); System.out.println("jsonObject.toString() " + jsonObject.toString()); template.convertAndSend("/topic/greetings", jsonObject.toString()); return "goods"; } @MessageMapping("/hello") @SendTo("/topic/greetings") public String greeting(HelloMessage message) throws Exception { Thread.sleep(1000); // simulated delay System.out.println("Message " + message); return message.getName(); } \[/code\] SimpleMessageTemplate은 다음과 같이 정의되어 있습니다 \[code lang=java\] @Configuration @EnableWebSocketMessageBroker public class WebSocketConfig implements WebSocketMessageBrokerConfigurer { @Override public void configureMessageBroker(MessageBrokerRegistry config) { config.enableSimpleBroker("/topic"); config.setApplicationDestinationPrefixes("/app"); } @Override public void registerStompEndpoints(StompEndpointRegistry registry) { registry.addEndpoint("/gs-guide-websocket").withSockJS(); } } \[/code\] 클라이언트에서는 어떻게 받았을까요 \[code lang=javascript\] var stompClient = null; function setConnected(connected) { $("#connect").prop("disabled", connected); $("#disconnect").prop("disabled", !connected); if (connected) { $("#conversation").show(); } else { $("#conversation").hide(); } $("#greetings").html(""); } function connect() { var socket = new SockJS('/gs-guide-websocket'); stompClient = Stomp.over(socket); stompClient.connect({}, function (frame) { setConnected(true); console.log('Connected: ' + frame); stompClient.subscribe('/topic/greetings', function (greeting) { showGreeting(greeting.body); setNurikabe(JSON.parse(greeting.body)); }); }); } function disconnect() { if (stompClient !== null) { stompClient.disconnect(); } setConnected(false); console.log("Disconnected"); } function sendName() { stompClient.send("/app/hello", {}, JSON.stringify({'name': $("#name").val()})); } function showGreeting(message) { $("#greetings").append("<tr><td>" + message + "</td></tr>"); } function setNurikabe(message) { receiveObj = eval(message); if (receiveObj.method == 'create' ) { createMap(receiveObj.name, receiveObj.x , receiveObj.y); } else { appendItem(receiveObj.name, receiveObj.x , receiveObj.y, receiveObj.method, receiveObj.number); } } function createMap(id, width, height) { var rectSize = 25; var startPoint ={x:25, y:25}; $("#main-content").append("<canvas id=\\"canvas" + id + "\\" width='500px' height='500px'></canvas>") var c = document.getElementById("canvas" + id); var ctx = c.getContext("2d"); ctx.font="15px Arial"; for (i = 0 ; i < width; i++) { for (j = 0 ; j < height; j++) { ctx.rect(startPoint.x + (rectSize * i), startPoint.y + (rectSize * j), rectSize, rectSize); ctx.stroke(); } } } function appendItem(id, x, y, type, number) { var rectSize = 25; var startPoint ={x:25, y:25}; var c = document.getElementById("canvas" + id); var ctx = c.getContext("2d"); ctx.font="15px Arial"; if (type == 'number') { ctx.fillText(number,startPoint.x + (rectSize * x)- 16, startPoint.y + (rectSize * y)- 6); ctx.stroke(); } else if (type == 'block') { ctx.fillRect(startPoint.x + (rectSize * x), startPoint.y + (rectSize * y), rectSize, rectSize); ctx.stroke(); } else if (type == 'room') { ctx.fillText("o",startPoint.x + (rectSize * x)- 16, startPoint.y + (rectSize * y)- 6); ctx.stroke(); } } $(function () { $("form").on('submit', function (e) { e.preventDefault(); }); $( "#connect" ).click(function() { connect(); }); $( "#disconnect" ).click(function() { disconnect(); }); $( "#send" ).click(function() { sendName(); }); }); \[/code\] 코드 중간중간에 `var socket = new SockJS('/gs-guide-websocket');` 와 `stompClient.subscribe('/topic/greetings', function (greeting)`와 같은 url이 있습니다 해당 코드가 java코드에 어디에 있는지 확인하여 매핑되는지 확인하면 좋을거 같습니다

테스트
---

스프링 부트프로젝트를 실행시킵니다 \[code lang=text\] $ mvn spring-boot:run \[/code\] 저는 해커톤의 특성상(이라고 쓰고 귀찮아서 라고 읽어주세요) 시간이 부족하기 때문에 url을 떄렸습니다 방생성 : http://localhost:8080/create?name=A1&x=10&y=10&method=create number 마킹 : http://localhost:8080/putItem?name=A1&x=1&y=1&method=number&number=1 (1,1) 좌표에 숫자 1이 찍힙니다 ![이미지1](https://github.com/devahea/2018hackerton-viewer/blob/master/ex1.PNG?raw=true) block 마킹 : http://localhost:8080/putItem?name=A1&x=1&y=2&method=block (1,2) 좌표가 블록됩니다 (윽 해커톤땐 잘 됬었음요) ![이미지2](https://github.com/devahea/2018hackerton-viewer/blob/master/ex2.PNG?raw=true) room 마킹 : http://localhost:8080/putItem?name=A1&x=1&y=3&method=room (1,3) 좌표가 o로 마킹 됩니다 ![이미지2](https://github.com/devahea/2018hackerton-viewer/blob/master/ex3.PNG?raw=true)

마무리
===

아해 해커톤에서 누리카베를 실시간으로 보여주기위한 view 프로젝트를 진행했습니다 짧은 시간에 샘플 코드를 빨리 찾아서 빠르게 개발해볼 수 있었습니다