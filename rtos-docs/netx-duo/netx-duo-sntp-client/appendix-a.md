---
title: 부록 A - Azure RTOS NetX Duo SNTP 치명적인 오류 코드
description: 다음 오류 코드로 인해 Azure RTOS NetX Duo SNTP 클라이언트가 현재 서버와의 시간 업데이트를 중단합니다.
author: philmea
ms.author: philmea
ms.date: 06/04/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 065e7a3e65b3cf8d7fcfb34bb9568a673791609a
ms.sourcegitcommit: e3d42e1f2920ec9cb002634b542bc20754f9544e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104810578"
---
# <a name="appendix-a---azure-rtos-netx-duo-sntp-fatal-error-codes"></a><span data-ttu-id="202ca-103">부록 A - Azure RTOS NetX Duo SNTP 치명적인 오류 코드</span><span class="sxs-lookup"><span data-stu-id="202ca-103">Appendix A - Azure RTOS NetX Duo SNTP Fatal Error Codes</span></span>

<span data-ttu-id="202ca-104">다음 오류 코드로 인해 Azure RTOS NetX Duo SNTP 클라이언트가 현재 서버와의 시간 업데이트를 중단합니다.</span><span class="sxs-lookup"><span data-stu-id="202ca-104">The following error codes will result in the Azure RTOS NetX Duo SNTP Client aborting time updates with the current server.</span></span> <span data-ttu-id="202ca-105">애플리케이션은 SNTP 클라이언트의 사용 가능한 서버 목록에서 현재 서버를 제거할지 결정하거나 서버 목록의 사용 가능한 다음 서버로 전환합니다.</span><span class="sxs-lookup"><span data-stu-id="202ca-105">It is up to the application to decide if the server should be removed from the SNTP Client list of available servers, or simply switch to the next available server on the list.</span></span> <span data-ttu-id="202ca-106">각 오류 상태의 정의는 \*nxd_sntp_client.h에 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="202ca-106">The definition of each error status is defined in \*nxd_sntp_client.h.</span></span> *

<span data-ttu-id="202ca-107">SNTP 클라이언트가 아래 목록의 오류를 애플리케이션으로 반환하면 서버를 다른 서버로 바꿔야 합니다.</span><span class="sxs-lookup"><span data-stu-id="202ca-107">When the SNTP Client returns an error from the list below to the application, the Server should probably be replaced with another Server.</span></span> <span data-ttu-id="202ca-108">NX_SNTP_KOD_REMOVE_SERVER 오류 상태는 SNTP 클라이언트 KOD(Kiss of Death) 처리기(콜백 함수)에 표시되어 다음을 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="202ca-108">Note that the NX_SNTP_KOD_REMOVE_SERVER error status is left to the SNTP Client kiss of death handler (callback function) to set:</span></span>

- <span data-ttu-id="202ca-109">NX_SNTP_KOD_REMOVE_SERVER 0xD0C</span><span class="sxs-lookup"><span data-stu-id="202ca-109">NX_SNTP_KOD_REMOVE_SERVER 0xD0C</span></span>  
- <span data-ttu-id="202ca-110">NX_SNTP_SERVER_AUTH_FAIL 0xD0D</span><span class="sxs-lookup"><span data-stu-id="202ca-110">NX_SNTP_SERVER_AUTH_FAIL 0xD0D</span></span>  
- <span data-ttu-id="202ca-111">NX_SNTP_INVALID_NTP_VERSION 0xD11</span><span class="sxs-lookup"><span data-stu-id="202ca-111">NX_SNTP_INVALID_NTP_VERSION 0xD11</span></span>  
- <span data-ttu-id="202ca-112">NX_SNTP_INVALID_SERVER_MODE 0xD12</span><span class="sxs-lookup"><span data-stu-id="202ca-112">NX_SNTP_INVALID_SERVER_MODE 0xD12</span></span>  
- <span data-ttu-id="202ca-113">NX_SNTP_INVALID_SERVER_STRATUM 0xD15</span><span class="sxs-lookup"><span data-stu-id="202ca-113">NX_SNTP_INVALID_SERVER_STRATUM 0xD15</span></span>  

<span data-ttu-id="202ca-114">SNTP 클라이언트가 아래 목록의 오류를 애플리케이션으로 반환하면 서버가 일시적으로 유효한 시간 업데이트를 제공하지 못할 수 있지만 서버를 제거할 필요는 없습니다.</span><span class="sxs-lookup"><span data-stu-id="202ca-114">When the SNTP Client returns an error from the list below to the application, the Server may only temporarily be unable to provide valid time updates and need not be removed:</span></span>

- <span data-ttu-id="202ca-115">HNX_SNTP_NO_UNICAST_FROM_SERVER 0xD09</span><span class="sxs-lookup"><span data-stu-id="202ca-115">HNX_SNTP_NO_UNICAST_FROM_SERVER 0xD09</span></span>  
- <span data-ttu-id="202ca-116">NX_SNTP_SERVER_CLOCK_NOT_SYNC 0xD0A</span><span class="sxs-lookup"><span data-stu-id="202ca-116">NX_SNTP_SERVER_CLOCK_NOT_SYNC 0xD0A</span></span>  
- <span data-ttu-id="202ca-117">NX_SNTP_KOD_SERVER_NOT_AVAILABLE 0xD0B</span><span class="sxs-lookup"><span data-stu-id="202ca-117">NX_SNTP_KOD_SERVER_NOT_AVAILABLE 0xD0B</span></span>  
- <span data-ttu-id="202ca-118">NX_SNTP_OVER_BAD_UPDATE_LIMIT 0xD17</span><span class="sxs-lookup"><span data-stu-id="202ca-118">NX_SNTP_OVER_BAD_UPDATE_LIMIT 0xD17</span></span>  
- <span data-ttu-id="202ca-119">NX_SNTP_BAD_SERVER_ROOT_DISPERSION 0xD16</span><span class="sxs-lookup"><span data-stu-id="202ca-119">NX_SNTP_BAD_SERVER_ROOT_DISPERSION 0xD16</span></span>  
- <span data-ttu-id="202ca-120">NX_SNTP_INVALID_RTT_TIME 0xD21</span><span class="sxs-lookup"><span data-stu-id="202ca-120">NX_SNTP_INVALID_RTT_TIME 0xD21</span></span>  
- <span data-ttu-id="202ca-121">NX_SNTP_KOD_SERVER_NOT_AVAILABLE 0xD24</span><span class="sxs-lookup"><span data-stu-id="202ca-121">NX_SNTP_KOD_SERVER_NOT_AVAILABLE 0xD24</span></span>