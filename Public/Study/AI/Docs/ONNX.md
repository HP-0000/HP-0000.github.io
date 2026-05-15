---
---
AI 모델에 대한 글로벌 인공지능 업계 표준 포맷이다.
###### ONNX Runtime
마이크로소프트가 제공하며, 
프레임워크 종속성 없이, ONNX 표준 규격의 딥러닝 모델을 다양한 크로스 플랫폼 환경에서 초고속으로 단독 구동할 수 있는 C++ 기반의 초경량 추론(Inference) 엔진 라이브러리다.
https://github.com/microsoft/onnxruntime/releases

모델 시각화
https://github.com/lutzroeder/netron/releases/tag/v9.1.2
# onnx.proto
ONNX 는 프로토 버퍼로 작성된다.
[Protobuf](/Public/Study/Data%20Structure/Docs/Protobuf) 
구조는 다음과 같다.
https://github.com/onnx/onnx/blob/main/onnx/onnx.proto

---
### 1단계 깊이: `ModelProto` (최상위 컨테이너 - 겉봉투)
ONNX 파일을 파싱할 때 컴퓨터가 가장 먼저 열어보는 영역입니다. 모델 전체의 메타데이터와 실제 뇌 구조(`GraphProto`)로 들어가는 입구를 담고 있습니다.

| 태그 (Tag) | 필드명 (Field Name) | 데이터 타입 (Type) | 필수/선택 | 설명 |
| :--- | :--- | :--- | :--- | :--- |
| **`1`** | `ir_version` | `int64` | **필수** | ONNX Intermediate Representation(IR) 규격 버전 |
| **`2`** | `producer_name` | `string` | 선택 | 모델 변환 도구 및 프레임워크 이름 (예: `"PyTorch"`, `"TensorFlow"`) |
| **`3`** | `producer_version` | `string` | 선택 | 변환에 쓰인 도구의 상세 버전 (예: `"2.1.0"`) |
| **`4`** | `domain` | `string` | 선택 | 네임스페이스 도메인의 역순 표기 (예: `"ai.onnx"`, `"com.microsoft"`) |
| **`5`** | `model_version` | `int64` | 선택 | 이 모델 파일 자체의 고유 버전 수치 |
| **`6`** | `doc_string` | `string` | 선택 | 모델 사양을 마크다운으로 적어둔 설명 주석 텍스트 |
| **`7`** | `graph` | **`GraphProto`** (LEN) | **필수** | **[트리 깊이 2단계 진입]** 실제 AI 뇌 세포 연산 구조 및 가중치 그래프 |
| **`8`** | `opset_import` | `repeated OperatorSetIdProto` | **필수** | 모델이 정상 구동하기 위해 수입해 온 표준 AI 연산자 셋 버전 정보 리스트 |
| **`14`** | `metadata_props` | `repeated StringStringEntryProto` | 선택 | 모델 개발자가 암호화 키 등 임의의 고유 정보를 쌍으로 남겨놓는 메타데이터 영역 |
| **`20`** | `training_info` | `repeated TrainingInfoProto` | 선택 | 온디바이스 학습(C++) 제어에 활용되는 학습 세팅 및 옵션들의 리스트 |
| **`25`** | `functions` | `repeated FunctionProto` | 선택 | 자주 쓰는 연산 구조를 매크로 함수처럼 선언하여 보관하는 정의 리스트 |

---

### 2단계 깊이: `GraphProto` (인공신경망 뇌 구조)
실제 데이터를 받아 계산을 처리하는 인공신경망 노드들과, 목소리 데이터 가중치 영역의 입구를 담은 본진 구역입니다.

| 태그 (Tag) | 필드명 (Field Name) | 데이터 타입 (Type) | 필수/선택 | 설명 |
| :--- | :--- | :--- | :--- | :--- |
| **`1`** | `node` | **`repeated NodeProto`** (LEN) | **필수** | **[트리 깊이 3단계 진입]** 연산을 처리하는 신경망 레이어(Node)들의 리스트 |
| **`2`** | `name` | `string` | **필수** | 이 신경망 계산 그래프의 고유 식별 명칭 |
| **`5`** | `initializer` | **`repeated TensorProto`** (LEN) | **필수** | **[트리 깊이 3단계 진입]** 목소리 톤 등 학습된 알맹이 가중치(Weights) 리스트 |
| **`11`** | `input` | `repeated ValueInfoProto` | **필수** | 외부 데이터를 받아들이는 입력 단자들의 데이터 규격 정의 목록 |
| **`12`** | `output` | `repeated ValueInfoProto` | **필수** | 모든 계산이 끝난 최종 생성 데이터를 내보내는 출력 단자 규격 목록 |
| **`13`** | `value_info` | `repeated ValueInfoProto` | 선택 | 각 연산 노드 사이를 흐르는 중간 계산 데이터 텐서들의 메모리 사양 목록 |
| **`14`** | `quantization_annotation` | `repeated TensorAnnotation` | 선택 | AI 연산 고속화를 위해 실수형을 정수형으로 압축 정렬해 둔 양자화 데이터 목록 |
| **`15`** | `sparse_initializer` | `repeated SparseTensorProto` | 선택 | 메모리 최적화를 위해 0이 많은 희소 가중치를 특수하게 압축 저장해 둔 영역 |
| **`10`** | `doc_string` | `string` | 선택 | 이 인공신경망 그래프에 대한 주석/설명문 |

---

### 3단계 깊이 (A): `NodeProto` (개별 연산 레이어 - 뇌 세포 하나)
신경망의 가닥을 이어주는 개별적인 연산 명령어로, 이름 기반 연결을 통해 신경망 지도를 이룹니다.

| 태그 (Tag) | 필드명 (Field Name) | 데이터 타입 (Type) | 필수/선택 | 설명 |
| :--- | :--- | :--- | :--- | :--- |
| **`1`** | `input` | `repeated string` | **필수** | 이 연산(예: Conv)에 인풋으로 연결되어 들어오는 데이터들의 고유 이름들 |
| **`2`** | `output` | `repeated string` | **필수** | 연산이 완료된 후 다음 레이어로 아웃풋해 줄 데이터들의 고유 이름들 |
| **`3`** | `name` | `string` | 선택 | 이 연산 레이어 자체의 고유 식별 명칭 |
| **`4`** | `op_type` | `string` | **필수** | 실행할 연산 명령어 종류 (예: `"Conv"`, `"Relu"`, `"Add"`, `"Gemm"`) |
| **`5`** | `attribute` | `repeated AttributeProto` (LEN) | 선택 | **[트리 깊이 4단계 진입]** 커널 크기, 스트라이드, 패딩 등 연산 고정 매개변수 리스트 |
| **`6`** | `doc_string` | `string` | 선택 | 이 개별 연산 레이어에 대한 주석/설명문 |
| **`7`** | `domain` | `string` | 선택 | 표준 연산자가 아닌 커스텀 연산자를 사용할 경우 지정하는 도메인 영역 |

---

### 3단계 깊이 (B): `TensorProto` (목소리 수치가 잠들어 있는 물리 가중치)
실제 딥러닝 연산에 직접 대입되어 계산되는 실수 및 정수형 가중치 수치들이 날것 그대로 저장되는 구역입니다.

| 태그 (Tag) | 필드명 (Field Name) | 데이터 타입 (Type) | 필수/선택 | 설명 |
| :--- | :--- | :--- | :--- | :--- |
| **`1`** | `dims` | `repeated int64` | **필수** | 이 가중치 텐서의 차원 형태 구조 (Shape, 예: `[64, 3, 3]`) |
| **`2`** | `data_type` | `int32 (DataType Enum)` | **필수** | 가중치 알맹이의 수학적 데이터 형식 코드 (`1: FLOAT`, `6: INT32`, `7: INT64` 등) |
| **`4`** | `float_data` | `repeated float` | 선택 | 크기가 작은 Float32 실수 가중치들을 나열 보관하는 영역 |
| **`5`** | `int32_data` | `repeated int32` | 선택 | 크기가 작은 Int32 정수 가중치들을 나열 보관하는 영역 |
| **`7`** | `string_data` | `repeated bytes` | 선택 | 가중치가 문자열 데이터 형태로 되어 있을 때 보관하는 영역 |
| **`8`** | `name` | `string` | **필수** | 이 가중치의 고유 식별 명칭 (예: `"conv1.weight"`, `"bias"`) |
| **`9`** | `raw_data` | `bytes` | **필수** | **[핵심] 실제 거대한 AI 가중치 실수 바이트열이 어떠한 가공 없이 날것 그대로 박혀있는 영역** |
| **`10`** | `double_data` | `repeated double` | 선택 | 가중치가 64비트 정밀 실수형(Double)일 때 나열 보관하는 영역 |
| **`11`** | `int64_data` | `repeated int64` | 선택 | 가중치가 64비트 정수형(Int64)일 때 나열 보관하는 영역 |
| **`12`** | `uint64_data` | `repeated uint64` | 선택 | 가중치가 부호 없는 64비트 정수형(Uint64)일 때 나열 보관하는 영역 |
| **`15`** | `external_data` | `repeated StringStringEntryProto` | 선택 | 가중치 파일 용량이 너무 커서 `.onnx` 파일 외부에 격리 저장할 때의 경로 정보 |

---


