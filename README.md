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

##### package.json의 내용
dependencies : 실제 코드에서 필요한 패키지,	예시로 express, react    
devDependencies :	개발 환경에서만 필요한 패키지, 예시로 (테스트, 빌드 도구 등)   
peerDependencies : 필요한 라이브러리만, 직접 설치하지 않고 사용자에게 설치를 맡기는 경우
optionalDependencies : 설치가 실패하더라도 프로젝트 실행에 꼭 필요하지 않은 패키지, 해당 패키지가 설치되지 않아도 프로젝트가 정상적으로 작동해야 하는 경우 사용

##### package.json을 유지해야 하는 이유
프로젝트의 의존성 정보 제공
버전 범위 설정 가능
스크립트와 메타데이터 저장
새로운 패키지 설치 및 관리

#### 의존성을 왜 관리하나요?
##### 설치가 쉬움
npm install 또는 yarn install 한 줄로 모든 의존성을 자동 설치 가능   
특정 버전의 라이브러리를 쉽게 업데이트할 수 있음   
##### 일관된 개발환경 유지
개발자마다 다른 버전의 라이브러리를 사용하면 오류 발생 가능성 증가   
package.json에 의존성을 명확히 정의하면, 팀원들이 동일한 환경에서 개발 가능   
##### 배포 환경과 개발 환경 분리
개발 중에만 필요한 패키지(devDependencies)와 배포 시 필요한 패키지(dependencies)를 구분하여 최적화 가능   
##### 불필요한 코드 관리 (최적화)
프로젝트에서 사용하지 않는 패키지가 남아 있으면 용량 증가 & 성능 저하   
package.json만 수정해서 쉽게 관리 가능

| 구분            | package.json                                  | package-lock.json                              |
|---------------|---------------------------------|----------------------------------|
| 역할          | 프로젝트 기본 정보, 의존성 정의     | 설치된 패키지의 정확한 버전 고정  |
| 내용          | 패키지 이름, 버전, dependencies, scripts 등 포함  | 의존성 트리 및 패키지의 정확한 버전 기록  |
| 업데이트      | 직접 수정 가능 | 직접 수정하지 않으며, npm install 시 자동 업데이트  |
| 버전 관리      | `^1.0.0`처럼 범위 지정 가능       | `1.0.0`처럼 고정된 버전 유지  |
| Git 관리      |  |  |

#### node module의 재설치
node module을 다시 설치해야 하는 경우   
1. 팀 작업을 하면서 Github으로부터 프로젝트 파일을 clone 했을 경우
2. 개인이 자신의 프로젝트를 다른 PC 등에서 clone을 받아 계속 개발해야 하는 경우
3. 프로젝트에 문제가 생겨서 node_module을 다시 설치해야 하는 경우

##### clone을 받은 프로젝트의 경우   
   다음 명령을 실행하면 package.json과 package-lock.json을 참고하여 패키지를 다시 설치 npm install    
   node_modules 디렉토리가 자동 생성   
   설치가 끝나면 프로젝트를 실행 시켜서 정상 동작 확인   

##### 개인이 자신의 프로젝트를 다른 PC 등에서 clone을 받아 계속 개발해야 하는 경우
   rm -rf node_modules package-lock.json node_modules랑 package-lock.json 삭제   
   npm cache clean --force 캐시도 깔끔히 삭제   
   npm install 다시 설치   

##### package-lock.json을 삭제하는 이유
   1. package-lock.json이 손상되었거나, 잘못된 의존성이 있을 때
      예시로 패키지를 여러번 업데이트하면서 충돌이 발생한 경우
      수동으로 package.json을 수정해서 package-lock.json에 영향을 미친 경우
   3. 최신 버전의 패키지를 다시 받고 싶을 때
   4. 팀 프로젝트에서 다른 팀원이 이상한 상태로 package-lock.json을 업데이트 했을 때

### React 홈페이지
文A 버튼 눌러서 한국어로 바꾸면 쉽게 볼 수 있음
버전 19가 최신임 react.dev

#### 개요
React는 component 단위로 개발하여 레고를 조립하듯이 앱을 완성
component는 작은 기능을 실행할 수 있는 하나의 모듈
공식 사이트의 홈에는 component가 어떻게 사용되는지 설명하고 있음
component의 조립 과정이 중요

#### Component를 사용한 유저 인터페이스 생성
React를 사용하면 component라고 하는 개별 조각으로 사용자 인터페이스를 구축할 수 있음
Video, Thumbnail 및 LikeButton이라는 react component를 생성해서 조립

#### Component를 작성하는 JavaScript와 Markup
React component는 JavaScript 함수   
조건에 따라 화면을 다르게 표시하려면 if문   
목록을 표시하려면 map()   
→ JS를 이미 알고 있으면 더 쉽게 배울 수 있음   

#### 필요한 곳에 상호작용 기능 추가
React component는 데이터를 수신하고, 화면에 표시해야 하는 내용을 반환   
사용자의 입력을 받아 새로운 데이터를 component에 전달 가능   
이때 React는 상호작용을 통해 얻은 새 데이터로 화면을 업데이트    
 
React 개발은 작은 component를 합쳐서 하나의 새로운 component를 만드는 식   

#### full-stack App 개발을 도와주는 React Framework
React는 라이브러리이기 때문에 component를 조합할 수는 있지만, 라우팅 및 데이터 가져오기 방법 등을 규정하진 않음   
React로 전체 앱을 빌드하려면 Next.js 또는 Remix와 같은 full-stack React Framework를 사용하는 것이 좋음   
사이트의 confs/[slug].js는 Next.js에서 제공하는 routing 방법 중 하나   
React도 하나의 아키텍처   
아키텍처 : 소프트웨어의 구성요소들 사이에서 유기적 관계를 표현하고 소프트웨어의 설계와 업그레이드를 통제하는 지침과 원칙     
따라서 이를 구현하는 Framework를 사용하면, 서버에서 실행되거나 혹은 빌드 중에도 비동기 component에서 데이터를 가져올 수도 있음     
또한 파일이나 데이터베이스에서 데이터를 읽어와서 대화형 component에 전달할 수도 있음    

#### 모든 플랫폼에서 최고의 성능을 발휘하는 React
React를 사용하면 동일한 기술로도 웹 앱과 네이티브 앱을 모두 구축할 수 있음   
각 플랫폼의 고유한 강점을 활용하여 모든 플랫폼에 잘 어울리는 인터페이스를 구현할 수 있음   

사람들은 웹 앱 페이지가 빠르게 로드되는 것을 원함   
React를 사용하면 서버에서 데이터를 가져오는 동안에도 HTML을 스트리밍하는 것을 시작할 수 있기 때문에, JS 코드가 로드되기 전에 콘텐츠를 점진적으로 채울 수 있음   
또한 클라이언트 측에서는 표준 웹 API를 사용해서, 렌더링 도중에도 UI를 반응하도록 할 수 있음   
이런 동작들은 사람들이 원하는 빠른 렌더링을 도와줌   

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
