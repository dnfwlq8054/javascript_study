study-k8s-chapter-04
4장

* 파드는 배포 가능한 기본 단위
* 실환경에서는 배포한 앱이 자동으로 실행되고 수동적인 개입 없이도 안정적인 상태로 유지되길 원할 것
* 이와 관련하여 시나리오별로 다양한 워크로드를 실습하는 것이 이번 장의 목표
    * https://kubernetes.io/ko/docs/concepts/workloads
    * 디플로이먼트는 9장, 스테이트풀셋은 10장에서 다름

4.1 파드를 안정적으로 유지하기

* 컨테이너가 살아 있는지 확인하는 것: liveness probe (전통적인 표현: 헬스체크)
* 지원 메카니즘: HTTP GET(L7), TCP(L4), Exec(임의의 로직)
    * https://raw.githubusercontent.com/kubernetes/website/main/content/en/examples/pods/probe/exec-liveness.yaml

4.1 라이브니스 실습

* 5번째 요청마다 500에러 던지는 실습용 도커 이미지 준비
* 수동으로 파드를 만드는 yaml 파일 작성
    * https://saycoding.tistory.com/41

$ k create -f ch4-liveness-pod.yaml # 파드 생성
$ k logs -f (pod name) # 파드 로그 확인
$ kgp -w # 파드 상태 와치 모드

#  Restart count, liveness probe, events 확인 설정 확인
$ k describe pod (pod name)

# 종료된 이전 파드 로그 확인
$ k logs (pod name) --previous 

4.1.5 효과적인 라이브니스 프로브 생성

* 운영 환경에서 실행 중인 파드는 반드시 라이브니스 프로프를 정의
* 정의하지 않으면 k8s가 앱이 살아 있는지를 알 수 있는 방법이 없음
* 특정 URL 경로(ex: /health)에 요청하도록 프로브를 구성해 앱 내 실행 중인 모든 구성 요소가 살아 있는지 또는 응답이 없는지 확인하도록 구성 가능
* 앱의 내부만 체크하고, 외부 요인의 영향을 받지 않도록 해야 함
    * 웹서버에서 DB 연결 실패는 웹서버의 라이브니스와 무관함  (재시작해서 해결되지 않으므로)
* 라이브니스 프로브가 너무 많은 연산 리소스를 사용해서는 안 되며, 완료하는데 넘 오래 걸리지 않아야 함
* 기본적으로 비교적 자주 실행되며 1초 내에 완료
* 너무 많은 일 → 컨테이너 속도 저하 (CPU 사용시간 소모)
* 프로브에서 재시도 루프 구현 X (프로브 자체 설정으로 재시도)
* liveness 한 노드 안에서 kubelet을 통해서 관리될 뿐 노드장애시에는 회복이 어려움.
* 이걸 대응하는 것이 다음 챕터



MSA 수준까지 쪼개지 않더라도 애플리케이션 stateless한 구성이어야 되지 않을까?
고팍스에서는 어떤 것들이 가능할까? private-relay에 일부 api들을 하나씩 떼어내기?



4.2 레플리케이션컨트롤러 소개

* RC는 k8s 리소스로서 파드가 항상 실행되도록 보장
* 라이브니스 프로브와 달리 노드 장애 대응 가능



4.2.1 RC의 동작

* 실행중인 파드 목록을 지속적으로 모니터링하고, 특정 레이블 셀렉터와 일치하는 파드 수가 의도하는 수와 일치하는지 항상 확인
* 세가지 구성요소: 1) 관리를 위한  레이블 셀렉터, 2) 복제 수, 3)파드 템플릿
* (yaml) 템플릿은 붕어빵 틀 같은 것
    * 레이블 셀렉터 변경했다고 기존 파드를 모두 수정하는 것 아님
    * 파드 템플릿을 변경했다고 이미 생성된 파드를 수정하는 것 아님


RC 실습 1: 생성

$ k create -f ch4-rc.yaml # yaml 생성 후 적용
$ k get rc
$ kgp

# yaml의 레이블 셀렉터와 파드 템플릿의 레이블이 다를 경우
# api 서버에서 에러 반환
$ k create -f ch4-rc-invalid.yaml 

$ kgp -w
$ k delete pod (node name)
$ k get rc
$ k describe rc wj-node


컨트롤러가 새로운 파드를 생성한 원인 정확히 이해하기

* 컨트롤러가 새 교체 파드 생성 => 삭제 그 자체에 대한 대응이 아니라 결과적인 상태에 대응
    * 결과적으론 같은 말
* RC는 파드가 삭제되면 그 이벤트에 대응해서 대체 파드를 새로 생성하는 개념이 아니라 desired상태에 만족하기 위해서 파드를 생성 
* 선언적 인프라 관리의 중요성


RC 실습 2: 노드 장애 테스트 

* (minikube는 불가)

$ k get nodes
$ k create -f ch4-rc.yaml
$ kgp # node에 분배되었나 확인
# node 하나 shutdown

* 제법 꽤 시간이 지나야 pod이 신규로 생성 됨
    * 더 빨리 안되나?



RC 실습 3: 안팎으로 이동하기

$ k label pod (node) type=special
$ kgp --show-labels
$ k label pod (node) wj-rc=wj-ch4 --overwrite
$ kgp --show-labels
$ k label pod (동일노드) wj-rc=wj-ch4-ec --overwrite

# edit 모드로 label을 직접 하나 추가
$ k edit rc wj-node

# 저장 후 파드를 하나 지면 새로 생성되는 파드에 추가된 라벨 포함
# 붕어빵 틀을 새로 교체하는 것일 뿐 이미 틀로 생성된 파드에도 영향을 주는 건 아냐

# 추가 테스트로 labelselector 변경
# 새로 replica 수만큼 한 번에 생성
$ k edit rc wj-node

$ k scale rc wj-node --replicas=5
$ k edit rc wj-node
$ k delete rc wj-node --cascade=false



4.3 ReplicaSet

* 이제 ReplicationController를 이제는 안쓴다고함 대신 (차세대) ReplicaSet을 쓴다.
    * https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicationcontroller/
* 크게 달라진 점? 좀 더 풍부한 표현식을 사용하는 레이블 셀렉터
    * RC: 특정 레이블이 있는 파드만 매칭
    * RS: 특정 레이블이 없는 파드, 레이블 값과 상관없이 레이블 키만으로 매칭
* 셀렉터 표현식 operator: In, NotIn, Exists, DoesNotExist


RS 실습

$ k create -f ch4-rs.yaml
$ k get rs
$ kgp
$ k describe rs
$ k delete rs (name)



4.4 DaemonSet

* (minikube는 노드가 하나라 실습에 다소 제약이 있음)
* 모든 노드에 노드당 하나의 파드만 실행하기 원하는 경우
    * 예: 시스템 수준 작업을 수행하는 인프라 관련 파드 (로그 수집기, 리소스 모니터)

실습

$ k create -f ch4-ds.yaml
$ k get ds
$ kgp
$ k label node minikube disk=ssd
$ kgp
$ k label node minikube disk=hdd --overwrite



4.5 Job

* 컨테이너 내부에서 실행 중인 프로세스가 성공적으로 완료되면 컨테이너를 다시 시작하지 않는 파드를 생성
* 노드 장애 발생할 경우 잡이 정상종료된 것이 아니라면 다른 노드에서 재실행
* 프로세스 자체 장애(에러코드 리턴)인 경우 잡에서 컨테이서 재실행 여부 설정 가능



실습

$ k create -f ch4-batch.yaml
$ k get jobs
$ k create -f ch4-multi-batch-job.yaml

# minikube에서는 'Error from server (NotFound): the server could not find the requested resource' 뜨면서 안 됨
$ k scale job multi-completion-batch-job --replicas 3



4.6 CronJob

* Job 리소스는 생성 즉시 바로 실행 되어 특정 시간 또는 지정된 간격으로 반복실행하기 원할 경우 사용
* Cron이라 예정된 스케쥴에 생성되어야 하지만 안 될 경우 실패처리를 위해 제약조건을 startingDeadlineSeconds 속성으로 걸 수 있음'

실습

$ k create -f ch4-batch.yaml
$ k get cronjobs.batch
$ k describe cronjobs.batch wj-every-3min-cron-job

study-k8s-chapter-05
5장 Service


K8s에서의 Service는 Loadbalancer의 역할을 한다고 생각하면 됩니다.
비슷한 예로 AWS의 ALB가 있습니다.
저희는 2장에서 이미 kubia-http라는 Service를 만들고 외부에 노출시켜 통신하는 실습을 진행하였습니다.

ReplicationController는 단순하게 Lable을 통해 Pod들을 묶어 Scaling해주는 기능만 제공합니다. (AWS의 TargetGroup과 유사)
따라서 Service와 RC는 다른 개념 입니다.


이름이 지정된 포트 사용

파드에 사용될 컨테이너를 yaml 파일로 정의할 때 port에 대한 name을 정의할 수 있습니다.
그러면 Service같은 layer에서 name으로 정의된 port를 사용할 수 있습니다.

---
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: my-replicaset
  labels:
    app: myapp
    type: frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      type: frontend
  template:
    metadata:
      labels:
        app: myapp
        type: frontend
    spec:
      containers:
      - name: kubia
        image: dnfwlq8054/kubia
        ports:
        - name: http
          containerPort: 8080
        - name: https
          containerPort: 8443
---
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  type: LoadBalancer
  selector:
    app: myapp
    type: frontend
  ports:
    - name: http
      port: 80
      targetPort: http
    - name: https
      port: 443
      targetPort: https

서비스 검색

서비스는 고정 IP와 DNS주소를 갖습니다.
파드는 생성될 때 파드의 환경변수를 검사해 서비스의 IP주소와 포트를 알아냅니다.
하지만 파드가 먼저 생성되고 그 후 서비스가 생성되어도 서비스에 등록할 수 있습니다.
Selector를 사용하면 됩니다.

kubectl exec [pod name] env  명령어를 통해 pod의 환경변수를 조회할 수 있습니다.

파드가 DNS를 통해 서비스를 찾을 땐 FQDN(절대 도메인 네임) 으로 찾습니다.
파드가 생성될 때 컨테이너 안에 /etc/resolve.conf 에 k8s DNS서버가 등록 됩니다.

root@my-replicaset-ppnlj:/# cat /etc/resolv.conf 
search default.svc.cluster.local svc.cluster.local cluster.local ap-northeast-2.compute.internal
nameserver 100.64.0.10
options ndots:5

서비스의 DNS주소는 다음과 같은 형태를 갖고 있습니다.
<서비스명>.<네임스페이스명>.svc.cluster.local
이러한 형태를 FQDN 형태라고 부릅니다.
그리고 같은 네임 스페이스 내에선 <서비스명>만 사용하여 서비스에 접근 할 수 있습니다.
이러한 경우에는 k8s DNS 서비스가 자동으로 FQDN을 완성하게 됩니다.

파드 내에서 서비스의 IP주소로 Ping을 날리면 Ping이 가지 않습니다.
이것은 11장에서 자세하게 다룬다고 했지만....
ICMP는 OSI 7 layer에서 3계층에서 동작하는 프로토콜이고, Service는 4계층 프로토콜 기반으로 동작합니다.
때문에 서비스에서는 ICMP프로토콜을 볼 수 없습니다.


클러스터 외부에 있는 서비스 연결

쿠버네티스 외부에 있는 서비스를 연결할 수 있습니다.
ExteranlName을 사용하면 됩니다.

간단하게 예를 들어 보자면
서드파티로부터 제공받은 데이터베이스가 클러스터 외부에 존재하고, 그 데이터베이스의 URL이 database.example.com이라고 가정합니다. 
이 경우, 우리는 이 데이터베이스를 db-service라는 이름의 ExternalName 서비스로 정의할 수 있습니다. 그러면 클러스터 내부에서는 db-service라는 이름으로 이 데이터베이스에 접근할 수 있게 되고, 이는 database.example.com으로 자동으로 리다이렉션됩니다.

즉, 요약하자면 DNS서버에 CNAME을 적용하는 것과 같습니다. 
때문에 서비스 참조가 단순하며(Ingress, ALB, VPN etc.. 설정이 필요 없음)
실제 주소가 변경되어도 클러스터 내부의 파드나 다른 서비스 설정을 변경할 필요가 없습니다.
(CNAME만 수정해주면 됨으로)
그래서 ExternalName을 사용하면 ClusterIP는 할당 받지 않습니다.


외부에서 서비스로 접근

방법은 총 3가지가 있습니다.

1. 노드포트로 서비스 사용

노드 포트는 30000~32767 사이의 포트를 사용하며, 모든 노드에 동일한 포트를 열어 외부에 노출 시킵니다.
접근하는 방법은 <Node1 IP>:30123, <Node2 IP>: 30123 이런식으로 접근할 수 있습니다.

Kind: Service
spec.type : NodePort

로 정의할 수 있습니다.
노드 포트는 매우 간단한 방식으로 외부 트래픽을 k8s서비스로 라우팅 하지만, 포트 관리가 수동적이고 모든 노드가 동일한 포트를 사용해야 한다는 단점이 있습니다. (방화벽 설정은 덤)


1. LoadBalancer 사용

일반적인 ALB와 동작은 유사합니다. 단, 조건이 있는데 클라우드 제공 업체에서 로드 벨런싱 기능을 지원해야 합니다.
(AWS ALB etc...) 추가로 LoadBalancer는 내부적으로 NodePort를 사용하고 있으며, LoadBalancer는 NodePort로 트래픽을 전달하도록 구성합니다. 때문에 클라우드 환경에 LoadBalancer 서비스를 생성할 수 있으면 NodePort구성은 생략이 가능 하다고 합니다. (https://kubernetes.io/ko/docs/concepts/services-networking/service/)


1. Ingress사용

Ingress는 외부 트래픽을 쿠버네티스 서비스로 라우팅하는 방법으로 OSI 7 Layer 를 지원합니다. Ingress는 HTTP와 HTTPS 트래픽을 라우팅할 수 있으며, URL 기반의 라우팅, SSL 인증서 관리, 세션 유지(persistence) 등의 고급 기능이 있습니다. 
Ingress를 사용하려면 Ingress Controller가 필요하며, Ingress Controller는 로드 밸런서, 웹 애플리케이션 방화벽(WAF), 인증 시스템 등 다양한 형태가 될 수 있습니다.


* K8s 공식 문서에 있는 Architecture
    https://kubernetes.io/ko/docs/concepts/services-networking/ingress/



* AWS EKS Workshop에 있는 Ingress Architecture
    https://archive.eksworkshop.com/beginner/130_exposing-service/ingress/



* K8s ingress config yaml (Ingress-nginx를 사용하는 ingress)

apiVersion: networking.k8s.io/v1
kind: Ingress
 
# Ingress 설정 
metadata:
  name: test
  annotations: 
    nginx.ingress.kubernetes.io/rewrite-target: / #  Ingress-nginx에서 '/'로 리다이렉트
 
spec:
  rules:
  - host: "foo.bar.com" # foo.bar.com 주소에 대한 처리
    http:
      paths:
      - pathType: Prefix # Prefix/Exact/ImplementationsSpecific
        path: "/foos1" # foo.bar.com/foos1 주소에 대한 처리
        backend:
          # foo.bar.com/foos1 주소에 대한 요청을 s1 서비스의 80번 포트로 전송
          service:
            name: s1 
            port: 
              number: 80
 
      - pathType: Prefix 
        path: "/bars2" # foo.bar.com/bars2 주소에 대한 처리
        backend:
          # foo.bar.com/bars 주소에 대한 요청을 s2 서비스의 80번 포트로 전송
          service:
            name: s2
            port: 
              number: 80
 
  - host: "*.foo.com"
    http:
       paths:
        # *.foo.com의 모든 요청을 s2 서비스의 80번 포트로 전송
        - pathType: Prefix
          path: "/"
          backend:
             service:
                name: s2
                port: 
                  number: 80


Ingress는 서비스 단위로 라우팅이 진행됩니다.
위에 ingress yaml 코드 처럼 rules에 의해서 어떠한 Domain name이 어느 서비스로 가는지 결정하게 됩니다.(책 236p)
알맞게 서비스에 도착한 패킷들은 서비스의 기본적인 로드벨런싱 기능을 통해서 각 파드에 전달 됩니다.
(service.spec.type : LoadBalancer 와는 다른 개념 입니다.)

이러한 ingress를 프로비저닝 해줄 수 있는 것이 ingress 컨트롤러입니다.
ingress 컨트롤러는 고정된 IP를 갖고 있으며, 해당 IP를 통해 외부에서 접근할 수 있습니다.
또한 클라우드 환경(AWS, GCP)에서 ingress를 설정한다면, 
AWS ALB Ingress Controller : https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.2/
GCE Ingress Controller : https://github.com/kubernetes/ingress-gce
를 사용해 클라우드 환경의 Load Balancer와 연동시킬 수 있습니다.
(이러한 설정 때문에 EKS, GKE를 사용하는 것 같습니다. 이걸 사용하면 거의 자동으로 해주는 것으로 보여집니다.
또는 Kops를 사용해도 좋을 것 같습니다.)

이렇게 클라우드 ALB와 연동시키면 DNS주소가 나오고, 이를 DNS Server의 CNAME으로 매핑하면 됩니다.

단, 이렇게 될 경우 가용성 문제가 생기는데요.(한개 있는 Ingress 컨트롤러가 Faill이 난다면?)
AWS로 예를 들자면, 컨트롤러를 여러개 생성한 후 Route 53의 DNS 라운드 로빈, DNS 페일오버 등의 
기술을 사용하여 트래픽을 이 컨트롤러들 사이에 분산시킬 수 있다고 합니다.
(또한 Ingress에서 정의한 Domain name은 수동으로 DNS에 등록해야 한다고 하네요. 이것까지 자동으로 되는지는 잘 모르겠습니다.)

그래서 클라우드 환경에서 Ingress를 사용하면 Service의 type LoadBalancer를 사용 안해도 됩니다.


externalTrafficPolicy (Double-hop dilemma)

k8s에서 service type을 NodePort or LoadBalancer로 구성할 때 externalTrafficPolicy option을 줄 수 있습니다.
해당 옵션은 서비스에 도착한 패킷을 파드에 어떤식으로 라우팅 한 것인지 결정하는 옵션입니다.

해당 옵션이 왜 필요한지 설명을 하자면 service type이 NodePort나 LoadBalancer인 경우에,
여러개의 노드중에 패킷이 한 노드에 도착했는데, 해당 노드에 Pod가 없을 경우 다시 라우팅을 합니다.
이러한 이유는 기본적으로 service는 모든 노드에게 균등하게 패킷을 전달하려고 합니다.
이는 쿠버네티스의 철학이라고 합니다.
즉, 패킷이 한번에 Pod에 의해 처리될 수도 있지만, 처리할 Pod가 없을 경우 다음 노드에 있는 Pod로 전달 되기 때문에
Double-hop dilemma 라고 불립니다.

이 문제를 externalTrafficPolicy로 해결할 수 있습니다.
externalTrafficPolicy는 cluster, local 두가지로 나뉩니다.

cluster는 default셋팅으로 위 방식처럼 동작 합니다.
cluster mode는 패킷은 모든 노드에 균등하게 배분할 수 있다 라는 장점이 있지만, 단점이 있습니다.

1. 노드간 패킷이 이동할 때 SNAT를 사용해 Source IP주소가 없어진다. (내부 라우팅을 위해서 SNAT를 사용합니다.)
    (이건 다시 밖으로 나갈 때 NAT 테이블에서 원복 해준다고 합니다. 하지만 pod는 볼 수 없습니다.)

1. Double-hop이 발생할 수 있다.


local 모드는 해당 패킷을 처리할 수 있는 파드가 있는 노드로 직접 갑니다.
때문에 Double-hop문제는 발생하지 않고 Source Ip주소를 볼 수 있지만, 단점이 있습니다.

1. 패킷은 Node간 이동이 불가능 하다. 즉, 한번 노드에 들어가면 해당 패킷을 무조건 처리해야 한다. 
2. 처리하지 못했을 경우 실패를 클라이언트에게 리턴한다. (다른 노드에 처리할 수 있는 파드가 존재해도)
3. 분산에 불균형이 이뤄질 수 있다. (특정 노드에게만 데이터가 갈 수 있기 때문에)


보통 ingress사용하면 service type을 ClusterIP or NodePort를 사용한다고 합니다.
NodePort 타입을 사용한다면 이런 문제는 고민해볼 필요가 있습니다.

externalTrafficPolicy에 대한 상세한 설명은  https://nangman14.tistory.com/72 에 잘 정리되어 있어서 공유합니다.


레디니스 프로브

라이브니스 프로브를 4장에서 배웠습니다.
라이브니스 프로브는 애플리케이션의 ‘생존' 상태를 확인한다면,
레디니스 프로브는 애플리케이션의 준비 상태를 확인합니다.

만약 레디니스 프로브가 실패하면, 파드는 엔드포인트 오브젝트에서 제거 됩니다.
서비스는 해당 파드로 트래픽을 전달하지 않게됩니다.

레디니스는 주로 초기화 중이거나 대기 중인 상태에서 애플리케이션을 보호하거나, DB에 연결할 수 없는 상태인 애플리케이션을 보호하는 등의 용도로 사용 됩니다.

레디니스의 유형은 3가지가 있습니다.

* Exec 프로브 : 컨테이너 내에 지정된 명령어를 실행
* HTTP GET 프로브 : 지정된 포트에서 HTTP GET 요청을 수행하고 응답 상태 코드를 검사.
* TCP 소켓 프로브 : 지정된 포트에 TCP연결을 확인.

