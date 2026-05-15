---
---

TrueType Collection 구조를 재 사용한다.
https://learn.microsoft.com/en-us/typography/opentype/spec/otff?source=docs#data-types


## TTCHeader
###### TTCHeader Version 1.0
디지털 서명을 포함하지 않는 헤더

| Type | Name | Description |
| :--- | :--- | :--- |
| `Tag` | `ttcTag` | TrueType Collection ID string: 'ttcf' |
| `uint16` | `majorVersion` | Major version of the TTC Header, = 1. |
| `uint16` | `minorVersion` | Minor version of the TTC Header, = 0. |
| `uint32` | `numFonts` | Number of fonts in TTC. |
| `Offset32` | `offsetTable[numFonts]` | Array of offsets to the TableDirectory for each font from the beginning of the file. |

###### TTCHeader Version 2.0
Version 2.0은 Version 1.0의 모든 필드를 포함하며, 
그 뒤에 디지털 서명(`DSIG`)을 위한 3개의 필드가 추가된 구조

| Type | Name | Description |
| :--- | :--- | :--- |
| `uint32` | `dsigTag` | Tag for DSIG table: 'DSIG' (to indicate the TTC has a signature) or null. |
| `uint32` | `dsigLength` | The length (in bytes) of the DSIG table. |
| `uint32` | `dsigOffset` | The offset (in bytes) of the DSIG table from the beginning of the TTC file.|

폰트 해석기는 샌드 박스에서 실행됨으로 보안 위험이 없어 디지털 서명은 잘 사용되지 않는다.
물론 자체 라이브러리를 이용하는 게임 엔진의 경우 보안의 구멍은 존재한다.

---

## Table Directory

폰트 각각에 대해  테이블 디렉토리가 존재한다

| Type | Name | Description |
| :--- | :--- | :--- |
| `uint32` | `sfntVersion` | 0x00010000 or 0x4F54544F |
| `uint16` | `numTables` | Number of tables. |
| `uint16` | `searchRange` |numTables 이하의 2의 최대 거듭제곱에 16을 곱한 값 |
| `uint16` | `entrySelector` | numTables 이하인 최대 2의 거듭제곱의 log 2 (log 2 (searchRange/16). |
| `uint16` | `rangeShift` | numTables 곱하기 16에서 searchRange를 뺀 값|
| `TableRecord` | `tableRecords[numTables]` | Table records array. |

각 테이블 레코드의 태그는 32비트 정수이고, 이를  빠르게 검색하기 위해 이진 탐색 한다.
테이블 레코드는 16바이트고  배열은 오름차순으로 정렬 되어있다.
searchRange 값을 통해  최대 2의 거듭 제곰 범위 만큼을 알 수 있음으로
이진 탐색을 진행한다. 이때 entrySelector 값을 통해 반복 회수를 알 수 있다.
rangeShift부터 마지막 까지 다시 이진 탐색을 진행한다.
마찬가지로 entrySelector 만큼의 반복 회수를 가진다.

###### sfntVersion
Glyph는 닫힌 폐곡선 집합이다.
사용된 곡선 공식에 따라 두 가지 버전을 명시할 수 있다.

| 구분 | 트루타입 윤곽선 (TrueType Outline) | CFF 데이터 (Compact Font Format) |
| :--- | :--- | :--- |
| sfntVersion | 0x00010000| 0x4F54544F |
| 곡선 공식 | 2차 베지에 곡선 (Quadratic Bezier) | 3차 베지에 곡선 (Cubic Bezier) |
| 제어점 수 | 곡선 하나당 제어 점 1개 사용 | 곡선 하나당 제어 점 2개 사용 |
| 특징 | 계산이 단순하여 화면 렌더링 속도가 매우 빠름 (애플/MS가 선호) | 수학적으로 더 정교한 곡선 표현이 가능하며 용량이 작음 (어도비/인쇄 업계 선호) |
| 데이터 테이블 | `glyf` 테이블에 윤곽선 데이터 저장 | CFF(또는 CFF2) 테이블에 데이터 저장 |
| 파일 확장자 | 보통 `.ttf` | 보통 `.otf` |

---

### TableRecord
| Type | Name | Description |
| :--- | :--- | :--- |
| `Tag` | `tableTag` | Table identifier. |
| `uint32` | `checksum` | Checksum for this table. |
| `Offset32` | `offset` | Offset from beginning of font file. |
| `uint32` | `length` | Length of this table. |

레코드의 실제 데이터는 offset을 보고 찾는다.

---





