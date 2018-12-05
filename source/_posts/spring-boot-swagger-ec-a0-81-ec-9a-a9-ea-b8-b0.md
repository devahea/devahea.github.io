---
title: spring boot swagger 적용기
tags:
  - spring boot
  - swagger
url: 43.html
id: 43
categories:
  - 미분류
date: 2017-01-18 20:34:55
---

spring boot 프로젝트에 swagger를 적용했습니다

rest api를 문서화 하기 위해 어떤걸 쓸지 고민하는 중 swagger를 써보기로 했습니다

문서화에 있어 필수적으로 고민한것들이 있습니다

1.  문서화가 자동으로 되어야 한다
2.  description을 잘 넣을 수 있어야 한다
3.  빨리 설치하고 필요없으면 삭제를 빠르게

1번이 무조건 핵심인데 찾던것들중 어떤건 junit테스트를 해야 만들어지고(우린 왜 unit test를 안하나) 어떤건 node를 설치해야 했습니다.

![스크린샷 2016-08-05 오전 10.53.06.png](https://ahea.files.wordpress.com/2017/01/43_hxf5mftuj6.png)

<dependency>

        <groupId>io.springfox</groupId>

        <artifactId>springfox-swagger-ui</artifactId>

        <version>2.2.2</version>

        </dependency>

<dependency>

    <groupId>io.springfox</groupId>

    <artifactId>springfox-swagger2</artifactId>

    <version>2.2.2</version>

</dependency>

swagger는 swagger-core와 swagger-ui로 나뉘어 있습니다

ui를 넣지 않으면 json으로만 데이터를 봐야 하기 때문에 swagger-ui를 넣으면 됩니다

swagger-ui는 webjar로 배포된다고 하네요

javaConfig로 swagger를 설정합니다

 Docket을 빈으로 등록해야 합니다

우리는 특별히 basePackage를 넣어줬습니다 저것을 하지 않으면 spring에 있는 앵간한 mapping이 다 들어갑니다 예를 들면 oauth 인증 url이라던가 말이죠

![스크린샷 2016-08-05 오전 11.13.50.png](https://ahea.files.wordpress.com/2017/01/43_brlay-y_ih.png)

@Configuration

@EnableSwagger2

@EnableAutoConfiguration

public class SwaggerConfig {

            @Bean

            public Docket swaggerSpringMvcPlugin() {

                    return new Docket(DocumentationType.SWAGGER_2)          

                .select()                                      

                .apis(RequestHandlerSelectors.basePackage("org.ahea.blindinterview"))

                .build();

            }

}

/swagger-ui.html 로 호출하면 결과를 확인할수 있습니다.

하지만 spring-security가 적용되어 있는 경우 시큐리티에 막혀 호출이 되지 않습니다

그런 경우 WebSecurityConfigurerAdapter를 이용하여 uri를 ignoring해줍니다

![스크린샷 2016-08-05 오전 11.20.16.png](https://ahea.files.wordpress.com/2017/01/43_xtpwaqywwd.png)

@Configuration

@EnableWebMvc

public class MyWebSecurity extends WebSecurityConfigurerAdapter {

        @Override

        protected void configure(HttpSecurity http) throws Exception {

                http.authorizeRequests()

                                .antMatchers(HttpMethod.OPTIONS, "/oauth/token").permitAll();

        }

        @Override

        public void configure(WebSecurity web) throws Exception {

                web.ignoring().antMatchers("/v2/api-docs", "/configuration/ui",

                                "/swagger-resources", "/configuration/security",

                                "/swagger-ui.html", "/webjars/**","/swagger/**");

        }

}

설치는 끝났습니다

api doc을 만들어봅시다

![스크린샷 2016-08-05 오전 11.25.09.png](https://ahea.files.wordpress.com/2017/01/43_gqblfdmnxu.png)

@ApiOperation(value = "헬로우 테스트", httpMethod = "GET", notes = "헬로우 테스트입니다")

        @ApiResponses(value = {

            @ApiResponse(code = 400, message = "Invalid input:..."),

            @ApiResponse(code = 200, message = "Ok" )

            })  

        @RequestMapping(value="/hello", method=RequestMethod.POST )

        public String hello(@ApiParam(value = "키값", required = true, defaultValue = "기본값") String value) {

                return "Blind Interview Server Running\\n";

        }

Requstmapping이 걸린 메소드에 저런것들을 넣을수 있습니다

자 만들어진 것을 보죠

![스크린샷 2016-08-05 오전 11.31.23.png](https://ahea.files.wordpress.com/2017/01/43_f0hngulaux.png)

이전에 더 깔끔하고 좋은 문서화 도구가 있었는데 이름을 도저히 모르겠습니다. 그래도 이정도면 훌륭합니다. 오 웹에서 테스트도 할수 있군요 try it out 버튼이 있군요

눌러보면

![스크린샷 2016-08-05 오전 11.34.57.png](https://ahea.files.wordpress.com/2017/01/43_olyvgtrz8l.png)

권한이 없다고 나오네요

이 상황은 spring security oauth때문에 일어나는 문제입니다

헤더에 token값을 넣어줘야 하지요

security oauth를 적용하지 않았다면 결과가 잘 나올껍니다

헤더값을 넣을수 있도록 해보겠습니다

![스크린샷 2016-08-05 오전 11.37.36.png](https://ahea.files.wordpress.com/2017/01/43_uvjaqeo6qj.png)

@ApiImplicitParams({

        @ApiImplicitParam(name = "Authorization", value = "authorization header", required = true,

                dataType = "string", paramType = "header", defaultValue = "bearer cbbb1a6e-8614-4a4d-a967-b0a42924e7ca")

        })

헤더에 토큰을 넣을수 있도록 했습니다

우선 토큰값은 강제로 넣어놨습니다

결과를 다시 보면

![스크린샷 2016-08-05 오전 11.59.03.png](https://ahea.files.wordpress.com/2017/01/43_0gqnafutg2.png)

Authorization 헤더값을 넣을수 있게 되었습니다

토큰 스토어에서 토큰을 받아올순 있지만 어떻게 처리할지 고민중이네요 우선 토큰 직접 발급 받으셔서 하세요

정상적인 토큰을 넣어주고 결과를 확인하면

![스크린샷 2016-08-05 오후 12.01.29.png](https://ahea.files.wordpress.com/2017/01/43_dp2qirz-rn.png)

ResponseBody에 결과가 잘 나오는 것을 확인 할수 있습니다

이정도면 괜찮지만 많이 아쉽습니다

1.  코드에 annotations으로 넣어야 하는 문제
2.  oauth 처리 문제 (이거로 어제 밤샐뻔했네요)
3.  깔끔한 UI 필요 (이정도면 훌륭하지만 분명 저는 전설속에 나오는 문서를 본적이 있습니다 더 좋은 문서화가 가능한데 말이죠….)