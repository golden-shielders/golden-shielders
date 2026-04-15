# [Modesecurity]

> **한 줄 요약**: Apache, IIS 및 Nginx의 오픈소스, 크로스 플랫폼 웹 애플리케이션 방화벽(WAF)엔진이다.

---

## 1. 개요

### Modesecurity란?
Breach Security에서 만든 오픈소스 WAF로, 최근에는 OWASP 프로젝트로 관리되고 있으며 자사 오픈소스 WAF 프로젝트로 안내하고 있다. 주로 Apache나 Nginx같은 웹서버에 리버스 프록시를 붙여서 사용한다.

### 어디서 만들었나
- **개발사 / 프로젝트**: Breach Security -> Trustwave -> OWASP
- **라이선스**: Apache License 2.0
- **공식 사이트**: https://github.com/owasp-modsecurity/ModSecurity

### 어떤 상황에서 쓰나
HTTP와 HTTPS 요청을 분석하여, SQLI, XSS 같은 웹(L7) 공격을 탐지 및 차단한다.

### 비슷한 툴과 비교
| 툴             | 특징                          | 차이점                              |
| ------------- | --------------------------- | -------------------------------- |
| **Coraza**    | **SecLang 및 OWASP CRS 호환성** | Go 기반의 최신형                       |
| **NAXSI**     | **고성능·낮은 룰 유지비용**           | Nginx 전용                         |
| **BunkerWeb** | UI와 배포 편의성                  | **보안 기능이 포함된 리버스 프록시형 웹 서버 플랫폼** |

---

## 2. 핵심 기능

| 기능               | 설명                                                       |
| ---------------- | -------------------------------------------------------- |
| HTTP 트래픽 로깅      | HTTP 요청과 응답을 기록해 **분석과 탐지**에 활용                          |
| 실시간 모니터링 및 공격 탐지 | **웹 요청**을 **실시간**으로 **검사**해 **이상 행위**와 **공격 시도**를 **탐지** |
| 공격 예방 및 가상 패치    | 애플리케이션 코드를 수정하지 않아도 **규칙으로 공격을 차단**                      |
| 유연한 규칙 엔진        | 다양한 조건과 규칙을 직접 작성해 **맞춤형 탐지**가 가능                        |
| 임베디드 모드 배포       | Apache, Nginx 같은 웹서버 내부 모듈로 붙여 사용 가능                     |
| 네트워크 기반 배포       | 프록시 또는 게이트웨이 형태로 네트워크 구간에 배치 가능                          |
| 이식성              | 여러 운영체제와 웹서버 환경에서 사용 가능                                  |

---

## 3. 설치 방법

### 요구사항
| 항목  | 최소 사양 | 권장 사양 |
| --- | ----- | ----- |
| CPU | 2     | 4     |
| RAM | 4     | 8     |
| 디스크 | 40    | 80    |


### 설치 단계
준비물: Nginx 혹은 Apache가 설치되어 있는 웹서버

**Step 1 — [설치에 필요한 패키지 설치]**
```bash
sudo apt install -y git g++ gcc make automake autoconf libtool pkgconf libpcre2-dev libxml2 libxml2-dev libyajl-dev libgeoip-dev liblmdb-dev libcurl4-openssl-dev libssl-dev zlib1g-dev libpcre3 libpcre3-dev
```

**Step 2 — [ModSecurity 설치]**
```bash
git clone --recursive https://github.com/owasp-modsecurity/ModSecurity
cd ModSecurity
git submodule init
git submodule update
./build.sh
./configure
make
sudo make install

```

**Step 3 — [ModSecurity Nginx용 커넥터 설치]**
```bash
cd ..
git clone https://github.com/owasp-modsecurity/ModSecurity-nginx
```

**Step 4 — [Nginx 설치 및 ModSecurty 커넥터 모듈 설치]**
```bash
wget http://nginx.org/download/nginx-1.28.0.tar.gz
tar -xzf nginx-1.28.0.tar.gz
cd nginx-1.28.0
./configure --add-module=../ModSecurity-nginx
make
sudo make install
```

### 초기 설정
```bash
vi /usr/local/nginx/conf/nginx.conf
	```
	http {
    server {
        listen 80;
        server_name _;

        modsecurity on;
        modsecurity_rules_file /usr/local/nginx/conf/modsecurity.conf;

        location / {
            proxy_pass http://TARGET_SERVER:PORT;
	        }
	    }
	}
	```
# ModSecurity에 필요한 기본 설정파일
sudo cp ../ModSecurity/modsecurity.conf-recommended /usr/local/nginx/conf/modsecurity.conf
sudo cp ../ModSecurity/unicode.mapping /usr/local/nginx/conf/unicode.mapping
sudo sed -i 's/SecRuleEngine DetectionOnly/SecRuleEngine On/' /usr/local/nginx/conf/modsecurity.conf
```

> **확인 방법**: proxy_pass가 정상적으로 설정되었다면 접속 시 설정해놓은 웹서버로 넘어가게 된다.


**[Wazuh-agent에 보낼 Log 추가 설정]**
```bash
sudo vi /var/ossec/etc/ossec.conf
	```
	# 해당 내용 추가
	# Nginx 설치 경로 확인 필요
	<localfile>  
	<location>/usr/local/nginx/logs/access.log</location>  
	<log_format>syslog</log_format>  
	</localfile>  
	
	<localfile>  
	<location>/usr/local/nginx/logs/error.log</location>  
	<log_format>syslog</log_format>  
	</localfile>  
	
	# /usr/local/nginx/conf/modsecurity.conf 에서 해당 경로 확인 가능
	<localfile>  
	<location>/var/log/modsec_audit.log</location>  
	<log_format>syslog</log_format>  
	</localfile>
	```
```


---

## 4. OWASP-CRS 설치

### OWASP-CRS란?
OWASP Core Rule Set의 줄임말로, WAF 엔진에서 사용하는 범용 웹 공격 탐지 규칙 모음이다. OWASP는 SQLI, XSS LFI 등 다양한 웹 공격을 탐지하는 Generic Attack Detection Rules라고 설명한다.
### 왜 설치해야하는가?
ModSecurity는 WAF엔진으로서 웹 요청에 대해서 검사를 하고 룰을 기반으로 해당 요청을 차단한다. 그러나 ModSecurity는 기본 Rule을 포함하고 있지 않기 때문에 OWASP-CRS를 설치하여 해당 Rule을 기반으로 차단할 수 있게 설정하기 위함이다.


**Step 1 — [OWASP-CRS 설치]**
```bash
cd /usr/local/nginx/
mkdir modsec
cd modsec
wget https://github.com/coreruleset/coreruleset/releases/download/v4.25.0/coreruleset-4.25.0-minimal.tar.gz
tar -xzf coreruleset-4.25.0-minimal.tar.gz
mv coreruleset-4.25.0-minimal owasp-crs
```
**Optional Setup — [crs-setup.conf 설정]**
```bash
# 따로 설정하지 않고 그대로 사용해도 되지만 운영환경에 따라서 설정해주는 걸 권장하고 있다.
mv owasp-crs/crs-setup.example owasp-crs/crs-setup.conf
vi owasp-crs/crs-setup.conf
	```
	Paranoia Level: 탐지강도(값을 높이면 탐지는 강해지지만 오탐 가능성 상승)
	Anomaly Scoring Threshold: 이상행위 점수를 몇점 이상일때 차단할지 정함.
	Early Blocking: 초기에 바로 차단할지의 여부
	Response Rules관련 옵션: 응답 본문까지 검사하는 규칙을 그대로 둘지, 환경에 따라 일부를 끌지 검토 가능.
	```
```
**Step 2 — [modsecurity.conf에 CRS 룰 추가]**
```bash
cd ..
vi conf/modsecurity.conf
	```
	Include /usr/local/nginx/modsec/owasp-crs/crs-setup.conf
	Include /usr/local/nginx/modsec/owasp-crs/rules/*.conf
	```
```
**Step 3 — [정상작동중인지 Log 확인]**
```bash
tail -f /var/log/nginx/error.log  
tail -f /var/log/modsec_audit.log
```

---

## 5. 주요 명령어 / 설정

```bash
# ── 자주 쓰는 명령어 ──────────────────────

/path/to/nginx -t          # nginx 설정 파일 문법 검사
/path/to/nginx -s reload   # nginx 재시작
/path/to/nginx start/stop  # nginx 시작/중지
```

### 주요 설정 파일
| 파일 경로                                  | 용도                |
| -------------------------------------- | ----------------- |
| `/path/to/nginx/conf/nginx.conf`       | nginx 설정 파일       |
| `/path/to/nginx/conf/modsecurity.conf` | modsecurity 설정 파일 |

### 주요 로그 파일
※해당 기준은 소스빌드 기준이며, modsec의 경우 modsecurity.conf 파일에 audit log 경로를 확인해야함.

| 파일 경로                            | 용도                |
| -------------------------------- | ----------------- |
| `/path/to/nginx/logs/access.log` | 웹 서버 접근 기록        |
| `/path/to/nginx/logs/error.log`  | 웹 서버 에러 로그        |
| `/var/log/modsec_audit.log`      | modsec에 의해 차단된 로그 |

---

## 6. OWASP-CRS 커스텀 룰 작성
※ 커스텀 룰 작성 시 modsecurity.conf에 Include 시켜줘야 함.
### 기본문법
```bash
SecRule @VARIABLES @OPERATOR "@ACTIONS"
```
### VARIABLES (무엇을 검사할지)

| 파라미터            | 용도                         |
| --------------- | -------------------------- |
| REQUEST_URI     | 요청한 URI 경로를 검사할 때 사용       |
| ARGS            | GET/POST 파라미터 전체를 검사할 때 사용 |
| REQUEST_HEADERS | 요청 헤더 값을 검사할 때 사용          |
| REQUEST_BODY    | 요청 본문 데이터를 검사할 때 사용        |

### OPERATOR (어떻게 비교할지)

| 파라미터          | 용도                  |
| ------------- | ------------------- |
| `@contains`   | 특정 문자열이 포함되어 있는지 비교 |
| `@rx`         | 정규표현식으로 패턴 매칭       |
| `@beginsWith` | 특정 문자열로 시작하는지 비교    |
| `@eq`         | 값이 정확히 같은지 비교       |

### ACTIONS (언제, 어떻게 처리할지)


| 파라미터     | 용도                    |
| -------- | --------------------- |
| `phase`  | 어느 처리 단계에서 룰을 검사할지 지정 |
| `id`     | 룰의 고유 번호 지정           |
| `log`    | 룰 매칭 시 로그를 남김         |
| `deny`   | 요청을 차단                |
| `status` | 차단 시 반환할 HTTP 상태코드 지정 |
| `msg`    | 로그에 남길 메시지 지정         |
| `t`      | 검사 전에 데이터를 변환하거나 정규화  |
| `ctl`    | 특정 룰 동작을 제어하거나 예외 처리  |
### 룰 예시
#### 1. 특정 URI 차단
``` apache
SecRule REQUEST_URI "@beginsWith /private" "phase:1,id:100010,log,deny,status:403,msg:'Private area blocked'"
```
#### 2. 특정 파라미터 값 패턴 검사
``` apache
SecRule ARGS "@rx (?i)select.+from" "phase:2,id:100011,log,deny,status:403,msg:'Suspicious SQL pattern'"
```
#### 3. 체인 룰 (첫 조건 만족시 수행)
```apache
SecRule REQUEST_METHOD "^POST$" "phase:1,id:100012,log,deny,status:403,chain,msg:'POST without Content-Length'"
    SecRule &REQUEST_HEADERS:Content-Length "@eq 0"
```
#### 4. 예외처리
```apache
SecRule REQUEST_URI "@beginsWith /index.php" "phase:1,id:100020,pass,nolog,ctl:ruleRemoveTargetById=981260;ARGS:user"
```

---

## 7. 참고 자료
- [공식 문서(V3.x 이상)](https://github.com/owasp-modsecurity/ModSecurity/wiki/Reference-Manual-%28v3.x%29)
- [GitHub](https://github.com/owasp-modsecurity/ModSecurity)
- [OWASP-CRS](https://owasp.org/www-project-modsecurity-core-rule-set/)