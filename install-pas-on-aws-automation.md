
## aws limit 설정
enlarge resource limit of EC2:
https://docs.pivotal.io/pivotalcf/2-3/customizing/aws.html
```
vpc=> 5 free
vms=>
t2.micro: 50
c4.large: 20
m4.large: 20
r4.large: 20
```

## 설치용 와일드카드 도메인 준비
prepare a wildcard domain for PAS foundation.
```
*.pcfdemo.net
*.apps.pcfdemo.net
*.system.pcfdemo.net
*.uaa.system.pcfdemo.net
*.login.system.pcfdemo.net
```

## 사설인증서 생성하기
ARN을 빠르게 만들기 위해서 sampivotal.com 도메인으로 서명된 사설인증서를 만들어서 AWS Cert Manager에 import했습니다.
사설인증서를 만드는 스크립트는 아래를 참조하세요
https://github.com/myminseok/generate-self-signed-cert


## pivnet 가입.
sign up for network.pivotal.io -> get pivnet_token



## 파이프라인 편집

~~~
git clone https://github.com/pivotal-cf/pcf-pipelines
~~~

### aws seoul region에 맞게 수정
- aws seoul region의 경우 az1, az2만 있으므로 az3제거하기
- NAT AMI변경
- Dev용으로 RDS대신 internal mysql사용하도록 변경
- PAS VM의 instance갯수를 조절할 수 있게 수정

위 내용을 반영한 수정된 파이프라인 참고: https://github.com/myminseok/pcf-pipelines-minseok


## fly cli설치
~~~
fly client download(linux):
wget https://github.com/concourse/concourse/releases/download/v4.2.1/fly_linux_amd64
~~~

### fly 로그인

~~~
fly -t sandbox login -c <concourse-url> -u <username> -p <password> -k 
~~~

## concourse에 파이프라인 생성하기

### for Seoul region( for two azs)
https://github.com/myminseok/pcf-pipelines-minseok

### for Tokyo region
git clone https://github.com/pivotal-cf/pcf-pipelines
cd pcf-pipelines/install-pcf/aws

## edit params.yml
use AMI from opsmanager-aws in network.pivotal.io

비밀번호가 담긴 파일은 별도 파라미터 파일로 관리하거나 credhub에 보관하도록 합니다.
~~~
cd pcf-pipelines/tree/master/install-pcf/aws
fly -t target sp -p install-pcf -c pipeline.yml -l ../../../params-aws.yml
~~~


