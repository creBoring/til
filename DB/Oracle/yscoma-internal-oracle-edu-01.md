# 'Yscoma' Internal Oracle Education - 01

> by yscoma

Oracle 기반으로 DB 기초지식부터 쿼리튜닝까지 진행되는 교육 정리본
<br>(생소한 내용에 대해)

* [세그먼트란?](#세그먼트란)
* [commit과 auto commit](#commit과-auto-commit)

---

## 세그먼트란?
세그먼트는 메타데이터를 관리하는 테이블입니다.

사용자가 insert, update, delete와 같은 쿼리문을 입력하면 아래와 같이 총 3 곳에 저장하게 됩니다.

- 실제 DB 공간
- Archive log
- 세그먼트

만약 사용자가 insert 문을 발생시키면, Oracle은 가장 먼저 실제 DB 공간에 먼저 저장시킵니다.
하지만, 이 데이터는 아직 사용자에게 확인되지 않으며 핸들링 할 수 없습니다.

그리고 insert 문에 대한 로그가 저장되며, 메타데이터는 **세그먼트** 에 저장됩니다.
마지막으로 commit이 이루어지면, DB에 저장 된 데이터는 사용자에게 쿼리될 수 있도록 업데이트됩니다.

쿼리문이 실제 DB에 반영되기 까지의 시나리오는 다음과 같습니다.

1. disk write ( 테이블 업데이트 )
2. 로그 생성 및 세그먼트 생성( Archive log 뿐만 아니라 모든 로그들 )
3. Action ( Commit, RoleBack )
 
## Commit과 auto commit
모든 RDB는 Commit 이 발생하기 전까지 Insert, Update, Delete 등에 대해 실제 DB에 업데이트하지 않습니다.
흔히 사용되는 MySQL, MSSQL 등도 Commit이 이루어지지 않고서는 DB에 변경사항을 반영하지 않습니다.

이 때 **Commit** 은 모든 작업을 정상적으로 처리하였음을 의미하며, 지금까지의 변경사항을 하나의 트랜잭션으로써 실제 DB에 반영하겠다는 뜻을 의미합니다.

> 트랜잭션 : 데이터 처리의 단위, 일련의 SQL 쿼리문을 논리적인 작업단위로 묶은 것

하지만 MySQL, MSSQL 등을 사용해보았다면, 사용자가 Commit을 직접 입력하지 않고도 변경사항이 잘 반영되었던 것을 경험할 수 있을 것입니다.
이는 **auto commit** 이라는 DB 옵션의 영향으로, 하나의 쿼리문이 실행될 때마다 자동으로 트랜잭션을 완료처리하여 DB에 즉시 반영해버립니다.
MySQL, MSSQL과 같은 대부분의 DB들은 이러한 auto commit이 default로 활성화되어 있기 때문에, 사용자가 commit을 입력하지 않아도 자동으로 데이터가 반영되었던 것입니다.

**Oracle** 은 다른 DB들과 다르게 default로 auto commit이 비활성화 되어 있습니다.

다만, auto commit을 통해 일일이 모두 DB에 반영해버리면, roll back 이 불가능하기 때문에 반드시 조심해야합니다.


> 작성일 : 2020-06-19
