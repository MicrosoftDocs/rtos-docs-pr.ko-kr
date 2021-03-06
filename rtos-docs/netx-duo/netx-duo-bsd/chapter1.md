---
title: 1장 - Azure RTOS NetX Duo BSD 소개
description: BSD Socket API Compliancy Wrapper는 몇 가지 제한 사항이 있는 기본 BSD Socket API 호출 중 일부를 지원하며, 그 아래에 있는 Azure RTOS NetX Duo 기본 요소를 활용합니다.
author: philmea
ms.author: philmea
ms.date: 06/04/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: caf8d5374204bc553ac903f4720d3db402d9a10da5c26caa0fa67c4b5d340049
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116790499"
---
# <a name="chapter-1---introduction-to-azure-rtos-netx-duo-bsd"></a>1장 - Azure RTOS NetX Duo BSD 소개

BSD Socket API Compliancy Wrapper는 몇 가지 제한 사항이 있는 기본 BSD Socket API 호출 중 일부를 지원하며, 그 아래에 있는 Azure RTOS NetX Duo 기본 요소를 활용합니다.

## <a name="bsd-socket-api-compliancy-wrapper-source"></a>BSD Socket API Compliancy Wrapper 원본

Wrapper 소스 코드는 단순성을 위해 설계되었으며 *nxd_bsd.h* 및 *nxd_bsd.c* 라는 두 개의 파일로 구성되어 있습니다. *nxd_bsd.h* 파일은 필요한 모든 BSD Socket API 래퍼 상수와 서브루틴 프로토타입을 정의하는 반면, *nxd_bsd.c* 에는 실제 BSD Socket API 호환성 소스 코드가 포함되어 있습니다. 이러한 Wrapper 원본 파일은 모든 NetX Duo 지원 패키지에 공통적으로 제공됩니다.

패키지는 다음으로 구성됩니다.

- **nxd_bsd.c**: Wrapper 소스 코드
- **nxd_bsd.h**: 주 헤더 파일

샘플 데모 프로그램:

- **bsd_demo_udp.c**: *두 개의 UDP 피어가 있는 데모(IPv4 전용)*
- **bsd_demo_tcp.c**: *단일 TCP 서버 및 클라이언트가 있는 데모*
- **bsd_demo_raw.c**: *두 개의 RAW 피어가 있는 데모*