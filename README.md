<a name="top">
	
# RSIMon
RSI(상대 강도 지수) 모니터링 웹

</a>

## 목차 📄

#### [1. 목적](#link_goal)
#### [2. 요구사항 구현 여부](#link_rfp)
#### [3. 구현 방법](#link_impl)
#### [4. 테스트(JUnit)](#link_test_junit)
#### [5. RestAPI 문서](#link_doc_restapi)


</br>

<a name="link_goal">  
	
## 1. 목적 🏆
	
</a>

 - 사용자가 딱 보고 RSI 수치 현황을 직관적으로 파악할 수 있도록 하기 위함
 - 업비트 거래소에 거래되는 코인에 한함
 - 현재 업비트 RSI 기능은 코인 개별 제공이라 리스트 형태, 코인별 비교 등이 불가능함
 - finviz라는 사이트가 존재하지만 업비트 코인이 아니고 주식만 있다. 
 - 한국인을 위한 사이트를 만드는 것이 목적
 - 
 
[맨 위로 가기](#top)	

<a name="link_rfp">  
	
n## 2. 요구사항 구현 여부
	
</a>

|ReqNo|요구사항명|구현|
|------|---|:---:|
|Req-01|Java8, Spring Boot, Gradle, MariaDB, Thymeleaf 기술 사용.|O|
|Req-02|화면 설계서 작성|O|
|Req-03|아키텍처 작성 - 버전까지|O|
|Req-04|요구사항 작성 - RFP(범위, 일정)|O|
|Req-05|작업 일정 작성 - WBS(RFP 번호 매칭)|O|
|Req-06|DB 스키마 구성|O|
|Req-07|유지보수 계획서|O|
|Req-08|과거 데이터 조회 - 하루, 한시간 단위 조회 (새로 고침 불가능)|O|
|Req-10|민감정보(서버, DB 접속 계정) 등은 암호화된 상태로 저장|O|
|Req-11|AWS 클라우드 서버 이용(EC2, CentOS)|O|
|Req-11|접속자 수 , 트래픽 수 추적 가능한 기능 필요|O|
|Req-11|도메인 사용하여 24시간 언제, 어디서든 접속 가능|O|
|Req-11|RSI 홈페이지, 게시판 기능 MSA 구성으로 진행(이건 추후)|O|
|Req-11|배포는 자동 배포 도구 사용|O|
|Req-11|테스트는 테스트 도구 사용(JUnit5)|O|
|Req-11|모든 과정을 전부 기록(에러까지), 매일 한 일도 일별 기록|O|
|Req-11|에러 발생 시 조치 절차 계획서 작성(유지보수)|O|

|Req-11|모든 통신은 RestAPI 를 사용한다.|O|
|Req-11|python은 Flask 를 이용한다.|O|

<!-- ※Req-09에서 Swagger JWT 토큰 입력 부분 미구현 => Postman 테스트로 대체. -->

화면 설계 관련 요구 사항
|ReqNo|요구사항명|구현|
|------|---|:---:|
|Req-11|테이블 표 형태로 수치를 표현|O|
|Req-11|직관적인 수치가 가능|O|
|Req-11|새로고침 남은 시간 직관적으로 표시|O|
|Req-11|110여개 코인중 몇 개를 최대로 보여줄 지 정하기|O|
|Req-11|그래프 기능도 추후 고려|O|
|Req-11|상,하한 임계치 초과 수치에 대해 빨간, 파랑으로 표시(임계치 사용자 임의 설정 가능)|O|
|Req-11|코인 이름, RSI 수치에 대해 오름차순 , 내림차순 가능|O|



[맨 위로 가기](#top)	

<a name="link_impl">  
	
## 3. 구현 방법
	
</a>

### Installation
![스프링 초기 설정](https://user-images.githubusercontent.com/40817016/154221723-01283a95-9ca4-4bb8-abc7-ab3b5fc33f4f.png)

### 개발환경
* Spring boot 2.5.9 RELEASE 
(swagger 충돌 문제로 2.6.3에서 다운그레이드)
* Java 8
* JPA & H2
* lombok
* gradle

### Library
```
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	runtimeOnly 'com.h2database:h2'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	
	// SWAGGER
	// implementation group: 'io.springfox', name: 'springfox-boot-starter', version: '2.9.2'
	implementation group: 'io.springfox', name: 'springfox-swagger2', version: '2.9.2'
	implementation group: 'io.springfox', name: 'springfox-swagger-ui', version: '2.9.2'

	// thymeleaf
    	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
    	implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect'
    
	// ETC
	implementation group: 'org.apache.httpcomponents', name: 'httpclient', version: '4.5.13'
	implementation group: 'org.json', name: 'json', version: '20211205'
	implementation group: 'com.googlecode.json-simple', name: 'json-simple', version: '1.1.1'
	implementation group: 'org.modelmapper', name: 'modelmapper', version: '2.3.8'    
	implementation group: 'commons-fileupload', name: 'commons-fileupload', version: '1.4'
	implementation group: 'commons-io', name: 'commons-io', version: '2.6'

}
```

### ERD
![image](https://user-images.githubusercontent.com/40817016/154811340-c995f5ee-9af3-44d6-9afb-94d5a172d04a.png)

※COL_DATE(날짜), COL_HOUR(시간) 복합키 설정하여 중복 방지 </br>
※광고 통계 정보 초기 데이터 Insert

```
INSERT INTO AD_STATISTICS 
(COL_DATE, COL_HOUR, REQ_CNT, RES_CNT, CTR_CNT) 
VALUES 
('2022-02-16', '10', 7,5,3), 
('2022-02-16', '11', 11,12,45),
('2022-02-16', '12', 13,24,39),
('2022-02-16', '13', 20,34,30);
```
</br>

### H2 URL
http://localhost:8080/h2-console/ </br>
![image](https://user-images.githubusercontent.com/40817016/154812524-74e2df16-bc71-48dd-a840-75415793b166.png) </br>


### Swagger URL
http://localhost:8080/swagger-ui.html#/ </br>
![image](https://user-images.githubusercontent.com/40817016/154833471-f0a0c604-a925-4681-aec8-07ddbcddb190.png)


### 참고
※스웨거 참고 블로그: https://antstudy.tistory.com/251 </br>
※File Upload 참고: https://stackoverflow.com/questions/49845355/spring-boot-controller-upload-multipart-and-json-to-dto

[맨 위로 가기](#top)
	
<a name="link_test_junit">  
	
## 4. 테스트(JUnit) 🏆
	
</a>

##### 4-1. 테스트 실행

 > /project-recruit-fsn/src/test/java/com/project/fsn/controller/AdStatisticsControllerTest.java 실행

##### 4-2. 테스트 과정

1. 특정 날짜 합계 광고 통계 데이터 조회
2. 특정 시간 광고 통계 데이터 조회
3. 광고 통계 파일 업로드
4. 특정 날짜 합계 광고 통계 데이터 조회

##### 4-3. 테스트 결과

> 1. 특정 날짜 합계 광고 통계 데이터 조회

![image](https://user-images.githubusercontent.com/40817016/154831939-0f2e4af3-a5c4-428e-9eee-ce8a9f564113.png)

</br>

- 파라미터 </br>
` 날짜 : 2022-02-16  `

- 응답 </br>
` 광고 요청 수 합계 : 51, 응답 수 : 75, 클릭 수 : 117 `

</br>

> 2. 특정 시간 광고 통계 데이터 조회
- 파라미터 </br>
` 날짜 : 2022-02-16 , 시간 : 12 `

- 응답 </br>
` 광고 요청 수 : 13, 응답 수 : 24, 클릭 수 : 39 `

</br>

> 3. 광고 통계 파일 업로드

</br>

- 업로드 Json 파일

![image](https://user-images.githubusercontent.com/40817016/154831827-a1691ab6-2b03-4e0e-b53a-ca00ad709ed0.png)

</br>

- 업로드 결과

![image](https://user-images.githubusercontent.com/40817016/154837922-2c50ba1c-9bbe-49a6-bc42-7b5c99e10ad2.png)
</br>
`초기 데이터에 없는 시간대 14시, 15시의 데이터는 Insert 된 것을 확인할 수 있다. ` </br>
`초기 데이터와 중복되는 시간대 12시, 13시의 데이터는 Update 된 것을 확인할 수 있다. `

</br>

> 4. 특정 날짜 합계 광고 통계 데이터 조회

- 응답 </br>
` 광고 요청 수 : 54, 응답 수 : 76, 클릭 수 : 113 `

</br>

> JUnit 테스트 결과

</br>

![image](https://user-images.githubusercontent.com/40817016/154837999-62a7bee3-4ad2-46d5-90cf-114a0289638b.png)


<a name="link_doc_restapi">  
	
## 5. RestAPI 문서

</a>

https://drive.google.com/drive/folders/1s7OsgB1YP-E3SwpN4COBJ-WvUW6CZKxc

