# 📁REST API 설계 가이드

> **REST (Representational State Transfer)**: 웹의 장점을 최대한 활용하기 위한 아키텍처 스타일.

---

## 1. REST API 설계 원칙 (기본)

#### 1) Resource(자원) 중심 설계
* **URI는 정보를 자원(명사)으로 표현**해야 한다. (동사 사용 금지)
    * `GET /getMembers` (X) -> `GET /members` (O)
    * `POST /deleteMember` (X) -> `DELETE /members/{id}` (O)
* **단수/복수(Singular/Plural) 규칙**:
    * 컬렉션(Collection, 리스트)은 **복수형**을 사용한다.
    * 예: `/members` (회원 목록), `/members/1` (1번 회원)

#### 2) 행위는 HTTP Method로 표현
* 자원에 대한 행위(CRUD)는 URL에 쓰지 않고 Method(GET, POST, PUT, DELETE)로 구분한다.
* URL에 `behavior`, `action` 등의 단어가 들어가면 안 된다.

#### 3) HATEOAS (Hypermedia As The Engine Of Application State)
* **개념**: 서버가 응답을 줄 때, **"다음 행동을 할 수 있는 링크(Link)"**를 함께 준다.
* **목적**: 클라이언트가 서버 URL 구조를 몰라도(하드코딩 하지 않아도) 응답에 있는 링크만 따라가면 상태 전이가 가능하도록 함.
* **예시 Response**:
```json
{
  "id": 1,
  "name": "user1",
  "links": [
    { "rel": "self", "href": "/users/1" },
    { "rel": "update", "href": "/users/1", "method": "PUT" },
    { "rel": "delete", "href": "/users/1", "method": "DELETE" }
  ]
}
```

---

## 2. REST API 심화 (Junior Interview 필수)

#### 1) Richardson Maturity Model (REST 성숙도 모델)
면접에서 "RESTful이란 무엇인가요?"라고 물었을 때 이 모델을 인용하면 좋습니다.

* **Level 0 (The Swamp of POX)**: HTTP를 단순히 RPC(원격 함수 호출) 운송 수단으로만 사용. 모든 요청을 `POST /api` 엔드포인트 하나로 처리하는 방식.
* **Level 1 (Resources)**: URL을 자원별로 구분(`users/`, `products/`)했지만, Method는 구별하지 않고 POST만 씀.
* **Level 2 (HTTP Verbs)**: **우리가 흔히 말하는 REST API**. Method(GET, POST, PUT, DELETE)와 Status Code를 올바르게 사용함.
* **Level 3 (Hypermedia Controls)**: **HATEOAS**까지 적용하여 완벽한 REST를 구현함.

#### 2) Self-descriptive Message (자체 표현 구조)
* REST API 메시지만 보고도 이것이 무엇인지 해석이 가능해야 한다.
* 보통 `Content-Type: application/json` 만으로는 불충분하며, API 명세(Profile) 링크 등을 헤더에 포함시켜 메시지를 자체적으로 설명해야 한다는 엄격한 정의도 있다.

#### 3) Stateless (무상태성)
* **서버는 클라이언트의 상태(Session)를 저장하지 않는다.**
* 모든 요청은 그 자체로 완벽해야 하며(인증 토큰 등을 포함), 서버는 요청만 보고 작업을 처리할 수 있어야 한다.
* 이로 인해 **서버 확장성(Scale-out)**이 매우 좋아진다. (어떤 서버로 요청이 가도 상관없음)