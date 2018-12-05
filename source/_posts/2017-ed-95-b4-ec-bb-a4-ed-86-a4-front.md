---
title: 2017 해커톤 – Front
url: 1598.html
id: 1598
categories:
  - 미분류
date: 2017-11-22 13:59:55
tags:
---

안녕하세요 아해 팀 new멤버 김민수입니다. 아해 팀에 들어와서 처음으로 한 활동이 해커톤이었고, 생애 첫 해커톤인 까닭에 긴장과 설레는 마음을 가지고 임했었네요. 이번 해커톤을 하기 전 기대했던 것은 관심 있던 기술들을 써보고, 다른 사람들의 코드를 봄으로서 안목을 넓히자는 것이었습니다. 저는 백엔드의 [Export](https://ahea.wordpress.com/2017/10/12/2017-%ED%95%B4%EC%BB%A4%ED%86%A4-1%EC%9E%A5-intro-export/) 부분과 프론트엔드를 맡아서 프로젝트를 진행했습니다. 프론트엔드는 Angular4를 사용했습니다. 지금부터 프론트엔드 작업내용과 이슈를 공유하겠습니다. 아래는 저희 프로젝트 화면입니다. ![전체화면](https://ahea.files.wordpress.com/2017/11/eca084ecb2b4ed9994eba9b4.png) 보시는 바와 같이 로직은 Field Name과 Type을 필요한 만큼 동적으로 생성하여 값을 넣고, 원하는 추출 타입과 수를 정한 뒤 Preview를 클릭하면 서버에서 데이터를 가져와 보여줍니다. 먼저, Angular는 [Component-Based Development](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%8F%AC%EB%84%8C%ED%8A%B8_%EA%B8%B0%EB%B0%98_%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B3%B5%ED%95%99)(이하 CBD)이기 때문에 Component로 나눠서 생각을 해보겠습니다. ![component](https://ahea.files.wordpress.com/2017/11/component.png) 총 4개의 Component로 구성되어있습니다.

*   main : 전체와 Header를 나타내고 있고, 최상위 Component입니다.
*   form : Field Name, Type, rows, exportType의 값을 서버로 전송하는 역할을 합니다.
*   fields-wrapper : field들을 동적으로 생성 및 삭제하는 역할을 합니다.
*   field : Field Name과 Type 값을 가지고 있습니다. 

제가 맡은 Field와 이것을 감싸고 있는 Wrapper에 대해 소개 드리겠습니다. ![wrapper](https://ahea.files.wordpress.com/2017/11/wrapper.png) 로직에서도 Component의 구조가 명확히 나타나네요. wrapper 안의 field의 구조로 말이죠. field를 생성, 삭제하는 로직을 설명하겠습니다. <ng-container>태그를 보시면 "rows"라는 Array를 루프 돌면서 field를 생성하는 역할을 하고 있습니다. 디폴트로 rows의 개수가 1개이기 때문에 최초에는 1개의 field만 생성됩니다. field를 생성(Add another field를 클릭) 하기 위해서는 rows.push()를 통해 Array에 값만 하나 넣어주면 됩니다. 삭제하기 위해서는 Array에서 빼주기만 하면 되겠죠? 간단한 로직이지만 이것을 통하여 CBD의 특성을 이해하게 되었고, CBD 기반 Angular의 강력함을 알았습니다. 만약, jQuery로 짰을 경우 HTML을 스크립트에서 만들고, 해당하는 selector를 찾아서 append(),  remove() 하고, 값이 존재하면 상황에 맞게 핸들링을 해줘야 합니다. 그러나 CBD를 활용하여 Component만 설계해 놓으면 추가, 삭제는 Array의 push(), splice()로 값이 필요할 경우는 \[ \]를 통한 바인딩으로 간단하게 처리할 수 있습니다. 그러던 중 잘 되어가던 프로젝트에 갑자기 기술적인 큰 이슈가 생겼습니다.  자식 컴포넌트에서 부모 컴포넌트로 데이터 전달하는 부분인데요. 4개의 Component 모두가 부모/자식 관계를 유지하고 있었고, 서버에 값들을 전달하기 위해서는 데이터를 부모 Component인 form으로 전달을 해줘야 합니다. 그러나 해당 부분에 대해 잘 알지도 못하고, 조급한 탓에 약 밤 11시부터 새벽 3시까지 고생을 하며  구현을 했습니다. 구현을 하긴 했지만  \[(ngModel)\] 양방향 바인딩과 @Input을 이용하여 억지로 구현을 했고, 급기야 잘 나눠진 부모/자식 Component를 합치기까지 했습니다. 최근 Angular를 할 기회가 생겨 위의 문제에 대해 해결방안을 찾아보니 [EventEmitter](https://angular.io/api/core/EventEmitter)를 이용하면 간단하게 해결할 수 있는 문제라는 것을 알게 되었습니다. 이번 해커톤을 통해 많은 것을 배우고 느꼈는데요.

1.  조급하면 시선이 좁아지니, 여유를 가져야 한다.
2.  알지 못하거나, 새로운 기술을 사용함으로써 다양한 안목이 생길 수 있다.
3.  무작정 코딩하기보다는 클래스의 역할과 책임을 정의하는 설계를 통해  OOP를 잘 활용해야 한다.

이런 좋은 기회와 경험을 준 아해 팀원 분들께 감사의 말씀을 드립니다.