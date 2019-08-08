# HA Configuration using pacemaker

## ASCS,ERS 이중화 구성

### Requirement

### Tagging the EC2 Instances

* pacemaker 어플리케이션에서 리소스를 구분하기 위한 태깅 설정이 필요합니다. 
* AWS CLI Profile

  * 리소스가 직접적인 AWS 서비스 접근을 위한 aws cli config 구성 \(인스턴스 내\)

  ```text
  aws configure --profile=cluster
  ```

### IAM Role and Policy 

{% hint style="info" %}
이중화 설정할 EC2에 맵핑할 IAM Role을 구성해야합니다.
{% endhint %}

#### AWS Data Provider Policy

```yaml
{
	 "Statement": [
		 {
		 "Effect": "Allow",
		 "Action": [
			 "EC2:DescribeInstances",
			 "EC2:DescribeVolumes"
			 ],
				 "Resource": "*"
			 },
		 {
			 "Effect": "Allow",
			 "Action": "cloudwatch:GetMetricStatistics",
			 "Resource": "*"
		 },
		 {
			 "Effect": "Allow",
			 "Action": "s3:GetObject",
			 "Resource": "arn:aws:s3:::aws-data-provider/config.properties"
		 }
	 ]
	}
```

#### STONITH Policy

```yaml
{
    "Version": "2012-10-17",
    "Statement": [
         {
             "Sid": "Stmt1424870324000",
             "Effect": "Allow",
             "Action": [
                 "ec2:DescribeInstances",
                 "ec2:DescribeInstanceAttribute",
                 "ec2:DescribeTags"
                 ],
                 "Resource": "*"
                 },
                 {
                 "Sid": "Stmt1424870324001",
                 "Effect": "Allow",
                 "Action": [
                 "ec2:ModifyInstanceAttribute",
                 "ec2:RebootInstances",
                 "ec2:StartInstances",
                 "ec2:StopInstances"
                 ],
                 "Resource": [
                 "arn:aws:ec2:region-name:aws-account:instance/i-node1",
                 "arn:aws:ec2:region-name:aws-account:instance/i-node2"
                 ]
         }
     ]
}
```

#### Overlay IP Agent Policy

```yaml
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Stmt1424860166260",
            "Action": [
            "ec2:DescribeRouteTables",
            "ec2:ReplaceRoute"
        ],
            "Effect": "Allow",
            "Resource": "arn:aws:ec2:region-name:account-id:route-table/rtb-XYZ"
        }
    ]
}
```

#### Route 53 Updates

```yaml
{
          "Version": "2012-10-17",
          "Statement": [
          {
                 "Sid": "Stmt1471878724000",
                 "Effect": "Allow",
                 "Action": "route53:GetChange",
                 "Resource": "arn:aws:route53:::change/*"
          },
 {
 "Sid": "Stmt1471878724001",
 "Effect": "Allow",
 "Action": "route53:ChangeResourceRecordSets",
 "Resource": "arn:aws:route53:::hostedzone/hosted zone ID/full name"
 },
 {
 "Sid": "Stmt1471878724002",
 "Effect": "Allow",
 "Action": [
 "route53:ListResourceRecordSets",
 "route53:ChangeResourceRecordSets"
 ],
 "Resource": "arn:aws:route53:::hostedzone/hosted zone ID"
 }
 ]
}
```

### Overlay IP Addresses 라우팅 테이블 추가

![](.gitbook/assets/image%20%28115%29.png)

### Enable Cluster Instances to use the Overlay IP

* 설정할 Cluster IP를 필요한 인스턴스에 설정

```text
ip address add OVERLAY-IP dev eth0
```

### Disable the Source/Destination Check

![](.gitbook/assets/image%20%2835%29.png)

### Corosync Configuration

corosync의 경우 pacemaer 데몬의 sync를 위하여 설정합니다.

crm명령어나 sle-ha 명령어를 이용하여 조인 가능하지만, 그렇게 진행할 경우 ssh\(root\) 접근 문제와 멀티 서브넷이 아닐 경우 Join 불가 같은 문제가 발생할 수 있습니다.

> nodelist아래 IP 영역에 이중화할 노드들의 Private IP를 기입하시고 설정해주시면 됩니다.

```yaml
# Please read the corosync.conf.5 manual page
totem {
 version: 2
 token: 5000
 consensus: 7500
 token_retransmits_before_loss_const: 6
 crypto_cipher: none
 crypto_hash: none
 clear_node_high_bit: yes
 interface {
 ringnumber: 0
 bindnetaddr: <ip-local-node>
 mcastport: 5405
 ttl: 1
 }
 transport: udpu
}
 logging {
 fileline: off
 to_logfile: yes
 to_syslog: yes
 logfile: /var/log/cluster/corosync.log
 debug: off
 timestamp: on
 logger_subsys {
 subsys: QUORUM
 debug: off
 }
}
nodelist {
 node {
 ring0_addr: <ip-node-1>
 nodeid: 1
 }
 node {
 ring0_addr: <ip-node-2>
 nodeid: 2
 }
}
quorum {
 # Enable and configure quorum subsystem (default: off)
 # see also corosync.conf.5 and votequorum.5
 provider: corosync_votequorum
 expected_votes: 2
 two_node: 1
}
```

{% hint style="info" %}
&lt;ip-node-1&gt;,&lt;ip-node-2&gt; 은 실제 설정된 IP로 변경해야 합니다. \(가상 IP X 
{% endhint %}

아래 명령어로 cluster 현황을 파악 할 수 있습니다.

```text
crm status
```

![](.gitbook/assets/image%20%2815%29.png)

### Configure Cluster Resources

#### Preparing the Cluster for adding the resources

#### 클러스터 노드에 리소스 핑을 위하여 Maintenance 모드로 변경

```text
crm configure property maintenance-mode="true"
```

#### Configure AWS specific Settings

#### stonith 모니터링 설정

```yaml
property cib-bootstrap-options: \
 stonith-enabled="true" \
 stonith-action="poweroff" \
 stonith-timeout="600s"
rsc_defaults rsc-options: \
 resource-stickiness=1 \
 migration-threshold=3
op_defaults op-options: \
 timeout=600 \
 record-pending=true
```

#### Configuration of AWS specific Stonith Resource

#### 인스턴스 모니터링, tag 및 aws cli 설정을 기반으로 확인

```yaml
primitive res_AWS_STONITH stonith:external/ec2 \
op start interval=0 timeout=180 \
op stop interval=0 timeout=180 \
op monitor interval=120 timeout=60 \
params tag=pacemaker profile=cluster
```

{% hint style="info" %}
5번 라인 tag=pacemaer profile=cluster는 설정한 값에 따라 수정해야 합니다.
{% endhint %}

#### Configure the resources for the ASCS

#### ASCS 리소스 맵핑

* ASCS 실행에 필요한 EFS 경로, VIP, 어플리케이션 실행 상태를 모니터링

```yaml
primitive rsc_fs_HA1_ASCS00 Filesystem \
	 params device="efs-name:/ASCS00" directory="/usr/sap/HA1/ASCS00" \
	 fstype="nfs4" \
	 options="rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2" \
	 op start timeout=60s interval=0 \
	 op stop timeout=60s interval=0 \
	 op monitor interval=200s timeout=40s
primitive rsc_ip_HA1_ASCS00 ocf:suse:aws-vpc-move-ip \
	 params address=192.168.201.116 routing_table=rtb-table \
	 interface=eth0 profile=cluster \
	 op start interval=0 timeout=180 \
	 op stop interval=0 timeout=180 \
	 op monitor interval=60 timeout=60
primitive rsc_sap_HA1_ASCS00 SAPInstance \
	 operations $id=rsc_sap_HA1_ASCS00-operations \
	 op monitor interval=120 timeout=60 on_fail=restart \
	 params InstanceName=HA1_ASCS00_sapha1as \
	 START_PROFILE="/sapmnt/HA1/profile/HA1_ASCS00_sapha1as" \
	 AUTOMATIC_RECOVER=false \
	 meta resource-stickiness=5000 failure-timeout=60 \
migration-threshold=1 priority=10
```

{% hint style="info" %}
* Optional: Including Route 53
* Application이 바라 볼 정보의 IP 정보를 도메인 맵핑
* fullname : 설정할 도메인 정보 입력 \(마지막 .까지 붙혀주셔야 합니다.
* hostedzoneid : ZONE ID \(route 53 콘솔에서 확인 가능\)
{% endhint %}

```yaml
primitive rsc_r53_HA1_ASCS00 ocf:heartbeat:aws-vpc-route53 \
	 params hostedzoneid=route-53-name ttl=10 fullname=name-full. profile=cluster \
	 op start interval=0 timeout=180 \
	 op stop interval=0 timeout=180 \
	 op monitor interval=300 timeout=180
```

#### Configure the Resources for the ERS

```text
primitive rsc_fs_HA1_ERS10 Filesystem \
	 params device="efs-name:/ERS10" directory="/usr/sap/HA1/ERS10" fstype=nfs4 \
	 options="rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2" \
	 op start timeout=60s interval=0 \
	 op stop timeout=60s interval=0 \
	 op monitor interval=200s timeout=40s
primitive rsc_ip_HA1_ERS10 ocf:suse:aws-vpc-move-ip \
	 params address=192.168.201.117 routing_table=rtb-table \
	 interface=eth0 profile=cluster \
	 op start interval=0 timeout=180 \
	 op stop interval=0 timeout=180 \
	 op monitor interval=60 timeout=60
primitive rsc_sap_HA1_ERS10 SAPInstance \
	 operations $id=rsc_sap_HA1_ERS10-operations \
	 op monitor interval=120 timeout=60 on_fail=restart \
	 params InstanceName=HA1_ERS10_sapha1er \
	 START_PROFILE="/sapmnt/HA1/profile/HA1_ERS10_sapha1er" \
	 AUTOMATIC_RECOVER=false IS_ERS=true \
	 meta priority=1000
```

#### Configure the Colocation Constraints between ASCS and ERS

```text
colocation col_sap_HA1_no_both -5000: grp_HA1_ERS10 grp_HA1_ASCS00
	location loc_sap_HA1_failover_to_ers rsc_sap_HA1_ASCS00 \
	 rule 2000: runs_ers_HA1 eq 1
	order ord_sap_HA1_first_start_ascs Optional: rsc_sap_HA1_ASCS00:start \
	 rsc_sap_HA1_ERS10:stop symmetrical=false
```

#### 최종 적인 hawk 대시 보드 상태

![](.gitbook/assets/image%20%284%29.png)

![](.gitbook/assets/image%20%2879%29.png)

## HANA DB Cluster

#### corosync configuration

corosync의 경우 pacemaer 데몬의 sync를 위하여 설정합니다.

crm명령어나 sle-ha 명령어를 이용하여 조인 가능하지만, 그렇게 진행할 경우 ssh\(root\) 접근 문제와 멀티 서브넷이 아닐 경우 Join 불가 같은 문제가 발생할 수 있습니다.

> nodelist아래 IP 영역에 이중화할 노드들의 Private IP를 기입하시고 설정해주시면 됩니다.

```text
# Please read the corosync.conf.5 manual page
totem {
 version: 2
 token: 5000
 consensus: 7500
 token_retransmits_before_loss_const: 6
 crypto_cipher: none
 crypto_hash: none
 clear_node_high_bit: yes
 interface {
 ringnumber: 0
 bindnetaddr: <ip-local-node>
 mcastport: 5405
 ttl: 1
 }
 transport: udpu
}
 logging {
 fileline: off
 to_logfile: yes
 to_syslog: yes
 logfile: /var/log/cluster/corosync.log
 debug: off
 timestamp: on
 logger_subsys {
 subsys: QUORUM
 debug: off
 }
}
nodelist {
 node {
 ring0_addr: <ip-node-1>
 nodeid: 1
 }
 node {
 ring0_addr: <ip-node-2>
 nodeid: 2
 }
}
quorum {
 # Enable and configure quorum subsystem (default: off)
 # see also corosync.conf.5 and votequorum.5
 provider: corosync_votequorum
 expected_votes: 2
 two_node: 1
}
```

아래 명령어로 cluster 현황을 파악 할 수 있습니다.

```text
crm status
```

* VIP 설정 \(빨간 색으로 표시된 부분은 시스템에 맞게 변경\)

  ```text
  primitive res_AWS_IP ocf:suse:aws-vpc-move-ip \
          params ip=10.20.0.3 routing_table=rtb-0e491c46c7577ade5 interface=eth0 profile=cluster \
          op start interval=0 timeout=180 \
          op stop interval=0 timeout=180 \
          op monitor interval=60 timeout=60 \
          meta target-role=Started
  ```

* stonith 설정 \(빨간 색으로 표시된 부분은 시스템에 맞게 변경\)

  ```text
  rimitive res_AWS_STONITH stonith:external/ec2 \
          op start interval=0 timeout=180 \
          op stop interval=0 timeout=180 \
          op monitor interval=120 timeout=60 \
          meta target-role=Started \
          params tag=pacemaker profile=cluster
  ```

* SAP Hana Topology 설정 \(빨간 색으로 표시된 부분은 시스템에 맞게 변경\)

  ```text
  primitive rsc_SAPHanaTopology_HDB_HDB00 ocf:suse:SAPHanaTopology \
          operations $id=rsc_sap2_HDB_HDB00-operations \
          op monitor interval=10 timeout=600 \
          op start interval=0 timeout=600 \
          op stop interval=0 timeout=300 \
          params SID=HDB InstanceNumber=00
  ```

* SAP HANA \(빨간 색으로 표시된 부분은 시스템에 맞게 변경\)

  ```text
  primitive rsc_SAPHana_HDB_HDB00 ocf:suse:SAPHana \
          operations $id=rsc_sap_HDB_HDB00-operations \
          op start interval=0 timeout=3600 \
          op stop interval=0 timeout=3600 \
          op promote interval=0 timeout=3600 \
          op monitor interval=60 role=Master timeout=700 \
          op monitor interval=61 role=Slave timeout=700 \
          params SID=HDB InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=true
  ```

* Multi-State 설정 클러스터 노드들의 수량을 조정\(빨간 색으로 표시된 부분은 시스템에 맞게 변경\)

  ```text
  ms msl_SAPHana_HDB_HDB00 rsc_SAPHana_HDB_HDB00 \
          meta clone-max=2 clone-node-max=1 interleave=true target-role=Started
  ```

* Clone 설정, 각 노드들의 복제 설정 정보에 대한 부분을 명시하는 설정

  ```text
  clone cln_SAPHanaTopology_HDB_HDB00 rsc_SAPHanaTopology_HDB_HDB00 \
          meta clone-node-max=1 interleave=true
  ```

* Colocation 설정

  ```text
  colocation col_saphana_ip_HDB_HDB00 2000: res_AWS_IP:Started msl_SAPHana_HDB_HDB00:Master
  ```

* Order 설정

  ```text
  order ord_SAPHana_HDB_HDB00 Optional: cln_SAPHanaTopology_HDB_HDB00 msl_SAPHana_HDB_HDB00
  ```

#### 설정 완료 화면

![](.gitbook/assets/image%20%28113%29.png)

