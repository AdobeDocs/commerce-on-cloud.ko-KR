---
title: 기존 Commerce 코드 가져오기
description: 기존 Commerce 코드를 새 클라우드 인프라 프로젝트로 가져오는 방법을 알아봅니다.
hide: true
hidefromtoc: 'yes'
recommendations: noDisplay, noCatalog
product_v2:
  - id: eadea719-cf89-469b-a6fd-a236a7138047
    internal-label: Commerce
role_v2:
  - id: c66ffd68-0f65-42bb-aa23-b4020f12e0bd
    internal-label: Admin
  - id: ff6a42d2-313e-452e-93a6-792e4fad9ff8
    internal-label: Developer
source-git-commit: e6e0bd8e116b2f0b93557b6aeb2aac7cbb8e1d8a
workflow-type: tm+mt
source-wordcount: '97'
ht-degree: 0%
---

# 기존 Commerce 코드 가져오기

Adobe에서는 빈 템플릿에서 또는 기존 코드를 가져와서 클라우드 인프라 프로젝트에 대한 Commerce을 만들 것을 권장합니다. 먼저 빈 템플릿으로 시작한 다음 기존 코드를 새 프로젝트로 가져오는 것이 좋습니다. 이렇게 하면 프로젝트를 지원하는 데 필요한 모든 파일이 확보됩니다.

기존 코드를 가져오기 위한 전체 워크플로에는 다음 단계가 포함됩니다.

- 템플릿 코드 바꾸기
- 데이터베이스 및 미디어 콘텐츠 가져오기
- 캐시를 지우고 가져오기를 확인합니다
