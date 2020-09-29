---
layout: post
title:  "DB Transaction에 대하여"
author: red
categories: [ DB ]
image: assets/images/11.jpg
description: "DB 트랜잭션에 대한 정리"
featured: true
hidden: true
---

# Goal
- DB Transaction 을 이해한다.
- DB Transaction 의 특징에 대해 이해한다.

# Transaction 이란
트랜잭션(Transaction)이란 데이터베이스 상태를 변화시키는 작업의 단위이며,  논리적인 작업의 쿼리 수에 관계 없이, 작업 셋 자체가 100% 적용되거나(COMMIT 시) 아무것도 적용되지 않아야 함을(ROLLBACK) 보장해 주는 것이다.

# 스토리지엔진에 따른 Transaction
트랜잭션을 지원하지 않는 스토리지 엔진을 사용할 시, 부분 업데이트(Partial Update)가 발생 해 테이블 정합성을 맞추기가 어려워진다.

예를 들어 다음과 같이 Primary Key만 가진 테이블이 있다.
```ruby
+-----+
| pk  |
+-----+
|  3  |
+-----+
```
위의 테이블에 1,2,3 을 INSERT 하는 SQL을 발행할 시 다음과 같은 에러가 발생 할 것이다.

```sql
INSERT INTO non_transaction_store_table (pk) VALUES (1), (2), (3)

Duplicate entry '3' for key 'PRIMARY'
```
그런데 만약 트랜잭션을 지원하지 않는 엔진이라면 1,2 가 INSERT 된 채 남게 된다. 이와 같은 부분 업데이트 현상이 발생하면 실패한 쿼리로 인해 남은 레코드를 다시 삭제하는 재처리 작업이 필요해질 수 있고, 복수의 쿼리를 실행했을 시 재처리 작업은 점점 불어나게 될 것이다.

만약 트랜잭션을 지원하는 엔진을 사용하게 된다면, `Duplicate entry` 가 발생 했을 시 1과 2도 테이블에 남지 않게 되므로, 데이터의 정합성을 확보 할 수 있다.
```sql
START TRANSACTION;
QUERY1...
QUERY2...
...
COMMIT;
```

# 트랜잭션의 특징
트랜잭션의 특징은 논리적 작업의 원자성이 보장되는 것이며, 크게 4가지(ACID)로 구분된다.
- 원자성 (Atomicity)
- 일관성 (Consistency)
- 고립성 (Isolation성
- 지속성 (Durability)

### 원자성 (Atomicity)
트랜잭션은 논리적 작업의 단위로서 실행 시 데이터베이스에 모두 반영되던가, 아니면 전혀 반영되지 않아야 한다는 것이다. 

예를 들어 계좌이체 트랜잭션은 실패할 수 있지만, 인출만 성공하고 입금이 실패하는 경우는 없어야 한다는 것이다. 계좌이체 라는 트랜잭션은 인출과 입금을 하나의 논리적 단위로 설정하고 데이터의 정합성을 보호한다.

### 일관성 (Consistency)
트랜잭션에서 데이터는 미리 정의된 규칙안에서만 수정이 가능한 특성이다. Column과 다른 타입의 데이터를 넣을려 할 시 트랜잭션이 실패해 데이터의 일관성을 보장한다.

### 고립성 (Isolation)
복수의 트랜잭션이 동시 실행될 때 

### 지속성 (Durability)
성공한 트랜잭션(COMMIT)된 내용은 영원히 적용된다.


# 트랜잭션 사용 시 주의점
트랜잭션의 단위는 최소한의 단위로 해 주는 것이 좋다. 한 트랜잭션이 실행되면 끝날 때까지 커넥션을 반환하지 않고 다른 작업들이 대기 상태에 들어가는 상황이 벌어질 수 있다.

또한 CUD(Create Update Delete) 가 발생할 시 RecordLock이 걸리기 때문에 동시처리 성능도 저하되게 된다.
