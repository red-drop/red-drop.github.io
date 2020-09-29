---
layout: post
title:  "DB Isolation Level(트랜젝션 격리수준)"
author: red
categories: [ DB ]
image: assets/images/11.jpg
description: "DB 트랜젝션 격리 수준에 대한 정리"
featured: true
hidden: true
---

# Goal
- DB Isolation Level 을 이해한다.
- DB Trasaction과 Isolataion 의 관계에 대해 이해한다.

# 트랜잭션 격리 수준 (Transaction isolation lock)
Thread에서 공유 자원에 동시 접근을 막기 위해 Lock을 걸듯이 DB에서도 트랜젝션간의 Lock을 설정 할 수 있다. 하지만 Lock은 동시처리량에 영향을 미치게 됨으로, 필요한 수준의 Lock을 적절히 설정해 데이터 정합성을 유지하면서 최대한의 성능을 내는 것이 중요하다.

트랜잭션 격리 수준에 따라 이 부분을 제어할 수 있다.
