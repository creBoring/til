# SSL/TLS Configuration

AWS에서는 ACM이라는 SSL 인증서를 발급 및 사용할 수 있게 해주는 서비스가 존재합니다.<br>
이 서비스는 여러 서비스에서 사용할 수 있으며, 대표적으로 **CloudFront, Elastic Load Balancer** 에서 트래픽 구간 암호화를 위해 사용됩니다.

>ACM과 함께 사용되는 AWS 서비스들<br>
https://docs.aws.amazon.com/ko_kr/acm/latest/userguide/acm-services.html

서비스가 아래와 같은 인프라로 구성되어 있다고 했을 때, 사용자는 CloudFront, ELB, EC2 총 3 군데에서 암호화를 진행할 수 있습니다.

```
사용자 → CloudFront → Elastic Load Balancer → EC2
```

이 때 관리자는 위의 모든 구간을 암호화할 수도 있으며, 간단히 가장 앞단의 CloudFront에서만 암호화를 진행할 수도 있습니다.
즉, 다음과 같은 두가지의 시나리오를 general 하게 사용합니다.

### 심플한 구성

사용자 입장에서는 앞단의 CloudFront 에서만 SSL 인증 처리를 해도 페이지 전체가 인증 되어 보여집니다. 그렇기 때문에 간단히 구성하고 싶으신 분들은 아래와 같이 CloudFront에서만 SSL 통신을 처리하고 하단에는 HTTP로 통신하게끔 구성할 수 있습니다.

![Simple](/images/AWS/CloudFront/ssl-tls-configuration-simple.png)


### 권고되는 구성

보안을 더 강화하고 싶으신 경우, 가장 상단에 위치하는 CloudFront만 SSL 통신을 이용할 것이 아니라, 모든 구간에서 SSL 통신을 이용하실 수 있습니다. 이의 경우 위에 설명드린 심플한 구성보다 더 안전하게 암호화된 통신이 가능합니다.

![Recommend](/images/AWS/CloudFront/ssl-tls-configuration-recommend.png)


- 모든 구간에 같은 SSL 인증서를 사용할 필요는 없습니다. 심지어 도메인까지 달라도 상관없습니다.
CloudFront에서 사용하는 도메인과 ELB에서 사용하는 도메인을 달리하고 싶은 경우, ELB의 도메인에 실제 사용할 도메인을 CName으로 연결한 후 CloudFront에서 ELB를 바라볼 때 해당 도메인을 사용하면 됩니다.<br>
(이 때 CloudFront에서는 host 헤더값을 넘겨주면 안됩니다.)

>작성일 : 2020-06-22
