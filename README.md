2주차 React1 수업
알아두어야 할 명령어
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

github에 publish
로그인이 안되어 있으면 오류 뜸 github에 로그인 해야 함
Publish Branch 누르고 Public으로 설정
