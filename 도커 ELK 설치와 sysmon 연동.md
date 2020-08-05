## ELK + Sysmon -> 장점 기존 sysmon의 이벤트 분석보다 시각화가 좋고 분석이 수월하다. 실시간 로그 전송 가능

1. sysmon에서 로그 데이터 수집
2. winlogbeat로 로그 데이터 전송
3. Ubuntu로 DB에 저장 ( LogStash )		2,3,4가 ELK
4. Kibana로 분석

logstash에서 로그 데이터를 수신 및 가공

logstash 설정 : elasticsearch에 보낼 로그 설정

sysmon의 로그를 바로 elasticsearch로 전송

input : beats라는 것을 통해 받는다.

output : elasticsearch로 보낼 계정 패스워드, elasticsearch에서 검색할때 고유한 값 = index ( 날짜로 설정 ), 도큐먼트 타입 설정

각 도커 컨테이너의 포트는 docker-compose.yml파일에 저장되어 있다.

winlogbeat 설정법 : window powershell(관리자권한)에 해당 winlogbeat의 폴더 경로로 이동 -> winlogbeat의 폴더에 yml파일의 내용을 변경 ==>> sysmon의 이벤트 log를 수집하여 logstash로 보낸다(우분투 ip)

sysmon 설정법 : 어떤 로그를 수집할지 기업에서 미리 설정한 내용을 sysmon폴더에 저장후 cmd의 관리자권한으로 sysmon의 폴더로 이동 -> .\sysmon.exe -i [sysmon설정파일.xml] 이후 sysmon started가 떠야 함
이후 powershell에서 winglobeat 실행 

PS> .\install-service-winlogbeat.ps1 (여기서 권한이 없는경우 2. Get-ExecutionPolicy 명령어를 작성하면 본인의 권한? 상태가 보여집니다.

 권한이 RemoteSigned 가 아니라면 Set-ExecutionPolicy RemoteSigned 를 입력
   
PS> start-service winlogbeat     < - > stop-service winlogbeat(서비스 중단)

이후 get-service winlogbeat로 서비스가 running으로 변경되며 서비스가 실행한다.

여기까지 현재 상황

sysmon의 데이터가 logstash로 실시간으로 전송중이고 elastic search에 실시간으로 저장이된다.


이후 우분투의 서버에서 나의 ip에 kibana의 port numb으로 인터넷 접속 (http://192.168.0.64:5601/) 이후 kibana설정의 Index Patterns에서 Create index pettern에서 winlogbeat-2020.날짜가 index로 설정된 날짜가 설정이 된다.

이후 index pattern을 winlogbeat이후 next step 다음 x축은 timestamp로 index pattern을 만들면 실시간으로 전송된 sysmon이 elk로 전송이 되고 kibana에서 데이터를 확인할수 있다. 이후 kinana의 discover에서 log를 확인할 수 있다.

위에서 쿼리를 통해 로그 분석할 수 있다.


winlog.event_id



