# 재능 예약

# 목차

- [재능 판매](#---)
  - [서비스 시나리오](#서비스-시나리오)
  - [분석/설계](#분석설계)
  - [구현:](#구현-)
    - [DDD 의 적용](#ddd-의-적용)
    - [폴리글랏 퍼시스턴스](#폴리글랏-퍼시스턴스)
    - [폴리글랏 프로그래밍](#폴리글랏-프로그래밍)
    - [동기식 호출 과 Fallback 처리](#동기식-호출-과-Fallback-처리)
    - [비동기식 호출 과 Eventual Consistency](#비동기식-호출-과-Eventual-Consistency)
  - [운영](#운영)
    - [CI/CD 설정](#cicd설정)
    - [동기식 호출 / 서킷 브레이킹 / 장애격리](#동기식-호출-서킷-브레이킹-장애격리)
    - [오토스케일 아웃](#오토스케일-아웃)
    - [무정지 재배포](#무정지-재배포)
  - [신규 개발 조직의 추가](#신규-개발-조직의-추가)

# 서비스 시나리오

재능 예약 시스템의 기능적, 비기능적 요구사항은 다음과 같습니다. 사용자가 원하는 재능을 예약한 후 결제를 완료합니다. 담당자는 예약 내역을 확인한 후 확정합니다. 사용자는 예약 현황을 확인할 수 있습니다.

기능적 요구사항

1. 사용자는 원하는 재능을 예약한다.
2. 사용자가 결제를 완료하면 예약이 완료된다.
3. 사용자가 결제를 완료하지 못하면 예약이 취소된다.
4. 예약이 완료되면 예약 상세 내역이 담당자에게 전달된다.
5. 담당자는 내역을 확인하고 확정한다.
6. 사용자는 예약 현황을 조회할 수 있다.
7. 사용자는 중간에 예약을 취소할 수 있으며, 해당 예약은 삭제된다.

비기능적 요구사항

1. 담당자 부재 등으로 예약 내역을 확정할 수 없더라도 사용자는 중단 없이 재능을 예약할 수 있다. (Event Pub/Sub)
2. 결제가 완료되어야 예약이 완료된다. (Req/Res)
3. 부하 발생 시 자동으로 처리 프로세스를 증가시킨다. (Autoscale)