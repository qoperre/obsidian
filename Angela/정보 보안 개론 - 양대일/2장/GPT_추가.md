<thinking_process>

1. **Note Review & Gap Detection**
    
    - **Coverage strength**: Your notes thoroughly list OS‐specific commands, granular log paths, and DB examples.
        
    - **Detected gaps / weak depth**
        
        1. **Access-control _models_** (DAC, MAC, RBAC, ABAC) are never named—even though you talk about “접근 제어”.
            
        2. **Principle of Least Privilege & Separation of Duties** are implied but not explained.
            
        3. **Session hijacking 대응** stops at “암호화·타임아웃”; modern counter-measures (secure cookies, SameSite, MFA-bound sessions, IP/UA binding) are missing.
            
        4. **AAA & network-device log-ins**: TACACS+/RADIUS are briefly mentioned but not contrasted or tied back to 장비 관리.
            
        5. **Vulnerability-management lifecycle** (identify→prioritise→patch→verify→improve) is only hinted as “취약점 관리”.
            
        6. **Log-management best-practice**: you list what Windows/Unix record, but omit syslog severity levels, log-rotation, and SIEM/centralisation trade-offs.
            
2. **Targeted Web Searches** (see citations below)
    
    - Found beginner-friendly explainers on each missing area:
        
        - Access-control models ([Avigilon](https://www.avigilon.com/blog/access-control-models?utm_source=chatgpt.com "Discretionary, Mandatory, Role and Rule Based Access Control"), [tenfold Security](https://www.tenfold-security.com/en/wiki/access-control-models/?utm_source=chatgpt.com "Access Control Models Explained - tenfold"))
            
        - Principle of least privilege ([CyberArk](https://www.cyberark.com/what-is/least-privilege/?utm_source=chatgpt.com "What is Least Privilege? - Definition - CyberArk"))
            
        - Session-hijacking mitigations ([Descope](https://www.descope.com/learn/post/session-hijacking?utm_source=chatgpt.com "Session Hijacking Explained & How to Prevent It - Descope"), [authgear.com](https://www.authgear.com/post/session-hijacking-types-real-world-examples-and-how-to-prevent-it?utm_source=chatgpt.com "Session Hijacking: Types, Real-World Examples, and How to ..."))
            
        - TACACS+ vs RADIUS and AAA roles ([rublon.com](https://rublon.com/blog/radius-vs-tacacs/?utm_source=chatgpt.com "RADIUS vs. TACACS+: What's the Difference? - Rublon"), [Portnox](https://www.portnox.com/blog/network-access-control/radius-vs-tacacs-the-complete-breakdown-for-decision-makers/?utm_source=chatgpt.com "RADIUS vs. TACACS+: A Comparative Breakdown - Portnox"))
            
        - Vulnerability-management steps ([PurpleSec](https://purplesec.us/learn/vulnerability-management-lifecycle/?utm_source=chatgpt.com "The Vulnerability Management Lifecycle (5 Steps) - PurpleSec"), [Pathlock](https://pathlock.com/blog/vulnerability-management-lifecycle/?utm_source=chatgpt.com "What is Vulnerability Management Lifecycle? | Six Stages & Tools"))
            
        - Syslog severity and rotation basics ([Last9](https://last9.io/blog/what-are-syslog-levels/?utm_source=chatgpt.com "Syslog Levels Made Simple: Why They Matter for Your Logs | Last9"), [레딧](https://www.reddit.com/r/ccna/comments/5dgrti/memorizing_syslog_severity_levels/?utm_source=chatgpt.com "Memorizing Syslog Severity Levels : r/ccna - Reddit"))
            
3. **Cross-checking**: No substantive conflicts; sources largely align.
    
4. **Output drafting**
    
    - For every gap create:  
        _Plain-language explanation_ + _3 practice questions_ (MCQ, short answer, scenario).
        
    - Use Korean terminology consistent with your notes but keep key English acronyms.
        
    - Organise with markdown headers for quick lookup.
        

</thinking_process>

<final_response>

# Topic 1: 접근 제어 모델 (DAC·MAC·RBAC·ABAC)

## Explanation

- **DAC (Discretionary Access Control)**  
    객체(파일·디렉터리) 소유자가 “누구에게 어떤 권한을 줄지” 직접 결정하는 방식. 윈도우의 NTFS “보안” 탭이 대표 예. 유연하지만, 소유자 실수로 과도한 권한을 주기 쉬움. ([Avigilon](https://www.avigilon.com/blog/access-control-models?utm_source=chatgpt.com "Discretionary, Mandatory, Role and Rule Based Access Control"))
    
- **MAC (Mandatory Access Control)**  
    군대·정부처럼 _시스템_이 등급 (label)으로 권한을 강제한다. 소유자도 마음대로 바꿀 수 없다. SELinux / Windows LAPS가 예시. ([Avigilon](https://www.avigilon.com/blog/access-control-models?utm_source=chatgpt.com "Discretionary, Mandatory, Role and Rule Based Access Control"))
    
- **RBAC (Role-Based Access Control)**  
    사용자→“역할”→“권한”으로 간접 매핑. 인사 이동 시 역할만 바꾸면 권한이 자동 변경돼 운영 부담이 적다. 회사 ERP, DBMS 대부분이 채택. ([tenfold Security](https://www.tenfold-security.com/en/wiki/access-control-models/?utm_source=chatgpt.com "Access Control Models Explained - tenfold"))
    
- **ABAC (Attribute-Based Access Control)**  
    사용자·리소스·환경 속성(근무지·시간·IP 등)을 조합해 정책을 만든다. “평일 09-18, 사내 IP면 허용” 같은 세밀한 제어가 가능. 클라우드 IAM에서 자주 활용.

## Practice Questions

1. **(객관식)** 다음 중 DAC의 특징으로 올바른 것은?  
    a) 등급(label) 기반 관리 b) 소유자가 권한을 제어 c) 역할 집합 기반 d) 속성 평가
    
2. **(단답형)** RBAC에서 _사용자 계정_과 _권한_ 사이에 위치하는 개념은?
    
3. **(시나리오)** 개발 서버를 SELinux Enforcing 모드로 돌렸더니 팀원이 “chmod 777”을 해도 접근이 막힌다. 이 현상이 발생한 이유를 설명하고 해결 방안을 제시하라.
    

---

# Topic 2: 최소 권한 원칙(Least Privilege) & 직무 분리

## Explanation

- **최소 권한 원칙(PoLP)**: 업무 수행에 꼭 필요한 최소한의 권한만 부여하는 보안 기본 원칙. 관리자 권한 남용·랜섬웨어 피해 범위를 크게 줄여준다. ([CyberArk](https://www.cyberark.com/what-is/least-privilege/?utm_source=chatgpt.com "What is Least Privilege? - Definition - CyberArk"))
    
- **직무 분리(Separation of Duties)**: 한 사람이 “요청+승인+검수” 전체를 담당하지 못하도록 역할을 분산해 내부 위협을 줄이는 통제 기법. 예) 개발자는 운영 서버에 직접 배포하지 않고, CI/CD 파이프라인+운영 승인으로 분리.
    

## Practice Questions

1. **(객관식)** PoLP를 가장 잘 준수한 계정 설계는?  
    a) 모든 직원에게 관리자 그룹 부여  
    b) 금융팀에게 회계 시스템 “읽기+쓰기”만 부여  
    c) 인턴에게 DB 루트 계정 공유  
    d) 운영팀에 개발 git push 권한 포함
    
2. **(단답형)** 직무 분리 원칙이 실패할 경우 발생할 수 있는 대표적 내부 보안 위협은?
    
3. **(시나리오)** 스타트업에서 한 서버 관리자가 AWS Root 키로 인프라를 모두 운영한다. 최소 권한과 직무 분리 측면의 위험을 지적하고 개선 절차를 설계하라.
    

---

# Topic 3: 세션 하이재킹 & 안전한 세션 관리

## Explanation

- **세션 하이재킹**: 공격자가 세션 ID(쿠키·토큰)를 가로채 합법 사용자로 가장하는 공격. 주로 패킷 스니핑, XSS, “Session Fixation” 기법 사용.
    
- **대응 전략**
    
    1. **HTTPS 강제** – 암호화로 세션 ID 노출 방지.
        
    2. **Secure / HttpOnly / SameSite 쿠키** – 스크립트·타 도메인 전송 차단.
        
    3. **재사용 불가 토큰(재로그인·ROT)** – 짧은 타임아웃, IP·UA 변경 시 재인증. ([Descope](https://www.descope.com/learn/post/session-hijacking?utm_source=chatgpt.com "Session Hijacking Explained & How to Prevent It - Descope"), [authgear.com](https://www.authgear.com/post/session-hijacking-types-real-world-examples-and-how-to-prevent-it?utm_source=chatgpt.com "Session Hijacking: Types, Real-World Examples, and How to ..."))
        
    4. **MFA Session Binding** – 로그인 후에도 고위험 요청 때 2차 인증.
        
    5. **서버 측 세션 저장소 무효화** – 로그아웃·패스워드 변경 시 즉시 세션 삭제.
        

## Practice Questions

1. **(객관식)** SameSite=Lax 쿠키 설정의 주된 목적은?  
    a) 쿠키 암호화 b) XSS 방어 c) CSRF 감소 d) 세션 만료 단축
    
2. **(단답형)** 사용자 IP·브라우저 지문이 바뀌면 세션을 폐기하는 기법을 무엇이라 부르는가?
    
3. **(시나리오)** 인터넷 뱅킹에서 10분 무작위 클릭 후 “다시 로그인하세요” 팝업이 뜬다. 이 기능의 보안 의의를 설명하고, 너무 짧을 때 UX 악영향을 최소화할 방법을 제안하라.
    

---

# Topic 4: AAA 프로토콜 – TACACS+ vs RADIUS

## Explanation

- **AAA(인증·인가·회계)**: 네트워크 장비 계정과 행동을 중앙 관리.
    
- **RADIUS**
    
    - UDP 1812/1813 사용, 패스워드만 암호화(헤더는 평문).
        
    - 주로 “사용자 네트워크 접속” 인증(802.1X, VPN).
        
- **TACACS+**
    
    - TCP 49 사용, 전체 패킷 암호화.
        
    - 명령 단위 권한 부여(“show run”만 허용 등)에 강하며, 장비 관리 로그인용에 적합. ([rublon.com](https://rublon.com/blog/radius-vs-tacacs/?utm_source=chatgpt.com "RADIUS vs. TACACS+: What's the Difference? - Rublon"), [Portnox](https://www.portnox.com/blog/network-access-control/radius-vs-tacacs-the-complete-breakdown-for-decision-makers/?utm_source=chatgpt.com "RADIUS vs. TACACS+: A Comparative Breakdown - Portnox"))
        
- **실무 팁**: 대규모 환경은 사내 AAA 서버(예: Cisco ISE)로 로그·정책을 통합, 로컬 패스워드는 “Break-glass” 비상용만 남긴다.
    

## Practice Questions

1. **(객관식)** 장비 관리 명령별 세분화된 권한 제어가 필요한 경우 추천 프로토콜은?  
    a) RADIUS b) TACACS+ c) LDAP d) Kerberos
    
2. **(단답형)** RADIUS 기본 인증/인가/회계 포트는 각각? (둘 중 하나만 맞혀도 정답)
    
3. **(시나리오)** 사내 Wi-Fi 인증은 RADIUS, 스위치 CLI 관리는 TACACS+로 분리했다. 이 구성을 AAA 관점에서 평가하고, 로그 중앙화를 위해 추가할 수 있는 장치를 설명하라.
    

---

# Topic 5: 취약점 관리 라이프사이클

## Explanation

1. **식별(Assess)** – 스캐너·SBOM으로 자산의 CVE를 발견.
    
2. **우선순위(Prioritise)** – CVSS, 비즈니스 영향, 노출 여부로 등급화.
    
3. **조치(Action/Remediate)** – 패치, 설정 변경, WAF 룰, 임시 완화.
    
4. **재평가(Reassess)** – 패치 후 스캔·침투 테스트로 확인.
    
5. **지속 개선(Improve)** – 취약점 발생 원인 분석, 정책·자동화 업데이트. ([PurpleSec](https://purplesec.us/learn/vulnerability-management-lifecycle/?utm_source=chatgpt.com "The Vulnerability Management Lifecycle (5 Steps) - PurpleSec"), [Pathlock](https://pathlock.com/blog/vulnerability-management-lifecycle/?utm_source=chatgpt.com "What is Vulnerability Management Lifecycle? | Six Stages & Tools"))
    

## Practice Questions

1. **(객관식)** CVSS 10.0 취약점이 있으나 방화벽으로 외부 노출이 없다. 우선순위 결정 시 고려해야 할 추가 요소는?  
    a) 패치 파일 크기 b) 내부 망 분리 여부 c) 개발자 인원 수 d) 로그 보관 기간
    
2. **(단답형)** 패치 적용 후 효과를 검증하는 라이프사이클 단계는?
    
3. **(시나리오)** 신규 IoT 디바이스 500대의 펌웨어에서 원격 코드 실행 CVE가 발견됐다. 다단계 취약점 관리 프로세스를 설계하라(식별→…→개선).
    

---

# Topic 6: 로그 관리 베스트 프랙티스 – Syslog & 회전

## Explanation

- **Syslog Severity 0-7**: Emergency(0) > Alert(1) > Critical(2) > Error(3) > Warning(4) > Notice(5) > Info(6) > Debug(7). 중요도 필터링으로 알람 폭주 방지. ([Last9](https://last9.io/blog/what-are-syslog-levels/?utm_source=chatgpt.com "Syslog Levels Made Simple: Why They Matter for Your Logs | Last9"))
    
- **Logrotate & 보존**: 디스크 과다 사용 방지, 규정 준수(예: 1년 보관) 충족.
    
- **SIEM 통합**: 여러 OS·DB·장비 로그를 중앙 수집→상관 분석→실시간 경보.
    
- **분산 vs 중앙 저장**: 이미 노트에 정리된 장단점 + 중앙 수집 시 *전송 암호화(TLS)*와 _무결성 서명_ 필요.
    
- **Tip**: 스케줄러로 “중요도≤Error & (SSH_DENY|Failed login)” 필터링해 슬랙 알림 → 즉시 대응. ([레딧](https://www.reddit.com/r/ccna/comments/5dgrti/memorizing_syslog_severity_levels/?utm_source=chatgpt.com "Memorizing Syslog Severity Levels : r/ccna - Reddit"))
    

## Practice Questions

1. **(객관식)** Syslog Severity 번호가 낮을수록 의미하는 것은?  
    a) 덜 심각 b) 더 심각 c) 테스트 메시지 d) 정확도 감소
    
2. **(단답형)** 다수 장비의 로그를 중앙 분석·알람하는 플랫폼을 무엇이라 부르는가?
    
3. **(시나리오)** 하루 50 GB의 웹 서버 로그가 쌓여 디스크가 가득 찬다. 회전·보존·분석을 동시에 만족시키는 설계를 제안하라.
    

---

# Summary

이 보완 자료는 기존 노트의 “접근 제어·권한·세션·취약점·로그” 파트를 **보안 원칙·모델·프로세스** 관점에서 메워 주어 초보자도 “왜 이렇게 해야 하나?”를 이해할 수 있게 했다.  
더 깊이 배우고 싶다면 다음을 추천!

- NIST SP 800-53 (보안 통제 카탈로그)
    
- OWASP Top Ten & ASVS (웹 세션·권한·로깅 사례)
    
- MITRE ATT&CK Navigator (실제 공격자가 권한·세션·로그를 어떻게 우회하는지)
    

시험 공부든 실무 체크리스트든, 위 개념을 기억하며 **“최소 권한·다중 계층 방어·가시성 확보”** 원칙을 꾸준히 적용해 보길!