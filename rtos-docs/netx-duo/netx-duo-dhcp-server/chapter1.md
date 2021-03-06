---
title: 1장 - Azure RTOS NetX Duo DHCP 서버 소개
description: NetX Duo에서 애플리케이션의 IP 주소는 *nx_ip_create* 서비스 호출에 제공된 매개 변수 중 하나입니다.
author: philmea
ms.author: philmea
ms.date: 06/08/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: a56f692059cbd3e2a72d64cf80ee90a917b80987add8130d3a1df70b3b0c3a71
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116788476"
---
# <a name="chapter-1---introduction-to-azure-rtos-netx-duo-dhcp-server"></a>1장 - Azure RTOS NetX Duo DHCP 서버 소개

NetX Duo에서 애플리케이션의 IP 주소는 *nx_ip_create* 서비스 호출에 제공된 매개 변수 중 하나입니다. IP 주소가 정적으로 또는 사용자 구성을 통해 애플리케이션에 알려진 경우 IP 주소를 제공해도 문제가 발생하지 않습니다. 그러나 애플리케이션이 해당 IP 주소를 인식하지 못하거나 담당하지 않는 경우가 있습니다. 이러한 경우 *nx_ip_create* 함수에 제로 IP 주소를 제공해야 하며, 애플리케이션은 해당 DHCP 서버와 통신을 설정하여 IP 주소를 동적으로 요청하여 가져옵니다.

## <a name="dynamic-ip-address-assignment"></a>동적 IP 주소 할당

네트워크에서 동적 IP 주소를 가져오는 데 사용되는 기본 서비스는 RARP(역방향 주소 확인 프로토콜)입니다. 이 프로토콜은 다른 네트워크 노드의 MAC 주소를 찾는 대신 자체 IP 주소를 가져오도록 설계되었다는 점을 제외하면, ARP와 유사합니다. 하위 수준 RARP 메시지는 로컬 네트워크에서 브로드캐스트되며 동적으로 할당된 IP 주소를 포함하는 RARP 응답으로 응답하는 것은 네트워크 상의 서버가 담당합니다.

RARP는 IP 주소를 동적으로 할당하는 서비스를 제공하지만, 몇 가지 단점이 있습니다. 가장 두드러진 결점은 RARP가 IP 주소의 동적 할당만 제공한다는 것입니다. 대부분의 경우 디바이스가 네트워크에 제대로 참여하려면 추가 정보가 필요합니다. 대부분의 디바이스에는 IP 주소 외에도 네트워크 마스크와 게이트웨이 IP 주소가 필요합니다. DNS 서버의 IP 주소 및 기타 네트워크 정보도 필요할 수 있습니다. RARP에는 이 정보를 제공하는 기능이 없습니다.

## <a name="rarp-alternatives"></a>RARP 대안

연구원들은 RARP의 결함을 극복하기 위해 BOOTP(부트 스트랩 프로토콜)라고 하는 더 포괄적인 IP 주소 할당 메커니즘을 개발했습니다. 이 프로토콜에는 IP 주소를 동적으로 할당하고 중요한 추가 네트워크 정보도 제공하는 기능이 있습니다. 그러나 BOOTP는 정적 네트워크 구성을 위해 설계된다는 단점이 있습니다. 빠른 주소 할당 또는 자동 주소 할당을 허용하지 않습니다.

여기서 DHCP(Dynamic Host Configuration Protocol)가 매우 유용합니다. DHCP는 지정된 기간 동안 클라이언트에 IP 주소를 “임대”하여 완전히 자동화된 IP 서버 할당과 완전한 동적 IP 주소 할당을 포함하도록 BOOTP의 기본 기능을 확장하도록 설계되었습니다. DHCP는 BOOTP와 같은 정적 방식으로 IP 주소를 할당하도록 구성할 수도 있습니다.

## <a name="dhcp-messages"></a>DHCP 메시지

DHCP는 BOOTP의 기능을 크게 향상시키지만, DHCP는 BOOTP와 동일한 메시지 형식을 사용하며 BOOTP와 동일한 공급 업체 옵션을 지원합니다. DHCP는 해당 기능을 수행하기 위해 다음과 같은 7개의 새로운 DHCP 관련 옵션을 도입했습니다.

- DISCOVER(1) (DHCP 클라이언트에서 보냄)
- OFFER(2) (DHCP 서버에서 보냄)
- REQUEST(3) (DHCP 클라이언트에서 보냄)
- DECLINE(4) (DHCP 클라이언트에서 보냄)
- ACK(5) (DHCP 서버에서 보냄)
- NACK(6) (DHCP 서버에서 보냄)
- RELEASE(7) (DHCP 클라이언트에서 보냄)
- INFORM(8) (DHCP 클라이언트에서 보냄)
- FORCERENEW(9) (DHCP 클라이언트에서 보냄)

## <a name="dhcp-communication"></a>DHCP 통신

DHCP 서버는 UDP 프로토콜을 활용하여 DHCP 클라이언트 요청을 수신하고 응답을 전송합니다. IP 주소를 갖기 전에 DHCP 정보를 전달하는 UDP 메시지는 255.255.255.255의 IP 브로드캐스트 주소를 활용하여 송수신됩니다. 그러나 클라이언트가 DHCP 서버의 주소를 알고 있으면 유니캐스트 메시지를 사용하여 DHCP 메시지를 보낼 수 있습니다.

## <a name="dhcp-server-state-machine"></a>DHCP 서버 상태 시스템

DHCP 서버는 *nx_dhcp_server_create* 를 처리하는 동안 생성된 내부 DHCP 스레드에 의해 처리되는 2단계 상태 시스템으로 구현됩니다. DHCP 서버의 주 상태는 1) DHCP 클라이언트로부터 DISCOVER 메시지를 수신하고 2) REQUEST 메시지를 수신합니다.

해당 DHCP 클라이언트 상태는 다음과 같습니다.

- **NX_DHCP_STATE_BOOT** 이전 IP 주소로 시작
- **NX_DHCP_STATE_INIT** 이전 IP 주소 값 없이 시작
- **NX_DHCP_STATE_SELECTING** DHCP 서버의 응답을 기다리는 중
- **NX_DHCP_STATE_REQUESTING** DHCP 서버가 식별됨, IP 주소 요청 전송됨
- **NX_DHCP_STATE_BOUND** DHCP IP 주소 임대가 설정됨
- **NX_DHCP_STATE_RENEWING** DHCP IP 주소 임대 갱신 시간이 경과됨, 갱신이 요청됨
- **NX_DHCP_STATE_REBINDING** DHCP IP 주소 임대 리바인딩 시간이 경과됨, 갱신이 요청됨
- **NX_DHCP_STATE_FORCERENEW** DHCP IP 주소 임대가 설정됨, 서버 또는 애플리케이션에 의한 강제 갱신
- **NX_DHCP_STATE_FAILED** 서버를 찾을 수 없거나 서버에서 응답을 받지 않음

## <a name="dhcp-additional-parameters"></a>DHCP 추가 매개 변수

NetX Duo DHCP 서버에는 DHCP 클라이언트에 대한 라우터 또는 게이트웨이 주소와 DNS 서버와 같은 일반적인/중요한 네트워크 구성 매개 변수로 DHCP 클라이언트를 제공하기 위해 *nxd_dhcp_server.h* 의 구성 가능한 옵션 NX_DHCP_DEFAULT_SERVER_OPTION_LIST에 설정된 옵션 매개 변수의 기본 목록이 있습니다.

## <a name="dhcp-rfcs"></a>DHCP RFC

NetX Duo DHCP 서버는 RFC2132, RFC2131 및 관련 RFC와 호환됩니다.
