# 202130113 노형진
## 2025-03-20 3주차
### React Project의 구조 
my-project: project 이름
node_modules/: 프로젝트에 설치된 모든 패키지들이 저장되는 폴더, npm install하면 여기에 저장됨
public/ 정적 파일을 저장   
  &nbsp;&nbsp;&nbsp;&nbsp;favicon.ico:브라우저 탭에 표시되는 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;index.html: React 앱이 마운트되는 html 파일 div id="root"가 포함   
  &nbsp;&nbsp;&nbsp;&nbsp;logo192.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;logo512.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;manifest.json: PWA 관련 설정 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;robots.txt: 검색 엔진 크롤러의 접근을 제어하는 파일   
src/: React 앱의 주요 코드가 위치하는 폴더   
  &nbsp;&nbsp;&nbsp;&nbsp;App.css: App.js에 적용되는 스타일   
  &nbsp;&nbsp;&nbsp;&nbsp;App.js: 메인 컴포넌트   
  &nbsp;&nbsp;&nbsp;&nbsp;App.test.js: Jest를 사용한 기본 테스트 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;index.css: 전역 스타일   
 &nbsp;&nbsp;&nbsp;&nbsp; index.js: React 앱의 진입점. ReactDOM.createRoot를 사용하여 App.js를 렌더링함       
  &nbsp;&nbsp;&nbsp;&nbsp;logo.svg: 기본 로고 이미지   
 &nbsp;&nbsp;&nbsp;&nbsp; reportWebVitals.js: 성능 측정 관련 코드   
 &nbsp;&nbsp;&nbsp;&nbsp; setupTests.js: 테스트 환경 설정 파일   
.gitignore: Git에 추가하지 않을 파일 목록을 정의   
package-lock.json: 프로젝트에 설치된 정확한 패키지 버전과 의존성 정보를 기록    
package.json: 패키지(라이브러리)와 프로젝트 정보를 관리하는 파일    
README.md:    
&nbsp;&nbsp;&nbsp;&nbsp;  프로젝트 개요: 프로젝트가 무엇을 하는지 간략히 설명    
&nbsp;&nbsp;&nbsp;&nbsp;  설치 방법: 프로젝트를 실행하려면 어떻게 해야 하는지, 필요한 환경은 무엇인지 등    

#### node_modules/
&nbsp;&nbsp;&nbsp;&nbsp;초기 node_module 및 새로 설치하는 패키지가 저장됨. 프로젝트에서 필요한 라이브러리들을 로컬에 저장.
##### node_modules/ 폴더를 Git에 포함하지 않는 이유
폴더 크기가 너무 크고, 패키지마다 버전이 다를 수 있음   
package.json과 package-lock.json(또는 yarn.lock)만 있으면 npm install로 다시 설치할 수 있음   
그래서 .gitignore 파일에 아래처럼 추가해서 node_modules/ 폴더를 제외하는 게 일반적   
#### src/
React 프로젝트의 주요 코드가 위치하는 디렉토리   
개발 하면서 대부분의 작업이 이루어짐

##### &nbsp;&nbsp;&nbsp;&nbsp; App.js
메인 컴포넌트로 필요한 서브 컴포넌트를 모아 관리   
출력을 위해서 index.js로 전달
##### &nbsp;&nbsp;&nbsp;&nbsp; App.css
App.js에 적용되는 스타일   
일반적으로 App.js 컴포넌트의 스타일을 정의
##### &nbsp;&nbsp;&nbsp;&nbsp; index.css
index.css는 React 애플리케이션의 전역(Global) 스타일을 설정하는 CSS 파일   
##### &nbsp;&nbsp;&nbsp;&nbsp; index.js
index.js는 React 애플리케이션의 시작점(entry point) 역할   
즉, React 앱을 실행할 때 가장 먼저 실행되는 파일   

### 의존성 관리와 package.json
#### 의존성?
의존성(Dependencies) 이란, 프로젝트가 실행되기 위해 필요한 외부 라이브러리 또는 모듈에 의존하여 동작하는 관계   
예시로 React 프로젝트에서는 react, react-dom 같은 라이브러리가 필요하고, Express 서버에서는 express, mongoose 같은 라이브러리가 필요   
따라서 이러한 의존성들을 체계적으로 관리할 필요가 있음, 이것이 의존성 관리(Dependency Management)   
#### package.json?
package.json은 Node.js 프로젝트의 설정 파일로, 프로젝트의 정보와 함께 사용하는 의존성 목록을 저장하는 역할   
package.json 주요 역할
1. 프로젝트의 이름(name), 버전(version), 설명(description) 등의 정보를 저장   
2. 프로젝트에서 사용 중인 의존성(Dependencies) 목록을 저장   
3. npm 또는 yarn 명령어 실행 시 사용할 스크립트(Scripts) 명령어 정의


<hr>
## 2025-03-13 2주차
## 알아두어야 할 명령어

ls -al : ls는 'list' 현재 디렉토리의 파일과 폴더 목록을 출력, -al에서 a는 'all'으로 숨김 파일도 보여줌, l은 'long format'으로 파일의 자세한 정보(권한, 소유자, 크기, 수정 날짜 등)를 표시   
rm -rf : rm는 'remove' 파일이나 폴더를 삭제, -rf에서 r은 'recursive'으로 디렉토리와 그 안의 파일들을 재귀적으로 삭제, f는 'force'으로 강제 삭제를 의미   
git init : 새로운 Git 저장소(Repository)를 생성하기, 현재 디렉토리를 Git으로 관리할 수 있게 하는 명령어   

예시
$ mkdir my_project   
$ cd my_project   
$ git init   
Initialized empty Git repository in /path/to/my_project/.git/   

mkdir은 'Make Directory', 디렉토리를 만드는 명령어 my_project라는 디렉토리를 생성   
cd는 'Change Directory', 터미널에서 현재 작업 중인 디렉토리를 변경   
초기화하려면 git init, 기존 커밋 기록 등 완전히 삭제하려면 rm -rf .git   

vscode를 이용하고 있다고 가정할 때, 새로운 걸 생성하거나, 삭제를 하는 것은 디렉토리에 변화를 일으킨 것, 그 바뀐 개수를 숫자로 Source Control에 표시   
commit할 때 제목은 52바이트까지, 뭔가 설명할 거 쓰려면 엔터 두 번   
commit할 때 필요한 건 user.name, user.email.   

$ git config user.name '자기 이름'   
$ git config user.email '자기 이메일'로 git에 이름과 이메일을 등록    
$ git config list는 Git의 설정값을 확인하는 명령어     
내용이 길게 나올텐데, q를 누르면 빠져나올 수 있다.   

## github에 publish

로그인이 안되어 있으면 오류 뜸 github에 로그인 해야 함   
Publish Branch 누르고 Public으로 설정     
README.md에서 md 뜻은 마크다운

## React?

### Node.js란?

React는 Node.js에서 파생된 것   
Apache 서버는 요청을 처리할 때 블로킹 방식으로 동작한다는 문제가 존재, 따라서 비동기 방식의 서버를 개발   
Javascript, 크롬의 v8엔진을 통해 제작   

#### Node.js는 어디에 활용?

웹 서버 (Express.js, Nest.js) → REST.API, GraphQL API 개발   
실시간 애플리케이션 (Socket.io) → 채팅, 실시간 알림, 스트리밍   
서버리스(Serverless) 환경 → AWS Lambda 같은 FaaS(Function as a Service)

#### Node.js가 인기를 끄는 이유
빠른 성능 : V8 엔진 기반 + 비동기 논 블로킹 방식으로 고성능 처리 가능   
JavaScript 풀스택 개발 : 프론트엔드와 백엔드를 같은 언어로 개발 가능   
활발한 생태계 : npm을 통해 다양한 패키지 사용이 가능   
실시간 애플리케이션에 강함 : WebSocket, Socket.io 지원   
마이크로 서비스 및 서버리스 환경과의 조화로운 연동

#### Node.js는 앞으로도 계속 발전할까?
Deno의 등장 : Node.js 창시자가 만든 Deno가 경쟁자로 떠오르고 있지만, Node.js의 생태계가 훨씬 크고 안정적   
ES 모듈 지원 강화 : 기존의 CommonJS에서 ES 모듈로의 전환 진행 중   
클라우드 및 서버리스 환경 확대 : AWS, Azure, GCP   

#### Node.js의 장단점
장점   
1. 비동기 논 블로킹 I/O로 높은 성능 제공
2. JavaScript 풀스택 개발이 가능하여 생산성이 향상됨
3. npm의 방대한 생태계를 활용 가능
4. 경량 서버 개발에 적합
5. 실시간 데이터 처리가 강력함

단점
1. CPU 집약적인 작업에 부적합 : 싱글 스레드 기반이라 멀티스레딩 성능이 부족함   
2. 콜백 지옥 문제 : 해결책으로 async/await과 Promise 사용   
3. 보안 취약점 : npm 패키지의 보안 문제가 자주 발생   

### React의 진화
Facebook에서 조던 윌케가 개발   
복잡한 UI를 쉽게 관리하기 위해 만들어짐  
React 0.3 (2013): 컴포넌트 기반 아키텍처 도입   
React 15 (2016): 성능 개선과 효율적인 렌더링   
React 16 (2017): Fiber 아키텍처 도입으로 비동기 렌더링 가능   
React 16.8 (2019): Hooks 도입으로 함수형 컴포넌트에서 상태 관리와 사이드 이펙트 처리 가능   
React 17 (2020): 주요 변경 사항 없이 업그레이드 용이성 개선   
React 18 (2024): Concurrent Mode 도입, React Server Components 지원   
2024년 12월 5일 19.0.0 버전이 공개됨   
React의 주 버전은 2, 3년에 한 번씩, 부 버전은 1년에 수차례 업그레이드   
React는 front end 개발자의 필수 과목   
공식문서를 통해 공부할 예정   

### React 개발환경 구축
Node.js(LTS) 다운로드   
node -v를 통해 버전 확인   
npm -v, npx -v : Node Package Manager, Node Package eXecute의 버전 확인   
node 설치하면 npm과 npx 자동 설치   
working project 만들기   
npx create-react-app '프로젝트 이름'으로 설치     

### React Project의 구조 
my-project: project 이름
node_modules/: 프로젝트에 설치된 모든 패키지들이 저장되는 폴더, npm install하면 여기에 저장됨
public/ 정적 파일을 저장   
  &nbsp;&nbsp;&nbsp;&nbsp;favicon.ico:브라우저 탭에 표시되는 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;index.html: React 앱이 마운트되는 html 파일 div id="root"가 포함   
  &nbsp;&nbsp;&nbsp;&nbsp;logo192.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;logo512.png: PWA용 아이콘   
  &nbsp;&nbsp;&nbsp;&nbsp;manifest.json: PWA 관련 설정 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;robots.txt: 검색 엔진 크롤러의 접근을 제어하는 파일   
src/: React 앱의 주요 코드가 위치하는 폴더   
  &nbsp;&nbsp;&nbsp;&nbsp;App.css: App.js에 적용되는 스타일   
  &nbsp;&nbsp;&nbsp;&nbsp;App.js: 메인 컴포넌트   
  &nbsp;&nbsp;&nbsp;&nbsp;App.test.js: Jest를 사용한 기본 테스트 파일   
  &nbsp;&nbsp;&nbsp;&nbsp;index.css: 전역 스타일   
 &nbsp;&nbsp;&nbsp;&nbsp; index.js: React 앱의 진입점. ReactDOM.createRoot를 사용하여 App.js를 렌더링함       
  &nbsp;&nbsp;&nbsp;&nbsp;logo.svg: 기본 로고 이미지   
 &nbsp;&nbsp;&nbsp;&nbsp; reportWebVitals.js: 성능 측정 관련 코드   
 &nbsp;&nbsp;&nbsp;&nbsp; setupTests.js: 테스트 환경 설정 파일   
.gitignore: Git에 추가하지 않을 파일 목록을 정의   
package-lock.json: 프로젝트에 설치된 정확한 패키지 버전과 의존성 정보를 기록    
package.json: 패키지(라이브러리)와 프로젝트 정보를 관리하는 파일    
README.md:    
&nbsp;&nbsp;&nbsp;&nbsp;  프로젝트 개요: 프로젝트가 무엇을 하는지 간략히 설명    
&nbsp;&nbsp;&nbsp;&nbsp;  설치 방법: 프로젝트를 실행하려면 어떻게 해야 하는지, 필요한 환경은 무엇인지 등    
 &nbsp;&nbsp;&nbsp;&nbsp; 사용법: 프로젝트를 어떻게 사용하는지, 주요 기능은 무엇인지    
&nbsp;&nbsp;&nbsp;&nbsp;  기여 방법: 프로젝트에 기여하는 방법, 코드 작성 규칙 등    
 &nbsp;&nbsp;&nbsp;&nbsp; 라이센스: 프로젝트의 라이센스 정보    
  기타 정보: 프로젝트의 버전, 개발자 연락처, FAQ 등    
