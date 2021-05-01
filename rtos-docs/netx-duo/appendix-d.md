---
title: 부록 D - Azure RTOS NetX Duo BSD 호환 소켓 API
description: IPv4와 IPv6용 BSD 호환 소켓 API에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 05/19/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 82439184d9facb6ddcc08ce81bc51182d7f34429
ms.sourcegitcommit: e3d42e1f2920ec9cb002634b542bc20754f9544e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104811047"
---
# <a name="appendix-d---azure-rtos-netx-duo-bsd-compatible-socket-api"></a><span data-ttu-id="6905b-103">부록 D - Azure RTOS NetX Duo BSD 호환 소켓 API</span><span class="sxs-lookup"><span data-stu-id="6905b-103">Appendix D - Azure RTOS NetX Duo BSD-Compatible Socket API</span></span>

## <a name="bsd-compatible-socket-api"></a><span data-ttu-id="6905b-104">BSD 호환 소켓 API</span><span class="sxs-lookup"><span data-stu-id="6905b-104">BSD-Compatible Socket API</span></span> 
<span data-ttu-id="6905b-105">BSD 호환 소켓 API는 아래의 NetX Duo&reg; 기본 형식을 활용하여 (몇 가지 제한 사항을 포함한) BSD 소켓 API 호출의 하위 집합을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-105">The BSD-Compatible Socket API supports a subset of the BSD Sockets API calls (with some limitations) by utilizing NetX Duo&reg; primitives underneath.</span></span> <span data-ttu-id="6905b-106">IPv6와 IPv4 프로토콜 둘 다와 네트워크 주소 지정이 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-106">Both IPv6 and IPv4 protocols and network addressing are supported.</span></span> <span data-ttu-id="6905b-107">BSD 호환 소켓 API 계층은 내부 NetX Duo 기본 형식을 활용하고 불필요한 NetX 오류 검사를 건너뛰므로 일반적인 BSD 구현보다 약간 빠르거나 동일한 속도로 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-107">This BSD-Compatible Sockets API layer should perform as fast or slightly faster than typical BSD implementations because this API utilizes internal NetX Duo primitives and bypasses unnecessary NetX error checking.</span></span>  

<span data-ttu-id="6905b-108">옵션을 구성할 수 있어 호스트 애플리케이션이 최대 소켓 수, TCP 최대 창 크기와 수신 대기 큐의 깊이를 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-108">Configurable options allow the host application to define the maximum number of sockets, TCP maximum window size, and depth of listen queue.</span></span>

<span data-ttu-id="6905b-109">성능과 아키텍처 제약 조건으로 인해 이 BSD 호환 소켓 API는 일부 BSD 소켓 호출을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-109">Due to performance and architecture constraints, this BSD-Compatible Sockets API does not support all BSD Sockets calls.</span></span> <span data-ttu-id="6905b-110">또한 특히 다음과 같이 일부 BSD 옵션은 BSD 서비스에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-110">In addition, not all BSD options are available for the BSD services, specifically the following:</span></span>

  - <span data-ttu-id="6905b-111">\***select** _ 호출은 fd_set \_readfds 인수만 사용할 수 있으며, 이 호출의 기타 인수(예: writefds, exceptfds)는 지원되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-111">\***select** _ call works with only fd_set \_readfds, other arguments in this call e.g., writefds, exceptfds are not supported.</span></span>
  - <span data-ttu-id="6905b-112">“int flags” 인수는 ***send** _, _*_recv_*_, _*_sendto,_*_, _ *_recvfrom_** 호출에 사용할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-112">The "int flags" argument is not supported for ***send** _, _*_recv_*_, _*_sendto,_*_ and _ *_recvfrom_** calls.</span></span> 
  - <span data-ttu-id="6905b-113">BSD 호환 소켓 API는 일부 BSD 소켓 호출만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-113">The BSD-Compatible Socket API supports only limited set of BSD Sockets calls.</span></span>

<span data-ttu-id="6905b-114">아래 소스 코드는 편의상 작성되었으며 ***nxd_bsd.c** _와 _*_nxd_bsd.h_\*_ 2개 파일로만 구성되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-114">The source code is designed for simplicity and is comprised of only two files, ***nxd_bsd.c** _ and _*_nxd_bsd.h_\*_.</span></span> <span data-ttu-id="6905b-115">설치하려면 (NetX 라이브러리가 아니라) 빌드 프로젝트에 해당 파일 2개를 추가하고 BSD 소켓 서비스 호출을 사용할 호스트 애플리케이션을 생성해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-115">Installation requires adding these two files to the build project (not the NetX library) and creating the host application which will use BSD Socket service calls.</span></span> <span data-ttu-id="6905b-116">_ *_nxd_bsd.h_*\* 파일은 애플리케이션 원본에도 포함해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-116">The _ *_nxd_bsd.h_*\* file must also be included in your application source.</span></span> <span data-ttu-id="6905b-117">IPv4와 IPv6 기반 애플리케이션의 샘플 데모 파일은 NetX Duo에서 무료로 제공하는 배포판에 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-117">Sample demo files for both IPv4 and IPv6  based applications are included with the distribution which is freely available with NetX Duo.</span></span> <span data-ttu-id="6905b-118">자세한 내용은 BSD 호환 소켓 API 패키지와 함께 제공되는 도움말과 추가 정보 파일에서 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-118">Further details are available in the help and Readme files bundled with the BSDCompatible Socket API package.</span></span>

<span data-ttu-id="6905b-119">BSD 호환 소켓 API는 아래의 BSD 소켓 API 호출을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6905b-119">The BSD-Compatible Sockets API supports the following BSD Sockets API calls:</span></span>

```c
INT     bsd_initialize (NX_IP *default_ip, NX_PACKET_POOL *default_pool, CHAR
        *bsd_memory_not_used);
```
```c
INT     getpeername( INT sockID, struct sockaddr *remoteAddress, INT
        *addressLength);
```
```c
INT     getsockname( INT sockID, struct sockaddr *localAddress, INT *addressLength);
```
```c
INT     recvfrom(INT sockID, CHAR *buffer, INT buffersize, INT flags,struct sockaddr
        *fromAddr, INT *fromAddrLen);
```
```c        
INT     recv(INT sockID, VOID *rcvBuffer, INT bufferLength, INT flags);
```
```c
INT     sendto(INT sockID, CHAR *msg, INT msgLength, INT flags, struct sockaddr
        *destAddr, INT destAddrLen);
```
```c        
INT     send(INT sockID, const CHAR *msg, INT msgLength, INT flags);
```
```c
INT     accept(INT sockID, struct sockaddr *ClientAddress, INT *addressLength);
```
```c
INT     listen(INT sockID, INT backlog);
```
```c
INT     bind (INT sockID, struct sockaddr *localAddress, INT addressLength);
```
```c
INT     connect(INT sockID, struct sockaddr *remoteAddress, INT addressLength);
```
```c
INT     socket( INT protocolFamily, INT type, INT protocol);
```
```c
INT     soc_close ( INT sockID);
```
```c
INT     select(INT nfds, fd_set *readfds, fd_set *writefds, fd_set *exceptfds, struct
        timeval *timeout);
```
```c
VOID    FD_SET(INT fd, fd_set *fdset);
```
```c
VOID    FD_CLR(INT fd, fd_set *fdset);
```
```c
INT     FD_ISSET(INT fd, fd_set *fdset);
```
```c
VOID    FD_ZERO(fd_set *fdset);
```