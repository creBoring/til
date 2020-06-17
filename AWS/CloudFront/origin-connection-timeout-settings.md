# Origin connection timeout settings

CloudFront의 Timeout 세팅에는 총 4개의 옵션 값이 존재합니다.

- Origin Connection Attempts **(new)**
- Origin Connection Timeout **(new)**
- Origin Response Timeout
- Origin Keep-alive Timeout

'Origin CloudFront Attempts' 와 'Origin Connection Timeout' 옵션은 2020-06-11 에 신규로 추가되었습니다.

> Amazon CloudFront, 구성 가능한 오리진 연결 시도 및 오리진 연결 제한 시간 지원<br>
https://aws.amazon.com/ko/about-aws/whats-new/2020/06/amazon-cloudfront-enables-configurable-origin-connection-attempts-and-origin-connection-timeouts/

### Origin Connection Attempts
CloudFront가 Origin을 대상으로 TCP Connection 요청을 몇번까지 보낼 지 정하는 값입니다.

### Origin Connection Timeout
CloudFront가 Origin을 대상으로 TCP Connection을 보낼 간격을 의미합니다.
위에서 설명한 **Origin Connection Attempts** 가 2 이상일 경우, 요청 사이의 간격은 **Origin Connection Timeout** 초를 사용합니다.

### Origin Response Timeout
CloudFront가 Origin으로부터 트래픽을 요청하고 응답을 정상적으로 받기까지의 총 시간을 제한할 때 사용하는 Timeout 입니다.

### Origin Keep-alive Timeout
CloudFront가 Origin과 Connection이 맺어진 이후, 이 Connection을 얼마나 유지할 것인지에 대한 Timeout 입니다.

---

### Explain

CloudFront에서는 Origin과의 연결을 원활히 하기 위해 Timeout 값을 지정하고 재연결 시도 회수를 지정해 성능 저하를 대비합니다.

우선, CloudFront는 Origin과 통신하기 위해 가장 먼저 TCP Connection을 시도하는데, 이 때 CloudFront는 이 TCP Connection 요청을 Origin에게 **Origin Connection Timeout** 초를 간격으로 **Origin Connection Attempts** 회 시도합니다. 예를들어 **Origin Connection Timeout** 이 10이고 **Origin Connection Attempts** 가 3이라면, 10초 간격으로 총 3번 Connection을 시도한다는 것을 의미합니다. (모두 Timeout이 발생한다면 총 30초가 소요되는 셈입니다)

첫번째 요청 이후 두번째 요청을 보내야 될 시기가 왔는데도 첫번째 요청에 대한 응답이 없으면, 첫번째 요청은 Timeout으로 처리하고 두번째 요청으로 넘어가게 됩니다.
그렇게 총 3번을 모두 실패하게 되면 CloudFront는 Gateway Timeout 값을 내보내게 됩니다.

그리고 만약 TCP Connection이 맺어졌다 하더라도, 하나의 Connection에서 데이터를 응답받는데 **Origin Keep-alive Timeout** 초 보다 오래걸리게 된다면 Timeout이 발생합니다.

마지막으로 이 모든 시간을 포함하여 CloudFront가 Origin으로부터 정상적인 응답을 받기 까지의 시간을 **Origin Response Timeout** 이라는 값으로 지정합니다. 아직 **Origin Connection Timeout** 과 **Origin Connection Attempts** 의 Timeout 값이 남았더라도 이 시간이 **Origin Response Timeout** 값을 초과하게 되면 Timeout이 발생합니다.

---

### NEW Options

Origin Connection Attempts 옵션과 Origin Connection Timeout 옵션이 2020-06-11 일자로 추가됨으로써, 조금 더 Origin 서버의 환경에 맞게 Timeout 설정이 가능해졌습니다.

위 두 옵션이 원래 존재하지 않았던 것이 아닙니다.<br>
위 두 옵션은 default로
```
Origin Connection Attempts = 3
Origin Connection Timeout = 10
```
으로 고정되어 있었습니다. (RFC에 따른 표준)

이 때문에 고객의 서버(Origin) 에서 속도 개선을 위해 Timeout을 2초로 지정해두었더라도, Origin Connection Timeout 값 때문에 영락없이 10초를 다 기다리게 되는 상황이었습니다.

이제는 이 Timeout 값을 조정할 수 있게 됨으로써 고객사의 서버 환경에 맞게 변경할 수 있게 되었습니다.


> 작성일 : 2020-06-17
