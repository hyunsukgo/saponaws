---
description: '#SAP on AWS implementation#S4/HANA'
---

# Installation Steps for SAP S/4HANA 1709\(DEMO\)

## Goal of the Demo:

* SAP on AWS implementation

## Demo Environment:

* AWS, SUSE Linux, S/4HANA 1709

## Requirements for the Demo:

* Prerequisites:
  * Basic understanding of AWS and terms - EC2 \(and related\), VPC, NACL, Security Group, Region, Available Zone, Keypair, and others
* AWS account
  * 12 digits AWS account ID: _**YOUR\_AWS\_ACCOUNT\_ID**_

##  Architecture

* * OS : Suse Linux Enterprise Server for SAP Application 12 SP4
  * SAP : SAP S/4HANA1709 FPS02
  * DB : SAP HANA 2.0 SPS02 Rev20

![](.gitbook/assets/0.png)

## Prerequisties

* * Installation Media list

![](.gitbook/assets/1.png)

![](.gitbook/assets/2.png)

* * SWAP size 설정
* SAP Application Server for Linux \(SAP Notes. 1597355 참조\)

![](.gitbook/assets/3.png)

* SAP HANA DB : 2GB \(1999997 참조\)

![](.gitbook/assets/4.png)

## Installation Steps

* * Installation Steps for SAP HANA \(DB\)

![](.gitbook/assets/5.png)

*  HANA Media 경로에서 ./hdblcm 실행

![](.gitbook/assets/6.png)

* Choose an action : 1. Install
* Select additional components for installation : 2.3 server client

![](.gitbook/assets/7.png)

* Select system usage : 2 test \(운영의 경우 1, 개발의 경우 3 선택\)
* OS Password는 sapadm : qweR123$, &lt;sid&gt;adm : qweR123$
* DB Password 는 SYSTEM : qweR123$

 ![](.gitbook/assets/8.png)-

- SAP HANA Database System installed로 설치 완료 확인.

* * Installation Steps for S/4 HANA \(ASCS\)

![](.gitbook/assets/9.png)

* SWPM 경로에서 SAP AP \(ASCS\)설치 수행 ./sapinst SAPINST\_USE\_HOSTNAME=sapapp IS\_HOST\_LOCAL\_USING\_STRING\_COMPARE=true

![](.gitbook/assets/10.png)

* Ur로\(4237 포트\) 접속하여 SWPM 접속
* SAP S/4HANA Server 1709 &gt; SAP HANA Server &gt;

SAP HANA Database &gt; Installation &gt; High-Availability System &gt;

**ASCS Instance**

![](.gitbook/assets/11.png)

* SAP System ID \(SAPID\) : S4H \(3글자 지정\)
* SAP Mount : /sapmnt

![](.gitbook/assets/12.png)

* DNS Domain : megazone.com

![](.gitbook/assets/13.png)

* Password for All Users \(Master Password\) : QweR123$

![](.gitbook/assets/14.png)

* Password of SAP System Administrator : qweR123$
* UID와 GID는 개발, 품질, 운영서버 모두 동일하게 설정

![](.gitbook/assets/15.png)

![](.gitbook/assets/16.png)

* Kernel 파일 import

![](.gitbook/assets/17.png)

* Saphostagent 파일 import

![](.gitbook/assets/18.png)

![](.gitbook/assets/19.png)

* sapadm password 입력 : qweR123$

![](.gitbook/assets/20.png)

* ASCS instance number와 Hostname 입력

![](.gitbook/assets/21.png)

* Message Server 포트입력 : 3610

![](.gitbook/assets/22.png)

* next

![](.gitbook/assets/23.png)

* next

![](.gitbook/assets/24.png)

* review 정보를 최종 확인 후 수정 필요시 체크하고 수정

![](.gitbook/assets/25.png)

* review 정보를 최종 확인 후 수정 필요시 체크하고 수정

![](.gitbook/assets/26.png)

* review 정보를 최종 확인 후 수정 필요시 체크하고 수정

![](.gitbook/assets/27.png)

* review 정보를 최종 확인 후 수정 필요시 체크하고 수정

![](.gitbook/assets/28.png)

* 설치진행

![](.gitbook/assets/29.png)

* ASCS 설치완료
  * Installation Steps for S/4 HANA \(Database instance\)

![](.gitbook/assets/30.png)

* SWPM 경로에서 ./sapinst 실행

![](.gitbook/assets/31.png)

* SAP S/4HANA Server 1709 &gt; SAP HANA Server &gt; SAP HANA Database &gt; System Copy &gt; Target System &gt; High-Availability System &gt; Based on AS ABAP &gt; **Database Instance**

![](.gitbook/assets/32.png)

* /sapmnt/S4H/profile 경로 입력

![](.gitbook/assets/33.png)

* Message Server Port 3610

![](.gitbook/assets/34.png)

* Master Password : qweR123$

![](.gitbook/assets/35.png)

* SAP user password : qweR123$

![](.gitbook/assets/36.png)

* DB 백업본으로 설치를 위한 homogeneous system copy

![](.gitbook/assets/37.png)

* DB host, Instance Number, SID, SYSTEM password 입력

![](.gitbook/assets/38.png)

* Multi-tenent DB 정보 확인 및 password 입력

![](.gitbook/assets/39.png)

* DB백업을 위한 SYSTEM user password 입력

![](.gitbook/assets/40.png)

* Kernel 파일 import

![](.gitbook/assets/41.png)

* Dependent와 independent Kernel 2개를 import

![](.gitbook/assets/42.png)

* Saphostagent 파일 import

![](.gitbook/assets/43.png)

* Saphostagent 파일 import detected 확인

![](.gitbook/assets/44.png)

* Sapadm 계정 password 입력 \(UID와GID는 다른 인스턴스와 동일\)

![](.gitbook/assets/45.png)

* DB schema 정보 입력

![](.gitbook/assets/46.png)

* Sapcontrol connect data의 정보 입력

![](.gitbook/assets/47.png)

* 설치할 DB백업파일 경로 및 파일 지정

![](.gitbook/assets/48.png)

* HDB Client 설치파일 path 선택 \(Local\)

![](.gitbook/assets/49.png)

* HDB client 설치 미디어 path 지정

![](.gitbook/assets/50.png)

* next

![](.gitbook/assets/51.png)

* parameter Summary 내용 확인 후 변경사항 필요시

해당부분 선택 후 변경

![](.gitbook/assets/52.png)

* Database instance\(DI\) 설치 진행

![](.gitbook/assets/53.png)

* DI설치 완료
  * Installation Steps for S/4 HANA \(Primary Application Server Instance\)

![](.gitbook/assets/54.png)

* ./sapinst

![](.gitbook/assets/55.png)

* SAP S/4HANA Server 1709 &gt; SAP HANA Server

&gt; SAP HANA Database &gt; System Copy &gt; Target System

&gt; High-Availability System &gt; Based on AS ABAP

&gt; **PAS Instance**

![](.gitbook/assets/56.png)

* SAP system profile path 지정

![](.gitbook/assets/57.png)

* Message server 3610 port

![](.gitbook/assets/58.png)

* Master Password : qweR123$입력

![](.gitbook/assets/59.png)

* Sap kernel 및 igs 파일 import

![](.gitbook/assets/60.png)

* 설치 파일 지정 후 detected check

![](.gitbook/assets/61.png)

* Saphostagent 탐지 후 upgrade 선택

![](.gitbook/assets/62.png)

* 업그레이드 설치할 saphostagent 지정

![](.gitbook/assets/63.png)

* Detected된 saphostagent 파일 확인

![](.gitbook/assets/64.png)

* DB host, SID, instance number, password 정보 입력

![](.gitbook/assets/65.png)

* SAP kernel 파일 지정 선택

![](.gitbook/assets/66.png)

* Detected 된 kernel 파일 지정 및 import

![](.gitbook/assets/67.png)

* Saphostagent upgrade 선택

![](.gitbook/assets/68.png)

* saphostagent파일 지정

![](.gitbook/assets/69.png)

* detected package 확인

![](.gitbook/assets/70.png)

* DB SID, host 확인 및 DB instance number, password 입력

![](.gitbook/assets/71.png)

* Multitenant DB 정보 입력

![](.gitbook/assets/72.png)

* Use SAP liveCache integrated in SAP HANA 선택

![](.gitbook/assets/73.png)

* DBACOCKPIT password 입력

![](.gitbook/assets/74.png)

* SAPABAP1 schema password 입력

![](.gitbook/assets/75.png)

* PAS instance number 및 host name 입력

![](.gitbook/assets/76.png)

* Message server 3610 port 입력

![](.gitbook/assets/77.png)

* Do not create message server access control list 선택

![](.gitbook/assets/78.png)

* next

![](.gitbook/assets/79.png)

* ddic password 입력

![](.gitbook/assets/80.png)

* Default Key

![](.gitbook/assets/81.png)

* next

![](.gitbook/assets/82.png)

* summary 내용 확인후 변경사항 필요시 체크박스 선택 수정

![](.gitbook/assets/83.png)

* 설치진행

![](.gitbook/assets/84.png)

* 설치완료
  * Installation Steps for S/4 HANA \(Enqueue Replication Server Instance\)

![](.gitbook/assets/85.png)

* ./sapinst

![](.gitbook/assets/86.png)

SAP S/4HANA Server 1709 &gt; SAP HANA Server &gt; SAP HANA Database

&gt; Installation &gt; High-Availability System &gt; **ERS Instance**

![](.gitbook/assets/87.png)

* Sap system profile path입력

![](.gitbook/assets/88.png)

* ASCS Host 정보 입력 \(SID, Instance number, hostname\)

![](.gitbook/assets/89.png)

* SAP hostagent detected시 upgrade 체크

![](.gitbook/assets/90.png)

* Saphostagent 설치 미디어 path 입력

![](.gitbook/assets/91.png)

* Detected 된 saphostagent 미디어 체크

![](.gitbook/assets/92.png)

* ERS instance number 및 host 정보 입력

![](.gitbook/assets/93.png)

* next

![](.gitbook/assets/94.png)

* summary 내용 확인 후 수정 필요시 해당 내역 체크 후 수정

![](.gitbook/assets/95.png)

* 설치진행

![](.gitbook/assets/96.png)

* 설치완료

