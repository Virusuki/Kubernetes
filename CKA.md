## Background
클라우드 네이티브에서 점점 더 중요한 역할을 하고 있으므로, 신속한 확장을 요하는 클라우드 네이티브 애플리케이션을 호스팅하는데 이상적인 플랫폼이다.
K8S라고도 하며, Kubernetes는 컨테이너화된 application의 배포, 확장 및 관리하는 서비스 중심의 아키텍처로 변화되면서 컨테이너를 오케스트레이션하는 
쿠버네티스의 중요성이 높아졌습니다. MicroService와 데브옵스(Devops) 환경에서 application을 빠르게 개발 및 배포하는 애자일 방법론을 적용하기 위해
쿠버네티스와 다른 요소 간의 기술을 제공하는 클라우트 네이티브 오픈소스 S/W 필수적이며, 클라우드 제품이 실행되는 컨테이너가 기준이 되었습니다.

### Who is it for?
해당 CKA 인증은 쿠버네티스 관리자, 클라우드 관리자 및 쿠버네티스 인스턴스를 관리 및 기타 IT 전문가로 생각해볼수가 있습니다.

## CKA을 위한 요구되는 지식
- 쿠버네티스 리소스: 워크로드 오브젝트
	--> POD(고래 떼를 일컫음) : 하나 이상의 컨테이너로 구성, 스케일링의 단위, application 친숙(환경변수 / 정상 여부 상태 검사 정의 등 용이하다)
	--> 컨테이너 네트워크 인터페이스 (CNI) : 파드 간의 연결
	: 컨테이너 런타임(예:도커)은 CNI 플러그인 실행파일(예:Weave Net)을 호출하여 컨테이너의 네트워킹 네임스페이스에 인터페이스 추가/제거

- 쿠버네티스 리소스: 컨트롤러 오브젝트
	-> ReplicaSet: (Node 고장/ Pod삭제 등 발생 시) 파드 복제하여 파드 개수 유지
	-> Delpoyment: old 버전 -> new 버전으로 복제, 레플리카셋 관리
	-> DaemonSet:
		√ 모든 노드에 동일한 파드를 실행시키고 싶은 경우에 활용
		√ 리소스 모니터링, 로그 수집기 등 유용, 클러스터에 노드 추가,삭제 되면 자동으로 파드도 생성/삭제 됨

- 쿠버네티스 리소스: 컨트롤러 오브젝트

## Features
In addition to the latest Neuroglancer features, KNeuroViz adds:
- Segmentation ID list window
- 3D Neuron mesh export .ctm or .obj file (KBrain-map App) function 
- Input data loading button
- powerful serverless computing and multi storage(local/cloud)
- Automated pre-processing for visualization of KNeuroViz
- Processing and accessing terabytes of KNeuroViz 3D images, mesh obj, and ctm data

## Installation
## Pre-processing
KNeuroVIz preprocessing setup manual

1. sudo apt-get install g++ python3-dev # recommend Python3
2. pip install numpy
3. Download igneous source. You will want to use the master branch from the [KNeuroViz fork](https://github.com/KBRI-NCRG/igneous) of igneous.
4. cd igneous and pip install -r requirements.txt
5. python setup.py develop
6. Download KNeuroViz_preprocessing.py Tool from the python tool folder

## Post-processing




## Background
클라우드 네이티브에서 점점 더 중요한 역할을 하고 있으므로, 신속한 확장을 요하는 클라우드 네이티브 애플리케이션을 호스팅하는데 
이상적인 플랫폼이다. K8S라고도 하며, Kubernetes는 컨테이너화된 application의 배포, 확장 및 관리하는 서비스 중심의 아키텍처로 변화되면서 컨테이너를 오케스트레이션하는 쿠버네티스의 중요성이 높아졌습니다. MicroService와 데브옵스(Devops) 환경에서 application을 빠르게 개발 및 배포하는 애자일 방법론을 적용하기 위해 쿠버네티스와 다른 요소 간의 기술을 제공하는 클라우트 네이티브 오픈소스 S/W 필수적이며, 클라우드 제품이 실행되는 컨테이너가 기준이 되었습니다. 
### Who is it for?
해당 CKA 인증은 쿠버네티스 관리자, 클라우드 관리자 및 쿠버네티스 인스턴스를 관리 및 기타 IT 전문가로 생각해볼수가 있습니다.

## CKA을 위한 요구되는 지식
- 쿠버네티스 리소스: 워크로드 오브젝트
	-> POD(고래 떼를 일컫음) : 하나 이상의 컨테이너로 구성, 스케일링의 단위, application 친숙(환경변수 / 정상 여부 상태 검사 정의 등 용이하다)
	-> 컨테이너 네트워크 인터페이스 (CNI) : 파드 간의 연결
	: 컨테이너 런타임(예:도커)은 CNI 플러그인 실행파일(예:Weave Net)을 호출하여 컨테이너의 네트워킹 네임스페이스에 인터페이스 추가/제거

- 쿠버네티스 리소스: 컨트롤러 오브젝트
	-> ReplicaSet: (Node 고장/ Pod삭제 등 발생 시) 파드 복제하여 파드 개수 유지
	-> Delpoyment: old 버전 -> new 버전으로 복제, 레플리카셋 관리
	-> DaemonSet:
		√ 모든 노드에 동일한 파드를 실행시키고 싶은 경우에 활용
		√ 리소스 모니터링, 로그 수집기 등 유용, 클러스터에 노드 추가,삭제 되면 자동으로 파드도 생성/삭제 됨

- 쿠버네티스 리소스: 컨트롤러 오브젝트
-

## 1.2. 마크다운의 장-단점
### 1.2.1. 장점
	1. 간결하다.
	
## CKA 선행 학습


## CKA 후기
