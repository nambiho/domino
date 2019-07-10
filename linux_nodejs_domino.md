#1 리눅스 설치.
#4 root password 생성
  sudo passwd root
#4 notes 그룹 생성
  groupadd notes
#4 notes 사용자를 만들어서 notes그룹에 넣기
  useradd -g notes notes
#4 notes sudo 권한 부여
  visudo
  notes ALL=(ALL:ALL) ALL
#4 domino /tmp로 복사
#4 domino 압출 풀기
  tar -xvf Domino1001...
#4 압축 해제된 domino디렉토리에서 root로 설치.
  sudo ./install
#4 id파일이 있으면 /local/notesdata로 이동.

#4 서버 실행과 도미노 설정

#4 root로 작업 : CentOS 7 부터는 firewalld로 넘어갔다고 함.
  firewall-cmd --permanent --zone=public --add-port=80/tcp
  firewall-cmd --permanent --zone=public --add-port=1352/tcp
  firewall-cmd --permanent --zone=public --add-port=<proton port>/tcp

#4 notes.ini설정
  Debug_threadid=0
  PROTON_LISTEN_ADDRESS=0.0.0.0
  PROTON_LISTEN_PORT=<proton port>
  Create_R10_Databases=1


#1 Nodejs 테스트
#4 root로 작업
  proton 복사
  proton 압축해제
    cd /opt/ibm/domino/notes/latest/linux
    tar xzvf /tmp/proton-addin-0.2.2.tgz
  proton 설치
    sh -v ./setup_proton.sh
#4 notes client
  데이터베이스 생성 후  ODS가 53 확인
    53이 아닌 경우 notes.ini에서 Create_R10_Database=1
    또는 domino console에서 load updall <database path> -e 실행
#4 node 디렉토리 생성 후 init
#4 domino-db package install
  yarn 또는 npm 으로 domino-domino-db-1.1.0.tgz 설치
    yarn add ./domino-domino-db-1.1.0.tgz
    npm install ./domino-domino-db-1.1.0.tgz
#4 index.js 생성
~~~
const server = require('@domino/domino-db/src/server');
const serverConfig = {
  hostName: '192.168.1.155',
  connection: {
    port: '23520',
  },
};

const svrPromise = server(serverConfig);
svrPromise.then(async svr => {
  const hostname = await svr.getHostName();
  const db = await svr.useDatabase({
    filePath: 'node/node-demo.nsf'
  });
  
  const documents = await db.bulkReadDocuments({
    query: "Form = 'Contact' and LastName = 'Fisher'",
  });
  console.log(documents);
})
.catch(err => console.log(err));
~~~
#4 node 실행
  node index.js
