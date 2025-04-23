---
title: WPF MVVM 프로젝트 구조 설계 방법
date: 2025-04-22 15:00:00 +0900
categories: [Programming, MVVM]
tags: [c#, wpf, mvvm, project structure]
description: Project Structure
#image:
#  src: https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/1200px-Markdown-mark.svg.png
#  width: 1000   # in pixels
#  height: 400   # in pixels
#  alt: image alternative text
---
> 프로젝트 구성을 했던 경험을 공유합니다.

## 프로젝트 구성 (Project Structure)

### Core
- **EFCore**
데이터베이스 연동을 위한 Entity Framework Core 모듈
- **Proxy**
외부 통신을 담당하는 모듈 그룹
  - **FTP**: FPT 파일 전송 기능
  - **Socket**: 소켓 통신 처리 기능
  - **Redis**: Redis 캐시 서버와의 통신 기능
### Log
   - **Log**
lot4net 을 사용하여 애플리케이션 로깅 기능을 담당합니다.
### Service
 - **Service**
비즈니스 로직을 처리하는 핵심 서비스 계층입니다.
(DB 처리, 외부 API 연동 등)
### Studio
- **Anaylzer**
데이터 분석 기능 제공하는 애플리케이션
- **Monitoring**
시스템 상태 모니터링 기능
- **Router**
메시지 및 데이터 라우터 기능
- **LogViewer**
로그 데이터를 시각화하여 조회할 수 있는 뷰어
### Support
- **Support**
공통 유틸리티, 헬퍼 메서드 등을 제공하는 지원 모듈.
Custom Control 을 사용하여 원하는 UI Control 을 제공함
### Test
- **Simuliator**
테스트 및 시뮬레이션을 위한 도구 모듈

## 요약
- **Core**: 핵심 통신 데이터 관리
- **Service**: 비즈니스 로직 처리
-  **Studio**: 데이터 분석 및 모니터링
-  **Support**: 공통 기능 지원
- **Test**: 테스트 환경 구축
> **=> 기능별로 명확히 분리하여 유지보수성과 확장성을 고려한 구조**