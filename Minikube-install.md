# Minikube 실습 

### Minikube 설치 환경

노트북 PC에 Virtualbox를 설치 후 외부 인터넷 연결 및 내부 Host/Guest OS 간 통신을 위해서 Host Only Adapter 및 Nat Network 구성
Guest OS 로 Ubuntu 설치
Minikube를 추가 가상화 환경없이 on-premise 노드 환경으로 설치하기 위해 Docker 및 kubectl 를 설치
Minikube를 이용하여 단일 노드 쿠버네티스 클러스터를 설치
Minikube는 다음과 같은 쿠버네티스 환경을 제공
  - DNS
  - NodePort
  - ConfigMap 과 Secret
  - Dashboard
  - 컨테이너 Runtime : Docker, CRI-O, containerd
  - CNI (Container Network Interface) 사용
  - Ingress

Minikube 로 설치된 Kubenetes 환경에서 샘플 Pod를 배포하여 실행을 확인


### Ubuntu apt 패키지 Index 업데이트

```
sudo sudo apt-get update
```



### HTTPS를 경유하여 레파지토리를 사용할 수 있도록 다음 명령을 실행하여 패키지를 설치

```
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```



### Docker 의 공식 GPG 키를 추가한다. 올바르게 등록되면 ‘OK’가 표시

```java
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```



### Docker 의 공식 GPG 키가 추가된 것을 확인

```java
sudo apt-key fingerprint 0EBFCD88
```



### Docker 의 레파지토리를 추가하고 등록이 되었으면 apt의 업데이트

```java
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

sudo apt-get update

```



### 설치 index에서 docker-ce 목록 확인

```java
apt-cache madison docker-ce
```



### 설치를 원하는 버전을 지정하여 설치(ex : 18.09.0 버전)

```java
sudo apt-get -y install docker-ce=5:18.09.9~3-0~ubuntu-xenial docker-ce-cli=5:18.09.9~3-0~ubuntu-xenial containerd.io
```



### 설치 완료 후 버전, 실행환경 조회

```java
docker version 
```



### user 에게 docker 실행 권한 부여

```java
sudo groupadd docker
sudo gpasswd -a ${USER} docker
sudo usermod -a -G docker $USER
  
exit
```



### Hello world 컨테이너 실행 테스트

```java
docker run hello-world
  
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:c3b4ada4687bbaa170745b3e4dd8ac3f194ca95b2d0518b417fb47e5879d9b5f
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/  
```



### Curl 명령으로 kubectl 실행파일 다운로드

```java
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```



### 파일 실행 권한 부여

```java
chmod +x kubectl 
```



### 로컬 PATH 로 실행파일 이동

```java
sudo mv ./kubectl /usr/local/bin/kubectl 
```



### 실행 확인

```java
sudo kubectl version 
  
Client Version: version.Info{Major:"1", Minor:"16", GitVersion:"v1.16.2", GitCommit:"c97fe5036ef3df2967d086711e6c0c405941e14b", GitTreeState:"clean", BuildDate:"2019-10-15T19:18:23Z", GoVersion:"go1.12.10", Compiler:"gc", Platform:"linux/amd64"}
The connection to the server localhost:8080 was refused - did you specify the right host or port?  
```



### Curl 명령으로 minikube 실행파일 다운로드

```java
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 
```



### 파일 실행 권한 부여

```java
chmod +x minikube 
```



### 로컬 PATH 로 설치

```java
sudo install minikube /usr/local/bin
```



###      Minikube 실행 
 ( 쿠버네티스 컴포넌트를 VM이 아닌 서버호스트에서 동작하도록 –vm-driver=none 옵션 지원)  

```java
sudo minikube start --vm-driver=none
  
😄  minikube v1.4.0 on Ubuntu 16.04
💣  The "none" driver requires root privileges. Please run minikube using 'sudo minikube --vm-driver=none'.
ubuntu@minikube-test:~$ sodu minikube start --vm-driver=none
sodu: command not found
ubuntu@minikube-test:~$ sudo minikube start --vm-driver=none
😄  minikube v1.4.0 on Ubuntu 16.04
🤹  Running on localhost (CPUs=2, Memory=3951MB, Disk=39642MB) ...
ℹ️   OS release is Ubuntu 16.04.6 LTS
🐳  Preparing Kubernetes v1.16.0 on Docker 18.09.9 ...
💾  Downloading kubelet v1.16.0
💾  Downloading kubeadm v1.16.0
🚜  Pulling images ...
🚀  Launching Kubernetes ...
🤹  Configuring local host environment ...

⚠️  The 'none' driver provides limited isolation and may reduce system security and reliability.
⚠️  For more information, see:
👉  https://minikube.sigs.k8s.io/docs/reference/drivers/none/

⚠️  kubectl and minikube configuration will be stored in /home/ubuntu
⚠️  To use kubectl or minikube commands as your own user, you may need to relocate them. For example, to overwrite your own settings, run:

    ▪ sudo mv /home/ubuntu/.kube /home/ubuntu/.minikube $HOME
    ▪ sudo chown -R $USER $HOME/.kube $HOME/.minikube

💡  This can also be done automatically by setting the env var CHANGE_MINIKUBE_NONE_USER=true
⌛  Waiting for: apiserver proxy etcd scheduler controller dns
🏄  Done! kubectl is now configured to use "minikube" 
```



### Minikube 실행 확인

```java
minikube version 및 minikube status 
```



### Minikube Addon 컴포넌트 활성화 (Ingress, Dashboard 활성화)

```java
sudo minikube addons list
sudo minikube addons enable ingress
sudo minikube addons enable dashboard
```



## Pod 배포  

### 1.Namespace 생성

```java
$ sudo kubectl create namespace example
$ sudo kubectl get namespaces
```



### Java 8 install

```java
sudo apt-get update

sudo apt install -y openjdk-8-jdk

java -version
openjdk version "1.8.0_212"
OpenJDK Runtime Environment (build 1.8.0_212-8u212-b03-0ubuntu1.18.04.1-b03)
OpenJDK 64-Bit Server VM (build 25.212-b03, mixed mode)
```



### Sample app Compile

```java
git clone https://github.com/cloudfoundry-samples/spring-music.git

cd spring-music/

./gradlew clean assemble
```



### Dockerfile

```java
cd ..

cp ./spring-music/build/libs/spring-music-1.0.jar .

vi Dockerfile
  
FROM openjdk:8-jdk-alpine
RUN mkdir /apps
COPY ./spring-music-1.0.jar /app/
WORKDIR /app
EXPOSE 8080
CMD ["java","-jar","/app/spring-music-1.0.jar"]  
```



### Docker Image Build

```java
sudo docker image build -t exam/hello:latest .
```



### Docker Image 생성 확인

```java
docker images |grep hello
```



### Pod 배포를 위한 Deployment.yml 작성 및 적용

```java
vi 10.exam_deployment.yml
  
apiVersion: apps/v1
kind: Deployment
metadata:
  generation: 1
  labels:
    run: exam-hello
  name: exam-hello
  namespace: example
spec:
  replicas: 3
  selector:
    matchLabels:
      run: exam-hello
  template:
    metadata:
      labels:
        run: exam-hello
    spec:
      containers:
      - image: exam/hello:latest
        imagePullPolicy: Never
        name: exam-hello
        ports:
        - containerPort: 8080
          protocol: TCP
      restartPolicy: Always

sudo kubectl apply -f 10.exam_deployment.yml
```



### 생성된 Pod 조회 및 Deployment 조회

```java
sudo kubectl get pods -n example

sudo kubectl get deployments -n example

```



### Pod 라우팅을 위한 Service.yml 작성 및 적용

```java
vi 11.exam_service.yml
  
apiVersion: v1
kind: Service
metadata:
  labels:
    run: exam-hello
  name: exam-hello
  namespace: example
spec:
  ports:
  - port: 8080
    protocol: TCP
    targetPort: 8080
  selector:
    run: exam-hello
  type: ClusterIP

sudo kubectl apply -f 11.exam_service.yml
```



### 생성된 Service 조회

```java
sudo kubectl get services
```



### 외부에서의 요청을 전달하기 위한 Ingress.yml 작성 및 적용

```java
vi 12.exam_ingress.yml
  
apiVersion: networking.k8s.io/v1beta1 # for versions before 1.14 use extensions/v1beta1
kind: Ingress
metadata:
  name: exam-hello
  namespace: example
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - host: exam-hello.ingress
    http:
      paths:
      - path: /|/(.+)
        backend:
          serviceName: exam-hello
          servicePort: 8080

sudo kubectl apply -f 12.exam_ingress.yml

```



### 생성된 Ingress 조회

```java
sudo kubectl get ingress -n example
```



### Ingress 에 설정된 host URL 로 거래 요청을 위하여 DNS 설정대신 /etc/hosts에 Minikube IP 및 Ingress 설정 Host 명 등록

```java
sudo vi /etc/hosts
10.0.2.180   exam-hello.ingress

```



### Curl 명령으로 URL 요청 확인

```java
curl exam-hello.ingress
  
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" class="en" ng-app="SpringMusic">
<head>
...
```



### 외부 브라우져에서 Ingress 에 설정된 host URL로 거래 요청 및 확인 (외부 /etc/hosts에도 exam-hello.ingress IP 지정 필요)

```java
Minikube Dashboard URL 확인 및 브라우저로 조회
 
```



### 조회된 URL 를 Host OS(Windows10) 에서 브라우져로 접속해서 Dashboard 화면을 로딩(GuestOS IP 및 kubectl proxy 포트로 접속)













