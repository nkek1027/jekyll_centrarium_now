## React Native 실전: Hello World

#### 루트 컴포넌트 등록
```
AppRegistry.registerComponent(appName, () => FoodHome);
```
앱 이름이 StudyRN 이고 FoodHome를 루트 컴포넌트로 설정하라고 리액트 네이티브에게 지시한다. AppRegistry.registerComponent의 호출은 앱마다 한 번만 하면 된다. 그외에 모든 컴포넌트는 FoodHome에 포함될 자식 컴포넌트가 되기 때문.

#### React를 임포트 하는 이유
리액트 네이티브 패키저는 ES2015나 ES2016 코드를 ES5로 트랜스파일한다. 이는 JSX코드에 있어서도 마찬가지이다.  <Text>Hello World</Text>는 자바스크립트 표현식이 아니다. 리엑트 네이티브 패키저는 바벨을 이용해 이를 JSX 코드를 다음과 같이 트랜스파일한다.
```
return React.createElement(Text, null, 'Hello World');
```
보는 바와 같이 바벨은 자동으로 JSX 코드를 React.createElement 구문으로 변환한다. 이게 바로 JSX를 사용하는 소스코드에서 항상 React 객체를 임포트해야하는 이유이다.
-> 요약 : 리액트패키저가 트랜스파일할때 React.createElement와 같이 React 객체를 직접사용하기 때문에 필요.

#### 디버깅 하는 방법
리액트 네이티브 앱을 디버깅할 때, 그 앱은 프록시 모드(proxy mode)로 구동된다. 이는 앱의 자바스크립트가 디바이스나 시뮬레이터의 자바스크립트코어 엔진이 아닌, 크롬 V8 자바스크립트 엔진에서 실행된다는 의미다. 그런 다음 리액트 네이티브 패키저는 앱과 크롬 사이의 통신을 웹 소캣을 사용해 중개한다.
방법 : 크롬디버거를 이용해야 함. 
1. 앱의 옵션을 활성(command + d)하여 Debug JS Remotely를 선택
2. 크롬의 새탭이 나타난다. 크롬에서 command + option + j를 눌러 크롬 개발자 도구의 콘솔을 연다.
3. sources 탭을 선택하여 해당 js파일을 열어 breakpoint를 설정한다.
