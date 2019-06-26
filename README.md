---
description: '#SAP #AWS #Pacemaker'
---

# SAP on AWS

## SAP on AWS Architecture

* OS : Suse Linux Enterprise Server for SAP Application 12 SP4
* SAP : SAP S/4HANA1709 FPS02
* DB : SAP HANA 2.0 SPS02 Rev20

![](.gitbook/assets/untitled-c43150c0-c298-47e4-937b-cf3bc7c918d8.png)

{% hint style="info" %}
위 구성은 AWS에서 권고하는 아키텍처를 기반으로 구성하였습니다.
{% endhint %}

{% tabs %}
{% tab title="ASCS" %}
ASCS \(ABAP SAP Central Service\) : Message Server를 가지고 LoadBalaning
{% endtab %}

{% tab title="ERS" %}
ERS \(Enqueue Replication Server\) : 고가용성을 위해 Lock Table 복제 역할
{% endtab %}

{% tab title="PAS" %}
PAS\(Primary Application Server\) : 첫 번째로 설치되는 Application Server
{% endtab %}

{% tab title="AAS" %}
HANA\(High Performance analytic Appliance\) : SAP in-memory DB
{% endtab %}

{% tab title="HANA" %}
HANA\(High Performance analytic Appliance\) : SAP in-memory DB
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="EC2" %}
각각의 서비스들이 설치되는 환
{% endtab %}

{% tab title="EFS" %}
사용할 라이브러리나 명령어들을 공유하기 위해 사용
{% endtab %}

{% tab title="EBS" %}
 파일들이 설치될 디스크 볼
{% endtab %}

{% tab title="S3" %}
데이터 백업 용도로 사
{% endtab %}

{% tab title="SSM" %}
Session Manager등 시스템 관리를 위한 AWS 서비
{% endtab %}

{% tab title="AppStream 2.0" %}
어플리케이션 가상화 : 주로 SAPGUI 연동에 사
{% endtab %}

{% tab title="VPN" %}
Op-Premise 환경에서 접근하기 위
{% endtab %}

{% tab title="VPC Peering" %}
VPC 간 통신을 위하여 설정
{% endtab %}
{% endtabs %}

