# Wazuh

> **한 줄 요약**: **Wazuh**는 시스템과 로그를 모니터링해 **위협**을 **탐지**하고 **대응**하는 **통합 보안 플랫폼**입니다.다.

---

## 1. 개요

### Wazuh란?
Wazuh는 **위협 예방, 탐지 및 대응**에 사용되는 무료 오픈 소스 플랫폼이다.
**온프레미스, 가상화, 컨테이너화 및 클라우드 기반 환경**에서 실행 가능하다.
### 어디서 만들었나
- **개발사 / 프로젝트**: Wazuh, Inc
- **라이선스**: GPL v2, Apache License 2.0
- **공식 사이트**:  https://wazuh.com

### 사용 목적

Wazuh는 오픈소스 XDR/SIEM 플랫폼으로, 엔드포인트/클라우드 워크로드의 보안 데이터를 수집해 탐지, 대응, 가시성 확보를 위해서 사용한다.

### 비슷한 툴과 비교
| 툴              | 특징                | 차이점            |
| -------------- | ----------------- | -------------- |
| Security Onion | 네트워크 중심 통합 보안 플랫폼 | NSM/NIDS 비중이 큼 |


---

## 2. 핵심 기능

| 기능          | 설명                                                                                                                                        |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| 침입 탐지       | Wazuh에이전트로 모니터링 대상 시스템을 검사하여 **이상 행위 탐지**, 에이전트 기능 외에도, **시그니처 기반 침입 탐지** 방식 사용, 정규 표현식을 통해 **로그 데이터 분석** 및 **침해 지표 탐지**                  |
| 로그 데이터 분석   | **Wazuh 에이전트**로 운영체제 및 애플리케이션 **로그를 읽고**, 중앙 관리자에게 전송하여 **규칙 기반 분석과 저장** 가능, **에이전트가 설치되지 않은 경우** 네트워크 장비나 애플리케이션으로부터 syslog 방식으로 데이터 받음. |
| 파일 무결성 모니터링 | **파일 시스템을 모니터링**하여 중요 파일의 **내용, 권한 , 소유권, 속성 변경을 식별**, 위협 인텔리전스와 결합하여 **위협**이나 **침해된 호스트 식별**.                                            |
| 취약점 탐지      | Wazuh에이전트로 **소프트웨어 인벤토리 정보를 수집**하여 서버로 전송, 서버는 **CVE 데이터베이스**와 연계하여 **취약 소프트웨어 식별**                                                       |
| 구성 점검       | **시스템 및 애플리케이션의 설정**을 모니터링하여, 보안 정책, 하드닝 가이드에 **부합 확인**, **사용자 정의 가능**.                                                                   |
| 사고 대응       | Wazuh에이전트로 활성화된 위협에 대응하기 위한 **액티브 리스폰스** 기능 제공, **원격**으로 **명령어**나 시스템 질의 **실행 가능**                                                        |
| 규제 준수       | **산업 표준 및 규정**을 준수하기 위한 **보안 통제 제공**(해당 기능은 기술적 컴플라이언스 요구사항을 충족하기 위함에 의의를 둠.)                                                             |
| 클라우드 보안     | API 수준에서 **클라우드 인프라 모니터링 지원**, 클라우드 **환경 설정 점검** 제공                                                                                       |
| 컨테이너 보안     | **Docker 호스트와 컨테이너**에 대한 **보안 가시성 제공**(Wazuh에이전트는 기본적으로 Docker엔진과 통합되어 있음.), 상세한 **런타임 정보 수집 및 분석**                                       |

---

## 3. 설치 방법

### 요구사항

#### 대상 OS
* Amazon Linux 2, Amazon Linux 2023
    
- CentOS Stream 10
    
- Red Hat Enterprise Linux 7, 8, 9, 10
    
- Ubuntu 16.04, 18.04, 20.04, 22.04, 24.04

| 항목         | 최소 사양 | 권장 사양 |
| ---------- | ----- | ----- |
| CPU(cores) | 2     | 4     |
| RAM(GB)    | 2     | 8     |
#### DISK space requirements

| Monitored endpoints | APS(alerts per second) | Storage in Wazuh Server<br><br>(GB/90 days) |
| ------------------- | ---------------------- | ------------------------------------------- |
| Server              | 0.25                   | 0.1                                         |
| Workstions          | 0.1                    | 0.04                                        |
| Network devices     | 0.5                    | 0.2                                         |
예를 들어, 워크스테이션 80개, 서버 10개, 네트워크 장비 10개의 90일치 로그만 저장한다고 했을 때 6GB의 공간이 필요하다.

### 설치 단계

#### 설치환경
OS: Ubuntu18.04

**Step 1 — [QuickStart]**
```bash
$ curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

**Step 2 — [Finish]**
```bash
INFO: --- Summary ---
INFO: You can access the web interface https://<WAZUH_DASHBOARD_IP_ADDRESS>
    User: admin
    Password: <ADMIN_PASSWORD>    #<--설치 완료시 해당 Password값이 출력된다.
INFO: Installation finished.
```

### 설치 확인
```bash
# /var/ossec/bin/wazuh-control status
```

> **확인 방법**: 설치 완료시 Wazuh Dashboard가 활성화된다. 이후 ip address로 서버 IP를 확인 한 후 브라우저를 통해 접근한다.

---

## 4. 기본 사용법

### UI
#### 로그인 이후 Wazuh 대시보드 화면 

![[Pasted image 20260409173318.png]]
#### Wazuh 대시보드 네비게이션바 화면
![[Pasted image 20260409174419.png]]


**주요 패널 설명**

- **[Overview]**: Wazuh 대시보드의 전체 보안 현황을 한눈에 보여주는 개요 화면이다.
- **[Agents Summary]**: 등록된 에이전트의 배포 및 연결 상태를 요약해 보여주는 패널이다.
- **[Last 24 Hours Alerts]**: 최근 24시간 동안 발생한 보안 경고를 심각도별로 보여주는 패널이다.
- **[Endpoint Security]**: 엔드포인트 설정 점검, 악성 행위 탐지, 파일 무결성 감시 기능을 제공하는 영역이다.
- **[Threat Intelligence]**: 위협 분석, 취약점 식별, MITRE ATT&CK 기반 분석 기능을 제공하는 영역이다.
- **[Security Operations]**: 운영 상태 점검과 규제 준수 대응을 지원하는 영역이다.
- **[Cloud Security]**: 클라우드 및 외부 연계 서비스의 보안 상태를 확인하는 영역이다.

**주요 메뉴 설명**

- **[Home]**: 대시보드의 시작 화면으로 이동하는 메뉴이다.
- **[Explore]**: Wazuh 관련 문서와 리소스를 탐색하는 메뉴이다.
- **[Endpoint security]**: 엔드포인트 보안 관련 대시보드로 이동하는 메뉴이다.
- **[Threat intelligence]**: 위협 분석 및 취약점 확인 메뉴이다.
- **[Security operations]**: 보안 운영 및 컴플라이언스 관련 메뉴이다.
- **[Cloud security]**: 클라우드 보안 관련 메뉴이다.
- **[Agents management]**: 에이전트와 에이전트 그룹을 관리하는 메뉴이다.
- **[Server management]**: 규칙, 디코더, 로그, 설정 등 서버 기능을 관리하는 메뉴이다.
- **[Indexer management]**: 인덱서와 저장·검색 관련 기능을 관리하는 메뉴이다.
- **[Dashboard management]**: 대시보드 화면과 저장 객체, 고급 설정을 관리하는 메뉴이다.

---

## 5. 주요 명령어 / 설정 치트시트

```bash
# ── 자주 쓰는 명령어 ──────────────────────

/var/ossec/bin/agent_control -l     # 에이전트 목록 조회
/var/ossec/bin/wazuh-control status # Wazuh 프로세스 상태 확인
/var/ossec/bin/wazuh-logtest        # 특정 로그가 어떤 룰에 매칭되는지 테스트
/var/ossec/bin/manage_agents        # 에이전트 등록 및 키 관리
/var/ossec/bin/wazuh-control        #Wazuh 프로세스의 시작, 중지, 상태 확인에 사용할 수 있는 명령어
sudo systemctl restart wazuh-manager          # 서비스 제어는 systemctl 사용 권장
```

### 주요 설정 파일
| 파일 경로                                  | 용도                                                          |
| -------------------------------------- | ----------------------------------------------------------- |
| /var/ossec/etc/ossec.conf              | Wazuh의 기본 메인 설정 파일이다. Manager와 Agent의 주요 동작 설정이 이 파일에 저장된다. |
| /var/ossec/etc/shared/<그룹명>/agent.conf | 에이전트 그룹별 중앙 설정 파일이다. Manager에서 여러 Agent에 공통 설정을 배포할 때 사용된다. |
| /var/ossec/etc/internal_options.conf   | 내부 옵션을 조정하는 파일이다. 주로 디버깅이나 세부 동작 제어에 사용된다.                  |
| /var/ossec/active-response/bin         | Active Response 스크립트가 위치하는 경로이다.                            |
| /var/ossec/logs/                       | Wazuh 로그 파일이 저장되는 경로이다.                                     |
| /var/ossec/bin                         | Wazuh의 주요 명령어 도구들이 위치하는 경로이다.                               |

---

## 6. 실습 예시

### 시나리오: [Wazuh 에이전트 등록]
**목표**: Guest 시스템에 Wazuh 에이전트를 설치한 뒤, 해당 시스템이 Wazuh Server에서 정상적으로 모니터링되는지 확인한다.
**환경**
- Wazuh Ver: 4.14
- Server OS: Ubuntu18.04
- Guest OS: Ubuntu18.04

> [!NOTE] 주의
> 설치하려는 Guest OS의 종류 마다 설치 방법이 다르므로 참고자료의 에이전트 설치관련 Docs를 참조하여 설치를 진행해야한다.


**Step 1 — [Guest OS에 Wazuh 에이전트 설치 준비]**
```bash
$ apt-get install gnupg apt-transport-https -y

$ curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --no-default-keyring --keyring gnupg-ring:/usr/share/keyrings/wazuh.gpg --import && chmod 644 /usr/share/keyrings/wazuh.gpg

$ echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | tee -a /etc/apt/sources.list.d/wazuh.list

$ apt-get update
```

**Step 2 — [Wazuh 에이전트 설치 및 Wazuh 서버와 연동]**
```bash
$ WAZUH_MANAGER="WAZUH_SERVER_IP" WAZUH_AGENT_NAME='WAZUH_GUEST_NAME(MUST BE UNIQUE)' apt-get install wazuh-agent
```

**Step 3 — [부팅 시 Wazuh 에이전트 자동시작 설정]**
```bash
# systemctl daemon-reload
# systemctl enable wazuh-agent
# systemctl start wazuh-agent
```

**Step 4 — [Guest OS에서 Wazuh 업데이트 비활성화]**
```bash
# sed -i "s/^deb/#deb/" /etc/apt/sources.list.d/wazuh.list
```

### Alternatives Installation

**Step 1 — [네비게이션바에서 에이전트 관리 메뉴로 이동]**

![[Pasted image 20260410145420.png]]

**Step 2 — [Deploy new agent 버튼 클릭]**

![[Pasted image 20260410145546.png]]

**Step 3 — [에이전트를 설치할 대상 OS환경 선택]**

![[Pasted image 20260410145713.png]]

**Step 4 — [에이전트가 통신할 Wazuh 서버 IP 입력]**

![[Pasted image 20260410145740.png]]

**Step 5 — [에이전트 이름 설정]**
※ 에이전트 이름은 중복된 이름이 들어갈 수 없음.

![[Pasted image 20260410145755.png]]

**Step 6 — [자동 완성된 명령어 복사]**

![[Pasted image 20260410145948.png]]

**Step 7 — [설치 대상 서버에서 복사한 명령어 실행]**

![[Pasted image 20260410150251.png]]
### 에이전트 설치 완료 시 결과 화면

![[Pasted image 20260410145004.png]]

---
## 6. 참고 자료
- [공식 문서](https://documentation.wazuh.com)
- [GitHub](https://github.com/wazuh/wazuh)
- [에이전트 설치 Docs]([https://github.com/wazuh/wazuh](https://documentation.wazuh.com/4.14/installation-guide/wazuh-agent/wazuh-agent-package-linux.html))