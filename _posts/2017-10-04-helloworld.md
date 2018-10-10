---
layout: post
title:  GraphQL
date:   "2018-9-02 11:10:59"
author: eunkyung
categories: Lifehack
tag: graphql
cover:  "/assets/graphql.png"
---

# 1. GraphQL에 관하여
## 1.1. [**GraphQL**](https://graphql.org/learn/)이란?
페이스북이 2012년에 개발하여 2015년에 공개적으로 발표된 데이터 질의어. 

## 1.2. GraphQL의 장점_단점 : 
### 1.2.1. 장점
        1. REST 및 부속 웹서비스 아키텍쳐를 대체할 수 있다
        2. 클라이언트는 필요한 데이터의 구조를 지정할 수 있으며, 서버는 정확히 동일한 구조로 데이터를 반환한다. 
        3. 클라이언트 개발자가 어떤 데이터가 필요한 지 명시할 수 있게 해 주는 강타입 언어이다. 이러한 구조를 통해 불필요한 데이터를 받게 되거나 필요한 데이터를 받지 못하는 문제를 피할 수 있다.
### 1.2.2. 단점
        1. 클라이언트 입장에서 쿼리문 작성하는데 불편함이 있다.

# 2. GraphQL 기본 setting 가이드
## 2.1. [**Apollo GraphQL**](https://www.apollographql.com/docs/)
Apollo GraphQL은 GraphQL API를 가능한 한 쉽게 사용하게 해주는 라이브러리. GraphQL을 안드로이드에서 사용하려면 Apollo GraphQL이라는 client tool이 필요하다.(지금까지 안드로이드 용 GraphQL 클라이언트는 하나 밖에 없음. graphQL공식 문서에도 안드로이드 가이드로 apollo graphql을 링크를 준다.) 


### 2.1.1 build.gradle 세팅하기 (현재 최신 버전 : 1.0.0-alpha2)
    dependencies {
      classpath 'com.apollographql.apollo:apollo-gradle-plugin:x.y.z'
    }

### 2.1.2 app build gradle 세팅하기
    apply plugin: 'com.apollographql.android'
    implementation 'com.apollographql.apollo:apollo-runtime:1.0.0-alpha2'

### 2.1.3 서버로부터 스키마 가져오기

Apollo GraphQL의 경우 schema.json 파일이 있어야 안드로이드 개발자가 작성한 쿼리문을 컴파일 할 수 있음. 

1. npm을 설치한다. (https://www.npmjs.com/get-npm  해당사이트의 가이드를 따르면 됨. node.js설치도 해야함.) 

2. apollo 및 apollo-codegen을 설치한다.
```
(개인의 컴퓨터 세팅에 따라 설치 접근 권한이 걸려있는 분들은 앞에 sudo를 붙여주면 됨.)
npm install -g apollo
npm install apollo-codegen
```

3. shema.json 파일을 프로젝트 서버로 부터 가져와 파일을 생성해 준다.
```
apollo-codegen introspect-schema http://yourserver/graphql --output 프로젝트경로/schema.json
(apollo schema:download http://yourserver/graphql 프로젝트경로/schema.json 를 사용하면 추후에 생성되는 Schema.json파일이 컴파일시 Getting “error: GraphQL schema file should contain a valid GraphQL introspection query result” after apollo schema:download 라는 에러를 발생하는 이슈가 있음.)
```

서버에서 필드명 혹은 api변경시 최신 스키마를 shell명령어를 통해 업데이트해줘야 하므로 app내에 스크립트 파일을 만듬.

[그림]

해당 스크립트를 실행하면 프로젝트내에 shema.json 파일이 생성됨.

테일리와 같이 쉘파일을 만들어 구동할 경우 아래와 같이 참고하시면 됩니다.
```
apollo-codegen introspect-schema http://주소/graphql --output ./app/src/main/graphql/com.navercorp.android.pet/schema.json
(apollo schema:download --endpoint=http://주소/graphql ./app/src/main/graphql/com.navercorp.android.pet/schema.json)
```

### 2.1.4 서버와 연결
쿼리문을 요청하고 응답값을 편하게 받아오기 위해 Apollo GraphQL의 ApolloClient를 이용한다. 
(ApolloClient 이용하지 않았을 때 발생하는 개발자의 고통은 어마어마하다. 쿼리를 작성할 때 String query = "{\"query\":\"{getPetInfo(limit:" + limit + "){pageInfo{" + PAGE_INFO_PARAMS + "},data{" + PET_PARAMS + "}}}\"}"; RequestBody body = RequestBody.create(MediaType.parse("text/plain"), query)
이런식으로 개발해야함.)
이하 고통을 생략하고

```

 object PetApolloClient {

    private val BASE_URL: String = "http://주소/graphql"
    private lateinit var petApolloClient: ApolloClient

    fun getPetApolloClient(): ApolloClient {
        val httpClient = OkHttpClient.Builder()
                .addInterceptor(HeaderInterceptor())
                .build()

        petApolloClient = ApolloClient.builder()
                .serverUrl(BASE_URL)
                .okHttpClient(httpClient)
                .build()

        return petApolloClient
    }
 }

```
PetApolloClient라는 유틸성 클래스를 통해 graphql 용으로 만들어 놓은 프로젝트 서버와 연결 한다.

### 2.1.5 쿼리문
GraphQL 요청에는 query (analogue of GET), mutation (analogue of POST, PUT) 및 subscription의 세 가지 유형이 있다.
요청 구축을 위해 .graphql 확장명을 가진 파일을 만들어야한다. 이 파일에는 모든 쿼리, 변이, 구독 및 조각 (요청에 사용할 수있는 필드 집합이있는 재사용 된 블록)이 모여 있다. examplequeries.graphql과 같은 이름을 생각해 볼 수 있다.(각 사용처에 맞게 쿼리 문서를 나눠서 하거나 한 파일에 여러 쿼리를 넣어도 됨.) 그러나 java 폴더와 동일한 레벨의 src / main / graphq 폴더에 파일을 저장해야한다.

[그림]

다음과 같이 폴더 안에 .graphql 파일을 생성하여 쿼리문을 작성해 준다. 
일반 apollo graphql은 .graphql 파일 내에 자동완성 기능이 제공되지 않아 http://주소/graphiql  서버에서 제공해주는 **GraphiQL**을 이용하여 쿼리문을 긇어 복사하여 붙여주는 형식으로 코딩을 하게 된다. Graph**i**QL(i가 들어가있음)은 swagger처럼 쿼리를 자동완성으로 만들어 볼수 있고 콜을 보내면 결과값을 받을 수 있는 툴을 말함.

******


# 3. 편한 Query 작성을 위한 setting 가이드

위에 설명(2번)까지가 Apollo GraphQL을 이용한 세팅 및 쿼리문 작성이었다. 보면 앱 개발자 입장에서 쿼리문을 작성하기란 쉽지 않다. 왜냐하면 어떤 api들이 있는지 그리고 해당 api의 응답 필드에 대한 정보가 없기 때문. (물론 shema.json에 정보가 나와있지만 코드간에 자동화 연결이 되어있지도 않고 json 파일을 눈으로 확인하기도 쉽지 않다.)
그래서 보통 지금까지 안드로이드 개발자들은 서버에서 만들어준 GraphiQL이란(swagger와 비슷한 기능) 링크를 통해 쿼리문을 작성하고 해당 쿼리문을 복사해서 넣는 구조로 개발해야하는 고통이 있음.

그래서 안드로이드 스튜디오에서 바로 쿼리문을 자동화 연결로 개발할 수 있도록
하나 더 준비한 플러그인 세팅 방법이 있다. 물론 공식적인 안정화 버전을 아니지만 원하는 api와 필드를 바로 알수 있도록 shema.json 이 아닌 schema.graphql이란 확장자 파일을 만들어 api별 필드를 연결해서 사용할수 있게 하는 구조이다.

## 3.1. Android Studio에서 GraphQL Query문 자동화 편집에 대한 문제 상황

    1. js-graphql이 JetBrain사 IDE에서 graphql 서포팅 해주는 플러그인 (https://plugins.jetbrains.com/plugin/8097-js-graphql) 문제는 플러그인이 1.x 버전에선 Android Studio를 지원하지 않음
    2. 현재 2.x 버전이 개발 단계인데, 2.0.0-alpha-2 버전 부터 Android Studio도 지원하기 시작함 (https://github.com/jimkyndemeyer/js-graphql-intellij-plugin/issues/164)
    3. 이를 설치하기 위해선 다음을 참고(https://github.com/jimkyndemeyer/js-graphql-intellij-plugin/releases/tag/2.0.0-alpha-2)
    4. 스키마를 제대로 가져오지 못하는 이슈가 있어서 graphql-cli로 직접 schema.graphql 파일을 생성해 줘야한다.(https://github.com/graphql-cli/graphql-cli)

## 3.2. Android Studio에서도 GrpahiQL처럼 편하게 쿼리하기

### 3.2.1 intellij-plugin setting
"Settings" > "Plugins" > "Browse repositories..." > "Manage repositories..."> add

    https://github.com/jimkyndemeyer/js-graphql-intellij-plugin/raw/v2/alpha-releases/updatePlugins.xml
    
[그림]

### 3.2.3 graphQL config file setting
    https://github.com/prisma/graphql-config
    

[그림]

[그림]
왼쪽 화살표를 클릭하면 schema.graphql 파일이 생성됨.

[그림]

schema.json은 Apollo graphql 라이브러리가 사용하는 파일이고 schema.graphql 파일은 안드로이드 개발자가 쿼리문을 자동완성으로 쉽게 쓰기 위한 파일이다 동일한 내용이지만 확장자가 달라 내부 구조가 다르다. 
schema.json은 Apollo graphql 라이브러리에서 사용하므로 빌드가 함께 이루어져야하는 파일이지만 schema.graphql는 쿼리문을 작성할때 필요한 파일로 빌드할때 필요한 파일이 아니다. 그리고 두가지를 모두 빌드를 하면 android studio에서 validation 에러가 떨어지며 빌드 에러가 발생한다.
그러므로 schema.graphql 파일은 빌드시 제외 되도록 res 폴더에 보관하는 것으로 한다.

[그림]

쿼리 예시문 (getMySelfAndMyHistoryFeed.graphql는 생성한 예문 - 각 필요에 따라 파일을 생성하여 작성하면 됨)

[그림]

apollo graphQL 라이브러리만 사용했을때(2.1.5 캡처사진 참고)에는 같은 getMySelfAndMyHistoryFeed.graphql 파일이지만 api및 필드가 자동완성이 되지 않고 마지 텍스트 파일에 코딩을 하는것과 마찬가지의 불편함이 있었는데 위에 쿼리문 캡체사진을 보면 색깔별 분리와 각 api와 해당 필드가 자동완성이 되는 것을 확인 할 수 있다.




[그림]
apolloClient를 이용해 작성한 쿼리를 실행하고 원하는 결과값만 얻어온다.

* Schema.json과 Schema.graphql 은 자동 생성 파일로 이부분은 수정 변경하지 않도록 합니다.(Schema.graphql에서 Long자료형이 빨갛게 표시된 부분은 무시해도 됩니다. 쿼리 자동완성을 위한 파일이기때문에 컴파일에 무관.) 단, ApolloClient는 스칼라 자료형을 기반으로 하기때문에 Long type을 object로 가져옵니다. 그래서 이에 맞게 형변환을 클라이언트에서 해줘야합니다. 
[공식문서 3.1.1.1Int 섹션 Note 참조](http://facebook.github.io/graphql/October2016/#sec-Int)

addCustomTypeAdapter라는 함수를 통해 받아올때(decode) 형변환을 해주고 값을 보낼때(encode) 한번 더 형변환을 해주게 됩니다.
[그림]

결과 응답값 역시 Apollo에서 Long을 Object로 주기 때문에 지져분하지만 두번의 파싱을 통해 Long값을 가져 옵니다. object라 표시됨은 보통 스칼라에 없는 자료형이 올때 인데 테일리의 경우 Schema.graphql에서 보다시피 인식 되지 않은 자료형은 Long값뿐이라 .toString().toLong을 진행해 줍니다. 단 다른 프로젝트의 경우 자바에서는 제공되지만 스칼라의 자료형이 제공되지 않은 double이라던지 기타 자료형의 경우 각 상황에 맞게 커스텀을 진행해줘야 합니다.
[그림]

물론, Apollo-graphql의 1.1.0-alpha버전 노트에 (#901)이슈로 "Fix mapping of GraphQl Int type to Java int type instead of Long" 
해결한 것으로 나오지만 addCustomTypeAdapter를 사용하지 않고 바로 사용하는 경우가 없는 것으로 보임(바로 Long type을 사용하시는 분은 연락주세요!)
https://github.com/graphql-java/graphql-java/pull/94#issuecomment-190008636


작성자 : 남궁은경 (궁금하신 점이 있으시거나 혹시 잘못된 정보가 있으면 언제든 연락 주세요)
