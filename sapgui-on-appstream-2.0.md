---
description: '#SAPGUI #AppStream 2.0'
---

# SAPGUI on AppStream 2.0

## Create network resources

## Create AppStream 2.0 image builder

> AppStream 2.0 은 EC2인스턴스를 사용하여 응용 프로그램을 스트리밍합니다. AppStream 2.0이 제공하는 Image Builder라고 하는 기본 이미지에서 인스턴스를 실행합니다. 사용자 지정 이미지를 만들려면 Image Builder인스턴스를 연결하고 어플리케이션 설치 및 구성을 진행 다음 Image Assistant를 이용하여 Snapshot을 만들어 이미지를 만듭니다.

### Image Builder 선택

| Option | Value |
| :--- | :--- |
| Name | 사용할 Image Builder의 이름 |
| Display Name |  |
| Instance Type | 인스턴스 타입을 설정할 수 있습니다. \(Default : General Purpose\) |
| Instance Family | 인스턴스 세대군\( stream.standard.medium 추천\) |

![image builder](.gitbook/assets/image%20%28102%29.png)

![image builder &#xC2E4;&#xD589;](.gitbook/assets/image%20%2810%29.png)

![](.gitbook/assets/image%20%288%29.png)

![](.gitbook/assets/image%20%2841%29.png)

### Image Builder 환경 구성

| Option | Value |
| :--- | :--- |
| Default Internet Access | SAPGUI 설치파일을 원할하게 다운 받기위하여 Enable |
| VPC | SAP Application이 설치되어 있는 VPC 선택\(당연?\) |
| Subnet  | 인터넷 연결을 위한 Subnet 할당 |
| Security Group | 아무거나 선택하여도 무방 \(default\) |
| Active Directory Domain | 이 설정은 필요 없습니다. |

![](.gitbook/assets/image%20%2846%29.png)

![](.gitbook/assets/image%20%2833%29.png)

## Connect to the image builder and install application

![](.gitbook/assets/image%20%28103%29.png)

### Image Builder 연결

![](.gitbook/assets/image%20%2849%29.png)

![](.gitbook/assets/image%20%2861%29.png)

### Administrator로 접속

![](.gitbook/assets/image%20%2876%29.png)

## Configure application

![](.gitbook/assets/image%20%285%29.png)

> SAPGUI를 다운로드합니다.
>
> * SAP GUI software -[http://support.sap.com/swdc](http://support.sap.com/swdc)

![](.gitbook/assets/image%20%2858%29.png)

#### SAP GUI 인스톨러 설치

![](.gitbook/assets/2019-05-29-4.46.27.png)



![](.gitbook/assets/image%20%2837%29.png)

![](.gitbook/assets/image%20%2880%29.png)

![](.gitbook/assets/image%20%28101%29.png)

![](.gitbook/assets/image%20%28106%29.png)

## Use image assistant to  create AppStream 2.0 image

### SAP GUI 실행 파라미터 설정

#### Launch Path : SAPGUI 실행 파일을 지

#### Launch Parameters : App인스턴스 사설 IP정보 입력, SID

![](.gitbook/assets/image%20%2838%29.png)

![App &#xCD94;&#xAC00; &#xD655;&#xC778;](.gitbook/assets/image%20%2816%29.png)

#### Switch User를 이용하여 App이 다양한 계정 환경에서 운영이 가능합니다.

![](.gitbook/assets/image%20%2866%29.png)

![](.gitbook/assets/image%20%2883%29.png)

#### 실제 App 실행

![App Launch](.gitbook/assets/image%20%2898%29.png)

![](.gitbook/assets/image%20%2891%29.png)

#### 다음과 같이 정상적으로 실행되는 것을 확인하였습니다.

![](.gitbook/assets/image%20%2848%29.png)

{% hint style="info" %}
* Name : 생성된 이미지를 구분하게 할 수 있는 이름
* Display Name : App 실행 시에 보여지는 이
{% endhint %}

![](.gitbook/assets/image%20%2822%29.png)

#### 설정 확인 후 이미지 생

![](.gitbook/assets/image%20%2844%29.png)

![](.gitbook/assets/image%20%2872%29.png)

#### 다음과 같이 이미지가 생성되는 것을 확인하였습니다.

![](.gitbook/assets/image%20%2832%29.png)

![](.gitbook/assets/image%20%2896%29.png)

## Provision a fleet

![](.gitbook/assets/image%20%2836%29.png)

| Option | Value |
| :--- | :--- |
| Name | Fleet이름 |
| Display Name | AWS Console상에서 보여지는 이름 |
| Description | 설 |

![](.gitbook/assets/image%20%281%29.png)

![&#xC774;&#xBBF8;&#xC9C0; &#xC120;&#xD0DD;](.gitbook/assets/image%20%2894%29.png)

![](.gitbook/assets/image%20%282%29.png)

{% hint style="info" %}
Fleet Type은 두가지 값을 설정 할 수 있습니다. User들의 사용량을 확인하여 설정하는 것을 추천합니다.

* Always-On : Fleet을 항상 작동 시킴
* On-Demand : 사용한 만큼만 비용 부
{% endhint %}

![](.gitbook/assets/image%20%2859%29.png)

![](.gitbook/assets/image%20%2864%29.png)

![](.gitbook/assets/image%20%2884%29.png)

![](.gitbook/assets/image%20%2868%29.png)

![](.gitbook/assets/image%20%2897%29.png)

## Create an AppStream 2.0 stack and a streaming URL

![](.gitbook/assets/image%20%28113%29.png)

![](.gitbook/assets/image%20%2820%29.png)

![](.gitbook/assets/image.png)

![](.gitbook/assets/image%20%286%29.png)

![](.gitbook/assets/image%20%2857%29.png)

![](.gitbook/assets/image%20%2871%29.png)

## Manage user access with an AppStream 2.0 user pool

![](.gitbook/assets/image%20%2813%29.png)

![](.gitbook/assets/image%20%2825%29.png)

![](.gitbook/assets/2019-07-02-3.03.19.png)

![](.gitbook/assets/image%20%2855%29.png)

![](.gitbook/assets/image%20%2892%29.png)

![](.gitbook/assets/image%20%2882%29.png)

