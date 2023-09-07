# REST API 설계가이드

## Method
- 검색: GET
- 생성: POST
- 업데이트: PUT? PATCH?
- 삭제: DELETE

## URL Rules
- 마지막에 `/` 를  포함하지 않는다.
```javascript
GOOD
    http://27.102.205.66:8390/accounts
BAD
    http://27.102.205.66:8390/accounts/
```
- 소문자 사용. 카멜케이스를 사용하지 않음.
```javascript
GOOD
    http://27.102.205.66:8390/account-groups
BAD
    http://27.102.205.66:8390/accountGroups
```
- `_` 가 아니라 `-` 를 사용한다
```javascript
GOOD
    http://27.102.205.66:8390/account-groups
BAD
    http://27.102.205.66:8390/account_groups
```
- 명사 사용
```javascript
GOOD
    http://27.102.205.66:8390/account
BAD
    http://27.102.205.66:8390/get-account
```
- Content-type 은 application/json
- 필터링(검색)이 필요한 경우는 query parameter 사용(Field-Selecting 활용)
  - https://docs.oracle.com/en/cloud/saas/cx-commerce/21b/ccdev/rest-api-query-parameters.html
```javascript
    http://27.102.205.66:8390/accounts?username=플럭시티
```

## HEADER
### 요청
- `JWT 사용에 따라 달라질 수 있음 `
### 응답
- 성공 응답은 2XX (기본 200(조회), 필요하면 201(생성), 202(수정), 204(삭제) 사용)
- 실패 응답은 4XX (401, 404, 405, 409 사용, 5XX 는 사용 안함)

## HATEOAS - `보류`
- HATEOAS(Hypermedia as the Engine of Application State) 사용: 클라이언트 애플리케이션이 새 리소스를 검색할 수 있도록 응답 본문에 관련 리소스에 대한 링크를 제공합니다.

## furthermore
- 리소스 이름에는 명사, 컬렉션에는 복수명사 사용 ( /users, /user/1 )
- 선택적 매개변수에 쿼리 매개변수 사용: 엔드포인트 URL을 단순하게 유지하고 선택적 매개변수에 쿼리 매개변수를 사용합니다.
- API 문서화: 예제 및 오류 코드를 포함하여 최종 사용자를 위한 명확하고 포괄적인 문서를 제공합니다. -> Swagger?
- URL을 간단하고 이해하기 쉽게 유지: 계층 구조를 사용하고 복잡한 쿼리 매개변수를 사용하지 마십시오.
- 보안 구현 (OAuth2, JWT, HTTPS, CORS, API 키, IP 제한 등)
- 실시간 업데이트 데이터를 제공한다.
- 다국어 지원
- binary data(이미지, 동영상 등) 지원
- multiple content type을 지원하도록
- SSL/TLS 암호화 사용: SSL/TLS와 같은 보안 통신 프로토콜을 사용하여 API 트래픽을 암호화하고 도청 및 변조를 방지합니다.