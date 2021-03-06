## Cloud Dataflow

### 1. Cloud Dataflow
* 정의 : 배치 및 스트림모드로 데이터를 변환하고 처리할 수 있는 완전한 관리형 서비스이다.



### 2. Cloud Dataflow 특징
* 리소스 프로비저닝 및 관리에 대한 서버리스접근 방식 덕분에 무제한에 가까운 용량을 이용해 대규모 데이터처리 과제를 해결할 수 있다.
* Apache Beam SDK를 통한 자바 및 Python API를 제공하기 때문에 간단하고 빠르게 파이프라인을 개발할 수 있다.
* GCP에서 관련된 모든 관리를 자동으로 해주기 때문에 서비스를 위한 프로그래밍에 전념할 수 있어 간접운영비가 사라진다.
* GCP의 통합로그기록 및 모니터링 솔루션인 Stack driver를 통해 파이프라인에 대한 모니터링 및 문제해결에 도움을 받을 수 있다.

### 3. Cloud Dataflow  지원하는 언어 : Java, Python, Go 같은 현재 지원하는 Apache Beam 언어이다.

### 4. Cloud Dataflow 구성요소
* Pipeline : 데이터처리를 위한 큰흐름이고, 처음부터 끝까지 전체데이터 처리작업을 캡슐화한 것을 말한다.
* Pipeline I/O : 데이터의 Input과 Output을 위한 부분을 말한다.
* PCollection : 데이터 저장을 위한 데이터타입(Input과 Output)이자 Dataflow Pipeline이 작동하는 분산 데이터세트이다.
    + 특징
        - 데이터플로 파이프라인 내에서 데이터를 저장하는 개념으로 한번 생성되면, 그 데이터는 수정이 불가능하다, 따라서 데이터를 변경하거나 수정하기 위해서는 PCollection을 재생성해야한다.
        - 요소유형(Element type) : 모든 유형이 될 수 있지만, 모두 동일한 유형이어야한다, 그러나 분산처리를 위해서는 각 개별요소를 바이트 문자열로 인코딩 할 수 있어야 한다.
        - 불변성(Immutability) : PCollection은 변경이 불가능하다.
        - 무작위 접근(Random access) : 개별요소에 대한 무작위 액세스를 지원하지 않는다.
        - 크기와 경계(Size and boundedness) : PCollection의 크기는 제한을 할 수도 있고 제한하지 않을 수도 있다, 보통파일이나 데이터베이스가 같은 배치 데이터소스는 제한된 PCollection으로 만들고 Pub/Sub이나 Kafka 같은 스트리밍 데이터소스는 제한되지 않는 PCollection으로 만든다.
        - 요소 타임 스탬프(Element timestamps) : PCollection 각 요소에는 고유한 타임 스탬프가 있다, 각 요소의 타임 스탬프는 소스에 의해 결정이 된다. Unbounded PCollection은 개별요소가 읽히거나 추가될 때 타임 스탬프를 할당한다.
* Transforms : 데이터의 변환 및 가공, 변경한 데이터는 PCollection에 저장한다, 즉 파이프라인에서 데이터 처리작업 또는 단계이다, 모든 PTransform은 하나이상의 PCollection 객체를 입력으로 가져와 해당 PCollection의 요소에 제공하는 처리기준을 수행하고 0개이상의 출력 PCollection 객체를 생성한다.
* Dataflow Runners : Cloud Dataflow는 사용목적에 따라 Runner를 다르게 설정할 수 있다.
    + Direct Runner : 로컬에서 돌릴때 사용
    + Apex Runner : Apache Apex 돌릴때 사용
    + Flink Runner : Apache Flink 돌릴때 사용
    + Spack Runner : Apche Spack 돌릴때 사용
    + Dataflow Runner : Cloud Dataflow 이용시 사용
    + Gearpump Runner : Apache Gearpump 돌릴때 사용



### 5. Unbounded Data(스트리밍 데이터) 처리
* Windowing 정의 : Unbounded Data(스트리밍 데이터)와 같은 경우에는 데이터가 끊이지 않고 들어오기 때문에 결과를 내보내야 하는 타이밍을 잡기 애매하다, 이를 위해서 시간을 기준으로 작업을 끊어서 처리하는데 이것을 말한다.
* Window 정의 : 스트리밍 데이터는 계속 들어오기 때문에 특정시간 단위로 지속적으로 처리하고 저장을 해주어야 한다, 이때 사용하는 개념이 Window인데 크게 Fixed Window와 Sliding Window, 그리고 Session Window라는 개념을 사용한다.
    + Fixed Window : 고정된 크기의 시간을 가지는 윈도우이다, 여기서는 10분이 윈도우 길이이다.
    + Sliding Window : 다른 윈도우들과 중첩이 되는 윈도우이다, 여기서는 10분이 윈도우 길이 이고, 윈도우 간격(새로운 윈도우가 생기는 시간 간격)은 5분이다.
    + Session Window : 데이터가 들어오면 원도우가 시작이 되고 세션 종료시간(5분)까지 데이터가 들어오지 않으면, 그때 윈도우를 종료되고 새로운 데이터가 들어올때 다시 새로운 윈도우를 생성한다.
* 지연 데이터와 WaterMark
    + 워터마크(WaterMark) : 실제 데이터들이 발생한 시간과 서버에 도착하는 시간에는 차이가 발생할 수 있기 때문에 어느 시점까지 데이터를 기다렸다가 처리해야 하는지에 대한 고민이 생길때 실제 데이터가 도착하는 시간을 예측해야 하는데 이를 말한다, 위터마크를 기반으로 윈도우의 시스템상의 시작시간과 종료시간을 예측하게 된다.
* Trigger(트리거)
    + 정의 : 처리중인 데이터를 언제 다음 단계로 넘길지를 결정하는 개념이다, Accumulating mode옵션을 이용하여 누적여부를 선택한다.
    + 종류
        - Time trigger : 득정 이벤트 시간에 작동한다.
        - Element count trigger : 데이터의 개수를 기반으로 작동한다.
        - Punctuaions trigger : 특정 데이터가 들어오는 순간에 작동한다.
        - Composite trigger : 여러가지 trigger를 조합한 형태이다. 
