# 개념(Concepts) & 원리(Principles) — Monitoring

- Observability vs Monitoring: 관측성(3 Signals)과 모니터링의 차이
	- **Observability (관측성) - 원인을 찾아줌**
		- 엔지니어링을 통해 해당 특성을 달성해야 함
			- 시스템 내부 상태를 외부에서 나오는 신호 만으로 추론하고 원인 분석할 수 있는 특성
			- 자유로운 쿼리, 분산 트레이싱으로 맥락 파악
			- 문제의 원인 파악 능력을 높이도록 신호 설계 필요 (context id, correlation id)
				- high cardinality
	- **Monitoring (모니터링) - 문제 발생 감지**
		- 미리 정한 지표와 규칙으로 시스템 상태를 감지, 문제 징후 탐지/알림
		- 실시간 감시/통보 중심
    
- 3 Signals: Metrics / Logs / Traces(+ Events)
	- **Metrics**
		- 지표, 추세, 임계치
			- 모니터링/알림
			- trace_id 링크 필요 (문제 발생 시 트레이스로 점프)
		- **Type**
			- 카운터 (누계)
				- http_requests_total
			- Gauge (현재값)
				- in_flight_requests
				- work_queue_depth
			- Histogram (지연 분포)
				- http_request_duration_seconds
	- **Logs**
		- 사실 기록
			- 사후 분석 / 디버깅
		- 구조적 json 형태
		- 필드
			- timestamp
			- level
				- error : 액션 가능한 것만
				- debug : 샘플링/비활성 기본
			- service
			- trace_id
			- span_id
			- msg
			- user_key
			- req_id
		- 민감정보
			- masking/drop 필요
	- **Traces**
		- 호출 경로 / 지연 분해
			- 근본 원인 파악
		- Trace (요청 단위)
			- 하나의 요청 (transaction) 이 분산 시스템을 통과하며 남기는 전체 기록
			- Span (서비스/DB/외부호출)
				- 전체 "trace"의 기록 속, 한 구간의 작업
	- **Events**
		- 문제 원인 후보 타임라인
		- Type
			- 배포, 릴리즈, 피처플래그 변경, 인프라 장애, 합성 모니터링 결과
    
- SLI · SLO · SLA: 사용자 중심 목표와 알람 설계의 기준
	- SLI : 사용자가 체감하는 품질 지표
	- **SLO : SLI 목표치 (알람 설계 기준)**
	- SLA : 대외 약속/보상 조항 (외부 우선 지표)
	- p95/p99
		- 대다수의 지표는 average 를 확인하지만, 이러한 경우 outlier은 감지 불가. Outlier를 감지하려면 95% 99%를 포함하는 지표를 사용
    
- Golden Signals(Latency/Traffic/Errors/Saturation), RED/USE 방법론
	- **Golden Signals**
		- **지연**
		- **트래픽**
		- **오류**
		- **포화**
	- RED (요청형): Requests, Errors, Duration
	- USE : Utilization, Saturation, Errors

- Whitebox vs Blackbox(내부 지표 vs 외부 헬스체크, 합성 모니터링 유형)
	- Whitebox
		- 내부 지표 / 트레이스 기반
	- Blackbox
		- 외부 헬스체크/합성 모니터링
	- 두개를 병행하는게 가장 이상적
    
- 데이터 수집 기법
	- Pull (구성 단순, 신뢰성 높음)
		- 모니터링 서버가 서비스로부터 직접 데이터를 끌어오는(수집하는) 방식.
		- 각 서비스는 /metrics 같은 지표 노출 엔드포인트를 제공하고, 모니터링 툴이 주기적으로 수집(Scrape)
		- Kubernetes에서 모든 Pod이 /metrics를 노출
		- Prometheus가 15초마다 각 Pod를 스크레이프
	- Push (방화벽 환경 적합)
		- 각 서비스가 직접 모니터링 서버나 게이트웨이에 데이터를 보내는 방식.
	- Hybrid
		- Pull은 시계열 메트릭 수집에 효율적
		- Push는 이벤트성 데이터(로그, 트레이스)나 단명 프로세스에 적합
		- 상황에 따라 둘 다 쓰면 장애 대응력↑
	- 라벨
		- http_requests_total{method="GET", status="200", service="checkout"}
		- 여기서 
			- http_requests_total 이 메트릭 이름
			- 나머지 metadata 가 라벨
	- 카디널리티
		- 라벨 조합의 unique 개수
		- status {200, 404, 500} X service {checkout, payment} = 6개
		- Prometheus 같은 시계열 DB에서는 **라벨 조합마다 새로운 시계열(time series)이 생성됨
		- 100% 일치하면 시간만 저장
		- 카디널리티가 높으면:  
			- 메모리 사용량↑
			- 쿼리 속도↓
			- 저장 비용↑
	- 저장
		- 시계열 DB (Prometheus TSDB, VictoriaMetrics)
		- 로그 저장소 (Elasticsearch, Loki)
		- 트레이스 저장소 (Jaeger, Tempo
	- 가공
		- 샘플링, 집계, 변환, 라벨 추가/수정
		- 오래된 데이터 압축 및 민료 처리
	- 시각화
		- 그라파나, 키바나
		- p95, p99, 에러율, cpu등
	- 샘플링
		- 모든 요청을 100% 저장하면 스토리지 비용 + 네트워크 부하 폭팔
		- 헤드
			- 요청 시작 시점에서 저장 여부를 랜덤/비율로 결정
		- 테일
			- 요청이 끝난 뒤에 저장 여부를 결정
		-   Rules 기반


















- 시계열 기본기: 스크레이프 주기, 집계/다운샘플, 보존기간(retention)
	- 주기 : 15~30초 권장
	- 다운샘플/롤업
		- 장기 보존 시, 시/분 단위 집계

- 지표 타입: Counter/Gauge/Histogram/Summary와 p95/p99 해석
    
- 라벨/카디널리티 관리: 비용·성능 균형(“카디널리티 예산”)
    
- 트레이싱 샘플링: Head/Tail/Rules 기반 샘플링과 오류 편향 샘플링
    
- 알림 원리: 임계값/비정상 탐지/SLO 기반 알림, 노이즈 억제
    
- 서비스 토폴로지: 의존성 그래프, 병목·핫스팟 파악
    
- eBPF/프로파일링: 커널/CPU/메모리 레벨 추가 신호
    

# 개념(Concepts) & 원리(Principles) — Logging

- 구조적 로깅(Structured JSON): 스키마, 키 표준화(ECS/OTel)
    
- 로그 레벨(Level/Severity)와 사용 가이드
    
- 상관관계 ID: trace_id/span_id/요청 ID 전파(헤더: W3C TraceContext 등)
    
- 멱등성·중복·백프레셔: 버퍼링/재전송/적어도 한 번(at-least-once)
    
- 개인정보/민감정보 처리: 마스킹·토큰화·필터링
    
- 멀티라인/스택트레이스 처리, 로테이션/압축
    
- 보존·티어링: 핫(검색) → 웜 → 콜드(객체 스토리지)
    
- 쿼리 패턴: 시간창+필드 조건+전문 검색, 인덱싱 전략
    

# 아키텍처 구성요소(Components)

**애플리케이션 계층**

- 로깅 라이브러리: 구조적 JSON, 컨텍스트(Trace/Span) 포함
    
- 메트릭스 SDK/Exporter: OpenTelemetry, Prometheus client 등
    
- 트레이싱 SDK: OpenTelemetry/Jaeger/Zipkin API
    
- 컨텍스트 전파 미들웨어: 게이트웨이/Ingress/HTTP 클라이언트
    

**수집/사이드카·에이전트 계층**

- 로그 에이전트: Fluent Bit/Fluentd, Promtail, Vector, Filebeat
    
- 메트릭 수집기: Prometheus(Node/Exporter), StatsD
    
- 트레이스 에이전트: OTel Collector(Agent), Jaeger Agent
    
- 서비스 메시 텔레메트리: Envoy/Istio/Linkerd 통계·접근로그
    

**버퍼/전송 계층(선택)**

- 큐/스트림: Kafka, Kinesis, SQS
    
- 재시도/백프레셔/배치 전송
    

**처리/변환 계층**

- OTel Collector/Logstash/Vector Processor: 파싱·리맵·샘플링·마스킹
    
- 스트림 처리: Flink/Spark/Faust(이상치 탐지, 파생지표)
    

**저장/인덱스 계층**

- 메트릭 TSDB: Prometheus(로컬), Thanos/Cortex/Mimir/VictoriaMetrics(확장)
    
- 로그 스토어: Elasticsearch/OpenSearch, Loki(인덱스 경량), CloudWatch Logs
    
- 트레이싱 백엔드: Jaeger, Tempo, Zipkin, X-Ray
    
- 콜드 스토리지: S3/GCS/아카이빙
    

**시각화/경보/운영**

- 대시보드: Grafana, Kibana/OpenSearch Dashboards
    
- 알림: Alertmanager, PagerDuty/Opsgenie/Slack
    
- 합성 모니터링: Blackbox Exporter, Checkly/Uptime
    
- 오류 모니터링: Sentry 등
    
- 접근제어/컴플라이언스: IAM/ACL, TLS, WORM 보관, 감사용(감사로그/SIEM 연동)
    

# 구현 패턴 & 변형(Variants)

**A. 최소구성(개발/소규모)**
- 앱 → (Prometheus + Grafana) 메트릭, 앱 → 파일 → Fluent Bit → Elasticsearch/Kibana
- 트레이스: OTel SDK → Jaeger all-in-one
    

**B. Kubernetes 표준 EFK**
- Pod(앱 로그) → Fluent Bit(DaemonSet) → Elasticsearch → Kibana
- 메트릭: Prometheus Operator(+ Alertmanager) → Grafana
- 트레이스: OTel Collector(Agent/Gateway) → Jaeger
    

**C. PLG(로그 경량화)**
- Promtail → Loki → Grafana(비용↓, 인덱스 경량)
- 메트릭/트레이스는 Prometheus/Tempo로 보완(PGT/PLGT)
    

**D. 확장형 멀티클러스터 메트릭**
- 각 클러스터 Prometheus → Thanos/Cortex/Mimir(장기보존/전역쿼리) → Grafana
    

**E. OTel 중심 허브형**
- 애플리케이션/mesh → OpenTelemetry Collector(Agent) → Collector(Gateway)
    - Processors(샘플링/마스킹/리맵) → Exporters(Tempo/Jaeger, Prometheus Remote Write, Loki/Elastic)
- 백엔드 벤더 자유도↑, 이식성↑
    

**F. 이벤트 스트림 우선**
- 앱/에이전트 → Kafka → 다중 싱크(Elasticsearch, S3, Druid/Pinot, TSDB)
- 장점: 탄력/재처리, 대규모 피크 처리
    

**G. 클라우드 매니지드**
- AWS: CloudWatch(Log/Metrics/Alarms) + Managed Grafana/Prometheus + X-Ray + OpenSearch
- GCP: Cloud Logging/Monitoring/Trace(OTel 호환)
- Azure: Application Insights + Log Analytics + Managed Prometheus
- 장단: 운영편의성↑, 비용/락인 고려
    

**H. 서비스 메시 중심 관측**
- Envoy/Istio 텔레메트리 → OTel Collector → Prometheus/Tempo/Loki
- 장점: 통일된 사이드카 계측, 단점: 메시 복잡도
    

**I. 보안·규제 강화형**
- 수집단계 PII 필터, 전구간 TLS, WORM 스토리지(규정 준수), 접근감사·SIEM 연계









---

**모놀리스**
- 모든 코드/데이터가 하나의 어플리케이션을 구성
- 하나의 서버로 작동
- **로깅**
	- 단일 비정형 로드 파일 (app.log)
	- 탐색 방법 (서비스에 문제 발생 시)
		- tail
		- grep
- **모니터링**
	- CPU/메모리/디스크와 같은 서버 단위의 지표만 확인
	- 요청에 흐름을 따라가는 트레이싱 기능 없음
		- 단순 속도/퍼포먼스만 확인 가능
- **한계**
	- 장애 발생 시 어느 부분에서 문제가 일어났는지 파악/복구 어려움
	- 서버를 늘리면 상호 연관 분석이 거의 불가능

**마이크로 서비스**
- 여러 작은 서비스가 각기 다른 컨테이너/노드에서 동작
- 기존의 서버 지표 + 단일 로그 방식으로 문제 추적이 불가
- 신규 기술 필요
	- **분산 모니터링**
	- **중앙 집중 로깅**
	- **분산 트레이싱**

**현대적인 모니터링**
- 목표
	- 실시간으로 서비스의 상태와 성능을 숫자로 관찰
	- 골든 시그널
		- 지연시간 (Latency)
		- 트래픽 (Traffic)
		- 오류율 (Errors)
		- 포화도 (Saturation)
	- SLI · SLO · SLA
		- SLI : 사용자가 체감하는 품질 지표
		- **SLO : SLI 목표치 (알람 설계 기준)**
		- SLA : 대외 약속/보상 조항 (외부 우선 지표)
	- 트레이싱
		- 요청이 마이크로서비스를 거치는 전 과정을 시각화
	- **Metrics**
		- 지표, 추세, 임계치
			- 모니터링/알림
			- trace_id 링크 필요 (문제 발생 시 트레이스로 점프)
		- **Type**
			- 카운터 (누계)
				- 시간 흐름에 따라 계속 누적되는 지표
				- 특정 기간 동안 증가량을 계산해서 속도나 증가율 분석 가능
				- http_requests_total, error_count_total
			- Gauge (현재값)
				- 값이 증가하거나 감소할 수 있는 지표
				- 특정 시점의 상태(스냅샷)을 그대로 반영
				- cpu_usage, memory_usage_bytes
			- Histogram (지연 분포)
				- 값을 구간 (bucket) 별로 집계하여 분포를 분석
				- 응답 시간이나 데이터 크기 같은 값의 분포를 관찰할 때 사용
				- http_request_duration_seconds
				- 퍼센타일 계산 가능
				- SLA/SLO 모니터링에 필수
	- 트레이싱 샘플링
		- 헤드 샘플링
			- 일정 비율
		- 테일 샘플링
			- 느린 요청/오류만 저장
	- 알림
		- SLO 소모율 (burn rate) 기반 알림
			- Alertmanager --> 메신저 전송
	- 라벨 관리
		- 라벨 수 (Cardinality) 제한

**현대적인 로깅**
- 목표
	- 상세 이벤트 기록을 중앙에서 빠르게 검색/분석
	- 구조화
		- JSON 포맷으로 필드 통일
			- timestamp
			- service
			- level
			- trace_id
			- span_id
			- message
		- 서비스별 분산 로그를 중앙에서 집계
	- 













