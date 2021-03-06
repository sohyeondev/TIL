	ü 이더넷의 규격
		○ IEEE802 위원회에서 결정
		○ 명칭 ex) 1000BASE-T
			§ 맨 앞의 숫자 : 전송 속도
			§ BASE : 베이스 밴드 방식
			§ '-' 뒤 : 전송 매체나 물리 신호 변환의 특징, T는 주로 UTP 의미
	ü 인터페이스
		○ MAC 주소 : 이더넷 인터페이스를 특정하기 위한 48비트 주소
			§ 선두 24비트 : OUI(인터넷 인터페이스를 제조하는 벤더 식별 코드)
			§ 뒤 24비트 : 시리얼 넘버(각 벤더가 할당)
			§ 이더넷 인터페이스에 미리 할당되어 있어 기본적으로 변경할 수 없는 주소
			§ 물리 주소 혹은 하드웨어 주소라고도 함
			§ 표기
				□ 1바이트씩 16진수 변환, '-'로 구분
				□ 1바이트씩 16진수 변환 : ':' 구분
				□ 2바이트씩 16진수 변환 : '.' 구분
				
		○ UTP 케이블 : 이더넷의 전송 매체, LAN 케이블
		○ RJ-45 이더넷 인터페이스 : UTP 케이블용
	ü 이더넷의 프레임
		○ 이더넷 헤더와 데이터, FCS(에러 체크)를 합한 전체
		○ 이더넷 헤더 
			§ 목적지 MAC 주소
			§ 출발지 MAC 주소
			§ 타입 코드
	ü 토폴로지 : 네트워크에서 기기를 연결하는 형태
		○ 버스형
			§ 동축 케이블을 전송 매체로 함
			§ 하나의 전송 매체에 각 기기가 매달린 듯한 연결 형태
			§ 하나의 전송 매체를 복수의 기기가 공유 => 어떻게 공유할 것인지 제어(CSMA/CD)
		○ 스타형
			§ 레이어2 스위치를 중심으로 함
		○ 링형
	ü CSMA/CD(데이터 전소 타이밍 제어)
		○ CS 
			§ 케이블이 현재 사용 중인지 감지 => 사용 중이면 대기, 비어 있으면 데이터 전송
			§ 동시에 복수의 호스트가 비었다고 판단 => 복수의 데이터 전송 => 전기 신호 충돌(전압의 변화) => 랜덤 대기시간
		○ 현재 이더넷에는 필요 없음(현재의 이더넷은 전송 매체를 공유하지 않음)
	ü 레이더2 스위치
		○ 이더넷을 이용한 네트워크 하나를 구성하는 네트워크 기기(여러 대 연결해도 하나)
		○ VLAN을 이용하면 복수의 네트워크 형성 가능
		○ 레이어2 스위치로 구성된 한 이더넷 네트워크 안에서 데이터(이더넷 프레임) 전송
		○ 이더넷 프레임을 전송하기 위해 이더넷 헤더의 MAC 주소 확인(수신한 이더넷 프레임 변경 없음)
		○ 네트워크의 입구 역할 => 엑세스 스위치라고도 표현
		○ 일반 가정용 제품의 경우 스위칭 허브라고 하기도 함
		○ 레이어2 스위치의 동작
			§ 수신한 이더넷 프레임의 출발지 MAC 주소를 MAC 주소 테이블에 등록
			§ 목적지 MAC 주소와 MAC 주소 테이블에서 전송할 포트를 결정해, 이더넷 프레임을 전송
			§ MAC 주소 테이블에 존재하지 않는 MAC 주소의 경우(Unknown 유니캐스트 프레임)는 수신 포트를 제외한 모든 포트로 이더넷 프레임을 전송(플러딩)
	ü MAC 주소 테이블 관리
		○ 하나의 포트에 반드시 하나의 MAC 주소만 등록되는 것은 아님
		○ MAC 주소 정보에는 보통 5분 정도의 제한시간이 있음
		○ PC 가 실행되는 동안은 MAC 주소 테이블에 PC의 MAC 주소가 등록되어 있음
	ü 전이중 통신
		○ 데이터 송신과 수신을 동시에 하는 것
		○ 데이터 수신용과 송신용으로 전송 매체를 나누어 사용
		○ 반이중 통신 : 송신과 수신을 동시에 할 수 없어, 서로 전환하면서 처리
	ü 무선 LAN
		○ 무선 LAN 인터페이스
			§ 노트북 컴퓨터나 스마트폰/태블릿에 미리 내장
			§ 데스크톱PC는 나중에 장착 가능
			§ 무선 LAN 인터페이스로 무선 LAN에 연결된 기기 = 무선 LAN 클라이언트
		○ 무선 LAN 액세스 포인트
			§ 무선 LAN 데이터 통신은 무선 LAN 액세스 포인트를 경유 = 인프라스트럭처 모드
			§ 무선 LAN 인터페이스끼리 직접 데이터를 주고받음 = 애드혹 모드
			§ 무선 LAN만으로는 통신이 완결되지 않는 경우가 보통 => 무선 LAN 액세스 포인트는 레이어2 스위치와 접속해 유선 이더넷 네트워크와 연결
		○ 무선 LAN 규격
			§ 이용하는 전파의  주파수 대역 차이
		○ Wi-Fi
		○ 어소시에이션
			§ 무선 LAN에 연결하는 것, 유선 이더넷의 케이블 배선에 해당
			§ SSID(무선 LAN의 논리적인 그룹을 식별하는 식별정보) 필요
				□ 무선 LAN 엑세스 포인트에 최대 32문자의 문자열로 SSID 지정
				□ 한 대의 엑세스 포인트에 복수의 SSID 설정 가능
				□ 복수의 엑세스 포인트에 같은 SSID 설정 가능
				□ ESSID(Extended Service Set Identifier)로 부르기도 함
				□ 암호화나 인증 등의 보안 설정은 SSID 별로 함
			§ 무선 LAN 클라이언트는 액세스 포인트가 내보내는 제어신호(비콘)에서 이용할 수 있는 전파의 주파수(채널)을 찾음 => 이용할 수 있는 채널을 발견하면 SSID를 지정해서 무선 LAN 액세스 포인트에 어소시에이션 요청을 보냄 => 무선 LAN 액세스 포인트는 어소시에이션 응답으로 접속할 수 있는지 알려줌
	ü 통신속도
		○ 실효속도/스루풋 : 실질적인 통신속도, 무선 LAN의 스루풋은 규격상 전송속도의 절반 정도
					=> 전파를 돌려쓰기 때문
		○ 전파 : 무선 LAN에서의 전송 매체
		○ 채널 : 무선 LAN 액세스 포인트에서 설정한 특정 주파수대의 전파
		○ 충돌 : 복수의 무선 LAN 클라이언트가 동시에 데이터를 전파에 실어 보내면, 전파가 중첩되어 수신하는 쪽에서도 원래 데이터를 재구성 할 수 없음.
		○ CSMA/CA(Carrier Sense Multiple Access with Collision Avoidance) : 어떤 타이밍에 무선 LAN 클라이언트가 데이터를 전파에서 실어 송신할지 제어할 수 있음
	ü CSMA/CA
		○ 빠른 쪽이 이기는 방식으로 전파를 이용하는 메커니즘
			§ 전파가 이용 중인지 확인한다(Carrier Sense) : 액세스 포인트에서 어소시에이션 했을 때 채널을 알고 있어, 그 채널의 전파를 검출하면 전파가 이용 중인지 알 수 있음. 전파가 이용 중일 때는 대기, 전파가 검출되지 않았다면 일정 시간 대기.
			§ 랜덤 시간 대기(Collision Avoidance) : 전파가 이용 중이 아니여도 랜덤 시간 동안 대기. 복수의 클라이언트가 동시에 전파를 미사용으로 판단하고 곧바로 데이터를 송신하기 시작하면 충돌 발생. 다른 무선 LAN 클라이언트와 송신 타이밍을 어긋나게 함.
			§ 데이터 송신 : 랜덤 시간동안 기다렸는데도 전파가 미사용이면 송신. 무선 LAN 통신에서는 데이터를 수신했으면 수신 확인 응답으로 ACK 반환.
		○ 무선 LAN 클라이언트가 데이터를 보내려고 할 때 대기 시간이 길어지고 스루풋이 저하.
	ü 무선 LAN의 보안
		○ 인증 : 무선 LAN 액세스 포인트에 정식 사용자만 접속할 수 있게 함.
		○ 암호화 : 무선 LAN으로 송수신하는 데이터를 암호화함으로써 전파를 엿들어도 데이터의 내용 자체가 관계 없는 제3자에게 새어나가는 것을 방지.
		○ WPA2 : 무선 LAN의 보안 규격
			§ 인증에 LEEE802.1X 이용
      § 단순한 암호 인증도 지원
![image](https://user-images.githubusercontent.com/85976426/143966789-6b244068-497f-4838-b534-de89b06e2f5a.png)
