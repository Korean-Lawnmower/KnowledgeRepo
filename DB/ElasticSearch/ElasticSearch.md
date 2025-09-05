**Elastic Search**
- 검색 엔진 & No-SQL

| **엘라스틱서치**         | **MYSQL 등의 관계형 데이터베이스(RDBMS)** |
| ------------------ | ------------------------------ |
| 인덱스                | 데이터베이스  / 테이블                  |
| 샤드                 | 파티션                            |
| 타입                 | 테이블                            |
| 문서                 | 행                              |
| 필드                 | 열                              |
| 매핑                 | 스키마                            |
| Query DSL / ES\|QL | SQL                            |
- Query DSL
	- REST API 형식

**RESTful API**
- http 헤더와 URL 만 사용하여 다양한 형태의 요청을 할 수 있는 아키텍처

| **엘라스틱서치의 요청방식** | **기능**                                          |
| ---------------- | ----------------------------------------------- |
| GET              | 데이터 조회                                          |
| PUT              | Document 번호를 직접 지정해서 값을 넣거나 수정할때, 내용 전체에 대한 수정  |
| ~~PATCH~~        | ~~내용 일부에대한 수정이지만, elasticsearch 에는 PATCH 가 없음~~ |
| POST             | 인덱스 업데이트, 데이터 조회                                |
| DELETE           | 인덱스 데이터 삭제                                      |
| HEAD             | 인덱스의 정보 확인                                      |

```elasticsearch
# create index
PUT /my-index

# index 정보
GET my-index

# document 업로드
POST /my-index/_doc
{
    "id": "park_rocky-mountain",
    "title": "Rocky Mountain",
    "description": "Bisected north to south by the Continental Divide, this portion of the Rockies has ecosystems varying from over 150 riparian lakes to montane and subalpine forests to treeless alpine tundra."
}

# 해당 document 업로드 시, 자동으로 index 에 mapping
"mappings": {
      "properties": {
        "description": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "id": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        },
        "title": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
      }
    },


PUT /my-index/_doc
{
    "id":"park_rocky-mountain2",
    "description": "Bisected north to south by the Continental Divide, this portion of the Rockies has ecosystems varying from over 150 riparian lakes to montane and subalpine forests to treeless alpine tundra.",
    "document_number":2,
    "tags": ["rocky", true, 3.14, "2"] << 안에 있는 자료형은 하나로 통일 해줘야 함
}


# 세밀한 조건 없이 특정 필드에서 값을 찾고자 할 때
GET /my-index/_search?q="tundra"


# 자료형을 고정해서 index 만들기
PUT /my-index2/_doc/1
{
	"name":"kim",
	"age":23,
	"address":"gangdong"
}

```

- **type : keyword** 자료형
	- 들어오는 문자열을 통으로 저장

----

### 구조

**Cluster**
- 클러스터 구성 시 홀수 개의 노드로 구성
- node 하나가 꺼졌을 때, data-loss를 막기 위해 원본 인덱스 복사하여 다른 노드에 저장
	- 분산 저장

**Master Node**
- 데이터 관리 의사결정 담당
- 따로 정해주지 않으면 셋중에 하나 랜덤으로 지정
- 실제 데이터 저장의 기능은 덜함
- 연산이 많아 RAM 이 큰 서버 사용 필요

**Node**
- 저장 위주의 작업이므로 Storage 가 큰 서버 사용 필요

**Shard**
- 도큐먼트를 작은 단위로 분할
- 여러개의 복제본 존재
- 여러 노드에 분산하여 저장


### 환경설정

```yml
# config/jvm.options

################################################################
## IMPORTANT: JVM heap size
################################################################
##
## The heap size is automatically configured by Elasticsearch
## based on the available memory in your system and the roles
## each node is configured to fulfill. If specifying heap is
## required, it should be done through a file in jvm.options.d,
## which should be named with .options suffix, and the min and
## max should be set to the same value. For example, to set the
## heap to 4 GB, create a new file in the jvm.options.d
## directory containing these lines:
##
-Xms1g << 메모리 할당 (1g)
-Xmx1g
##
## See https://www.elastic.co/guide/en/elasticsearch/reference/9.1/heap-size.html
## for more information
##
################################################################

#config/elasticsearch.yml 
cluster.name: woori-es 
node.name: woori-es-node01 
path: 
	data: C:\ITStudy\06_elk\elasticsearch\data 
	logs: C:\ITStudy\06_elk\elasticsearch\logs network.host: 127.0.0.1 discovery.type: "single-node" 
xpack.security.enabled: false

# config/kibana.yml

server.port: 5601 
server.host: localhost 
server.publicBaseUrl: "http://localhost:5601" 
elasticsearch.hosts: ["http://localhost:9200"]
```


---

### 검색 로직

```elasticsearch
# 세밀한 조건 없이 특정 필드에서 값을 찾고자 할 때
GET /my-index/_search?q="mountain2"

# 검색시에도 본문을 달아서 
POST/GET /my-index/_search
{
	"query": {
		"match": {
			"id": "mountain2"
		}
	}
}
```


---

**Mapping vs Setting**
- **Mapping**
	- 동적 맵핑
		- 자동으로 맵핑 (자료형 자동 생성)
	- 명시적 맵핑
		- 빈 인덱스를 만들어 놓고 type 을 명시해 주며 맵핑


**Field Type**

| **분류**         | **종류**                                           |
| -------------- | ------------------------------------------------ |
| 심플 타입          | text, keyword, date, long, double, boolean, ip 등 |
| 계층 구조를 지원하는 타입 | object, nested 등                                 |
| 그 외 특수한 타입     | geo_point, geo_shape 등                           |

- 문자열 : 2개의 variant
	- 텍스트 자료형
	- 키워드 자료형
```
# 2개를 동시에 지정 (specify 하지 않으면)
"name": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword",
              "ignore_above": 256
            }
          }
        }
```

**복합 자료형 검색**
- `"hobby": ["책보기", "ㅁㅁㄴㅇㄹ", "3333"],`
	- "hobby":"책보기"
	- "hobby":"ㅁㄴㅇㄹ"
	- "hobby":"3333"
- 분할해서 저장 하기 때문에
- `GET my-index/_search?q=hobby:"책보기"`
- simple 자료형과 똑같이 검색 / 검색 로직 가능

**Nested Field 검색**
```
POST woorifisa/_doc
{
  "students":
  {
    "name":"신짱구",
    "age": 5
  }
}
```

**mapping specified nested field 검색**
```
PUT woorifisa_nested_test
{
  "mappings": {
    "properties": {
      "student": {
        "type": "nested",
        "properties": {
          "name": {
            "type": "keyword"
          },
          "age": {
            "type": "long"
          }
        }
      }
    }
  }
}
```
- 위 구조상 2개의 document 
	- student --> properties
	- 타고 들어가는 형식

**Query**

| 구분           | `term` 쿼리                       | `match` 쿼리       |
| ------------ | ------------------------------- | ---------------- |
| **검색 대상**    | 정확한 값                           | 단어 기반(토큰화)       |
| **사용 필드 타입** | `Keyword`, `Numeric`, `Boolean` | `Text`           |
| **검색 방식**    | 분석기(토큰화) 없이 그대로 검색              | 분석기(토큰화) 적용 후 검색 |
| **사용 예시**    | 상태 값, ID 검색                     | 자연어 검색           |
**Match**
- 자연어 검색이 필요할 시

**Term**
- 정확한 값

**다중 조건 조합 쿼리 (bool Query)**
```
{
	"query": 
}
```


---

### 집계

**사용 상황**
- 로그
- 매트릭스
- 분산 추적
- 사용자 경험

**집계 API 종류**
- metric 집계
	- 총합, 평균, 최소값, 최대값
- bucket 집계
	- 구간별 구간의 대표값을 도출
- pipeline 집계
	- 특정 상황에 고정해놓고 메트릭 확인


```elasticsearch
# 양식
get kibana_sample_data_ecommerce/_search
{
	"size":0 # 결과 중에 n개만 보여주는 역할, 0일 시 , 조회 결과는 보지 않고 집계 결과만 출력
	"query":{
		"match_all":{}
		##### OR #####
		"term" : {
			"currency": {
				"value": "EUR"
			}
		}
	},
	"aggs":{
		"my-sum-aggregation":{
			"type":{
				"field":"field
			}
		}
	}
}

# 예시
get kibana_sample_data_eccommerce/_search
{
	"size":0,
	"query":{
	}
}
```



---
```log
$ echo "hello world 1 \nhello world 2" |  bin\logstash -f config\logstash-pipeline-1.conf

{
    "@timestamp" => 2025-08-13T00:48:30.539198700Z,
         "event" => {
        "original" => "\"hello world 1 \\nhello world 2\" \r"
    },
          "host" => {
        "hostname" => "2-24"
    },
       "message" => "\"hello world 1 \\nhello world 2\" \r",
      "@version" => "1"
}
```

