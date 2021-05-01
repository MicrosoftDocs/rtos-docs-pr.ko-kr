---
title: 1장 - Azure RTOS NetX DHCP 클라이언트 소개
description: NetX에서 애플리케이션의 IP 주소는 nx_ip_create 서비스 호출에 제공된 매개 변수 중 하나입니다.
author: philmea
ms.author: philmea
ms.date: 06/04/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 44eb764c84a15a1bc96cf94bcbc8f81be7b41eef
ms.sourcegitcommit: e3d42e1f2920ec9cb002634b542bc20754f9544e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104811622"
---
# <a name="chapter-1---introduction-to-azure-rtos-netx-dhcp-client"></a><span data-ttu-id="8b91b-103">1장 - Azure RTOS NetX DHCP 클라이언트 소개</span><span class="sxs-lookup"><span data-stu-id="8b91b-103">Chapter 1 - Introduction to Azure RTOS NetX DHCP Client</span></span>

<span data-ttu-id="8b91b-104">NetX에서 애플리케이션의 IP 주소는 *nx_ip_create* 서비스 호출에 제공된 매개 변수 중 하나입니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-104">In NetX, the application’s IP address is one of the supplied parameters to the *nx_ip_create* service call.</span></span> <span data-ttu-id="8b91b-105">IP 주소를 정적 또는 사용자 구성을 통해 애플리케이션에 알려진 경우 IP 주소를 제공해도 문제가 발생하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-105">Supplying the IP address poses no problem if the IP address is known to the application, either statically or through user configuration.</span></span> <span data-ttu-id="8b91b-106">그러나 애플리케이션이 해당 IP 주소를 인식하지 못하거나 담당하지 않는 경우가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-106">However, there are some instances where the application doesn’t know or care what its IP address is.</span></span> <span data-ttu-id="8b91b-107">이러한 경우 *nx_ip_create* 함수에 제로 IP 주소를 제공해야 하고 Azure RTOS DHCP 클라이언트 프로토콜을 사용하여 IP 주소를 동적으로 가져와야 합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-107">In such situations, a zero IP address should be supplied to the *nx_ip_create* function and the Azure RTOS DHCP Client protocol should be used to dynamically obtain an IP address.</span></span>

## <a name="dynamic-ip-address-assignment"></a><span data-ttu-id="8b91b-108">동적 IP 주소 할당</span><span class="sxs-lookup"><span data-stu-id="8b91b-108">Dynamic IP Address Assignment</span></span>

<span data-ttu-id="8b91b-109">네트워크에서 동적 IP 주소를 가져오는 데 사용되는 기본 서비스는 RARP(역방향 주소 확인 프로토콜)입니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-109">The basic service used to obtain a dynamic IP address from the network is the Reverse Address Resolution Protocol (RARP).</span></span> <span data-ttu-id="8b91b-110">이 프로토콜은 다른 네트워크 노드의 MAC 주소를 찾는 대신 자체 IP 주소를 가져오도록 설계되었다는 점을 제외하면, ARP와 유사합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-110">This protocol is similar to ARP, except it is designed to obtain an IP address for itself instead of finding the MAC address for another network node.</span></span> <span data-ttu-id="8b91b-111">하위 수준 RARP 메시지는 로컬 네트워크에서 브로드캐스트되며 동적으로 할당된 IP 주소를 포함하는 RARP 응답으로 응답하는 것은 네트워크 상의 서버가 담당합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-111">The low-level RARP message is broadcast on the local network and it is the responsibility of a server on the network to respond with an RARP response, which contains a dynamically allocated IP address.</span></span>

<span data-ttu-id="8b91b-112">RARP는 IP 주소를 동적으로 할당하는 서비스를 제공하지만, 몇 가지 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-112">Although RARP provides a service for dynamic allocation of IP addresses, it has several shortcomings.</span></span> <span data-ttu-id="8b91b-113">가장 두드러진 결점은 RARP가 IP 주소의 동적 할당만 제공한다는 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-113">The most glaring deficiency is that RARP only provides dynamic allocation of the IP address.</span></span> <span data-ttu-id="8b91b-114">대부분의 경우 디바이스가 네트워크에 제대로 참여하려면 추가 정보가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-114">In most situations, more information is necessary in order for a device to properly participate on a network.</span></span> <span data-ttu-id="8b91b-115">대부분의 디바이스에는 IP 주소 외에도 네트워크 마스크와 게이트웨이 IP 주소가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-115">In addition to an IP address, most devices need the network mask and the gateway IP address.</span></span> <span data-ttu-id="8b91b-116">DNS 서버의 IP 주소 및 기타 네트워크 정보도 필요할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-116">The IP address of a DNS server and other network information may also be needed.</span></span> <span data-ttu-id="8b91b-117">RARP에는 이 정보를 제공하는 기능이 없습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-117">RARP does not have the ability to provide this information.</span></span>

## <a name="rarp-alternatives"></a><span data-ttu-id="8b91b-118">RARP 대안</span><span class="sxs-lookup"><span data-stu-id="8b91b-118">RARP Alternatives</span></span>

<span data-ttu-id="8b91b-119">연구원들은 RARP의 결함을 극복하기 위해 BOOTP(부트 스트랩 프로토콜)라고 하는 더 포괄적인 IP 주소 할당 메커니즘을 개발했습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-119">In order to overcome the deficiencies of RARP, researchers developed a more comprehensive IP address allocation mechanism called the Bootstrap Protocol (BOOTP).</span></span> <span data-ttu-id="8b91b-120">이 프로토콜에는 IP 주소를 동적으로 할당하고 중요한 추가 네트워크 정보도 제공하는 기능이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-120">This protocol has the ability to dynamically allocate an IP address and also provide additional important network information.</span></span> <span data-ttu-id="8b91b-121">그러나 BOOTP는 정적 네트워크 구성을 위해 설계된다는 단점이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-121">However, BOOTP has the drawback of being designed for static network configurations.</span></span> <span data-ttu-id="8b91b-122">빠른 주소 할당 또는 자동 주소 할당을 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-122">It does not allow for quick or automated address assignment.</span></span>

<span data-ttu-id="8b91b-123">여기서 DHCP(Dynamic Host Configuration Protocol)가 매우 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-123">This is where the Dynamic Host Configuration Protocol (DHCP) is extremely useful.</span></span> <span data-ttu-id="8b91b-124">DHCP는 지정된 기간 동안 클라이언트에 IP 주소를 “임대”하여 완전히 자동화된 IP 서버 할당과 완전한 동적 IP 주소 할당을 포함하도록 BOOTP의 기본 기능을 확장하도록 설계되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-124">DHCP is designed to extend the basic functionality of BOOTP to include completely automated IP server allocation and completely dynamic IP address allocation through “leasing” an IP address to a client for a specified period of time.</span></span> <span data-ttu-id="8b91b-125">DHCP는 BOOTP와 같은 정적 방식으로 IP 주소를 할당하도록 구성할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-125">DHCP can also be configured to allocate IP addresses in a static manner like BOOTP.</span></span>

## <a name="dhcp-messages"></a><span data-ttu-id="8b91b-126">DHCP 메시지</span><span class="sxs-lookup"><span data-stu-id="8b91b-126">DHCP Messages</span></span>

<span data-ttu-id="8b91b-127">DHCP는 BOOTP의 기능을 크게 향상시키지만, DHCP는 BOOTP와 동일한 메시지 형식을 사용하며 BOOTP와 동일한 공급 업체 옵션을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-127">Although DHCP greatly enhances the functionality of BOOTP, DHCP uses the same message format as BOOTP and supports the same vendor options as BOOTP.</span></span> <span data-ttu-id="8b91b-128">DHCP는 해당 기능을 수행하기 위해 다음과 같은 7개의 새로운 DHCP 관련 옵션을 도입했습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-128">In order to perform its function, DHCP introduces seven new DHCP-specific options, as follows:</span></span>

- <span data-ttu-id="8b91b-129">DISCOVER(1) (DHCP 클라이언트에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-129">DISCOVER (1) (sent by DHCP Client)</span></span>

- <span data-ttu-id="8b91b-130">OFFER(2) (DHCP 서버에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-130">OFFER (2) (sent by DHCP Server)</span></span>

- <span data-ttu-id="8b91b-131">REQUEST(3) (DHCP 클라이언트에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-131">REQUEST (3) (sent by DHCP Client)</span></span>

- <span data-ttu-id="8b91b-132">DECLINE(4) (DHCP 클라이언트에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-132">DECLINE (4) (sent by DHCP Client)</span></span>

- <span data-ttu-id="8b91b-133">ACK(5) (DHCP 서버에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-133">ACK (5) (sent by DHCP Server)</span></span>

- <span data-ttu-id="8b91b-134">NACK(6) (DHCP 서버에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-134">NACK (6) (sent by DHCP Server)</span></span>

- <span data-ttu-id="8b91b-135">RELEASE(7) (DHCP 클라이언트에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-135">RELEASE (7) (sent by DHCP Client)</span></span>

- <span data-ttu-id="8b91b-136">INFORM(8) (DHCP 클라이언트에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-136">INFORM (8) (sent by DHCP Client)</span></span>

- <span data-ttu-id="8b91b-137">FORCERENEW(9) (DHCP 클라이언트에서 보냄)</span><span class="sxs-lookup"><span data-stu-id="8b91b-137">FORCERENEW (9) (sent by DHCP Client)</span></span>

## <a name="dhcp-communication"></a><span data-ttu-id="8b91b-138">DHCP 통신</span><span class="sxs-lookup"><span data-stu-id="8b91b-138">DHCP Communication</span></span>

<span data-ttu-id="8b91b-139">DHCP는 UDP 프로토콜을 활용하여 요청 및 필드 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-139">DHCP utilizes the UDP protocol to send requests and field responses.</span></span> <span data-ttu-id="8b91b-140">IP 주소를 유지하기 전에 DHCP 정보를 전달하는 UDP 메시지는 255.255.255.255의 IP 브로드캐스트 주소를 활용하여 송수신됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-140">Prior to having an IP address, UDP messages carrying the DHCP information are sent and received by utilizing the IP broadcast address of 255.255.255.255.</span></span>

## <a name="dhcp-client-state-machine"></a><span data-ttu-id="8b91b-141">DHCP 클라이언트 상태 시스템</span><span class="sxs-lookup"><span data-stu-id="8b91b-141">DHCP Client State Machine</span></span>

<span data-ttu-id="8b91b-142">DHCP 클라이언트는 상태 시스템으로 구현됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-142">The DHCP Client is implemented as a state machine.</span></span> <span data-ttu-id="8b91b-143">상태 시스템은 *nx_dhcp_create* 를 처리하는 동안 생성된 내부 DHCP 스레드에 의해 처리됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-143">The state machine is processed by an internal DHCP thread that is created during *nx_dhcp_create* processing.</span></span> <span data-ttu-id="8b91b-144">DHCP 클라이언트의 주요 상태는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-144">The main states of DHCP Client are as follows:</span></span>


- <span data-ttu-id="8b91b-145">**NX_DHCP_STATE_BOOT** 이전 IP 주소로 시작</span><span class="sxs-lookup"><span data-stu-id="8b91b-145">**NX_DHCP_STATE_BOOT** Starting with a previous IP address</span></span>

- <span data-ttu-id="8b91b-146">**NX_DHCP_STATE_INIT** 이전 IP 주소 값 없이 시작</span><span class="sxs-lookup"><span data-stu-id="8b91b-146">**NX_DHCP_STATE_INIT** Starting with no previous   IP address value</span></span>

- <span data-ttu-id="8b91b-147">**NX_DHCP_STATE_SELECTING** DHCP 서버의 응답을 기다리는 중</span><span class="sxs-lookup"><span data-stu-id="8b91b-147">**NX_DHCP_STATE_SELECTING** Waiting for a response from any DHCP server</span></span>

- <span data-ttu-id="8b91b-148">**NX_DHCP_STATE_REQUESTING** DHCP 서버가 식별됨, IP 주소 요청 전송됨</span><span class="sxs-lookup"><span data-stu-id="8b91b-148">**NX_DHCP_STATE_REQUESTING** DHCP Server identified, IP address request sent</span></span>

- <span data-ttu-id="8b91b-149">**NX_DHCP_STATE_BOUND** DHCP IP 주소 임대가 설정됨</span><span class="sxs-lookup"><span data-stu-id="8b91b-149">**NX_DHCP_STATE_BOUND** DHCP IP Address lease established</span></span>

- <span data-ttu-id="8b91b-150">**NX_DHCP_STATE_RENEWING** DHCP IP 주소 임대 갱신 시간이 경과됨, 갱신이 요청됨</span><span class="sxs-lookup"><span data-stu-id="8b91b-150">**NX_DHCP_STATE_RENEWING** DHCP IP Address lease renewal time elapsed, renewal requested</span></span>

- <span data-ttu-id="8b91b-151">**NX_DHCP_STATE_REBINDING** DHCP IP 주소 임대 리바인딩 시간이 경과됨, 갱신이 요청됨</span><span class="sxs-lookup"><span data-stu-id="8b91b-151">**NX_DHCP_STATE_REBINDING** DHCP IP Address lease rebind time elapsed, renewal requested</span></span>

- <span data-ttu-id="8b91b-152">**NX_DHCP_STATE_FORCERENEW** DHCP IP 주소 임대가 설정됨, 서버(현재 지원되지 않음) 또는 nx_dhcp_force_renew를 호출하는 애플리케이션에 의해 강제 갱신됨</span><span class="sxs-lookup"><span data-stu-id="8b91b-152">**NX_DHCP_STATE_FORCERENEW** DHCP IP Address lease established, force renewal by server (currently not supported) or by the application calling nx_dhcp_force_renew</span></span>

- <span data-ttu-id="8b91b-153">**NX_DHCP_STATE_ADDRESS_PROBING** DHCP IP 주소 검색, ARP 프로브를 보내 IP 주소 충돌을 감지합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-153">**NX_DHCP_STATE_ADDRESS_PROBING** DHCP IP Address probing, send the ARP probe to detect IP address conflict.</span></span>

## <a name="dhcp-client-multiple-interface-support"></a><span data-ttu-id="8b91b-154">DHCP 클라이언트 다중 인터페이스 지원</span><span class="sxs-lookup"><span data-stu-id="8b91b-154">DHCP Client Multiple Interface Support</span></span>

<span data-ttu-id="8b91b-155">DHCP 클라이언트는 이전에 단일 네트워크 인터페이스에서만 실행되도록 구현되었습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-155">The DHCP Client was previously implemented to run on only a single network interface.</span></span> <span data-ttu-id="8b91b-156">기본 동작은 DHCP 클라이언트를 기본 인터페이스에서 실행하기 위한 것입니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-156">The default behavior was (and still is) for the DHCP Client to run on the primary interface.</span></span> <span data-ttu-id="8b91b-157">애플리케이션은 *nx_dhcp_set_interface_index* 를 호출하여 기본 인터페이스 대신 보조 네트워크 인터페이스에서 DHCP를 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-157">By calling *nx_dhcp_set_interface_index*, the application could (and still can) run DHCP on a secondary network interface instead of the primary interface.</span></span>

<span data-ttu-id="8b91b-158">이제 여러 인터페이스에서 병렬로 실행되는 DHCP를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-158">It now supports DHCP running on multiple interfaces in parallel.</span></span> <span data-ttu-id="8b91b-159">둘 이상의 물리적 인터페이스에서 DHCP 클라이언트를 동시에 실행하는 방법에 대한 자세한 내용은 2장의 **동시에 여러 인터페이스의 DHCP 클라이언트** 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="8b91b-159">See **DHCP Client on Multiple Interfaces Simultaneously** in Chapter Two for specific details how to run DHCP Client on more than one physical interface simultaneously.</span></span>

## <a name="dhcp-user-request"></a><span data-ttu-id="8b91b-160">DHCP 사용자 요청</span><span class="sxs-lookup"><span data-stu-id="8b91b-160">DHCP User Request</span></span>

<span data-ttu-id="8b91b-161">DHCP 서버에서 IP 주소를 부여하면 *nx_dhcp_user_option_request* 서비스를 사용하여 DHCP 클라이언트 처리에서 한 번에 하나씩 추가 매개 변수를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-161">Once the DHCP server grants an IP address, the DHCP client processing can request additional parameters — one at a time — by using the *nx_dhcp_user_option_request* service.</span></span>

## <a name="dhcp-client-socket-queue"></a><span data-ttu-id="8b91b-162">DHCP 클라이언트 소켓 큐</span><span class="sxs-lookup"><span data-stu-id="8b91b-162">DHCP Client Socket Queue</span></span> 

<span data-ttu-id="8b91b-163">DHCP 클라이언트는 서버가 자체 응답을 기다리는 동안 소켓 수신 큐에서 다른 DHCP 클라이언트용으로 지정된 DHCP 서버에서 브로드캐스트 패킷을 자동으로 지웁니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-163">The DHCP Client automatically clears broadcast packets from DHCP Servers intended for other DHCP Clients from its socket receive queue while waiting for Server to respond to itself.</span></span> <span data-ttu-id="8b91b-164">사용 중인 네트워크에서 이 작업을 수행하지 않으면 클라이언트용 패킷이 삭제될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-164">In a busy network, not doing so could cause packets intended for the Client to be dropped.</span></span>

## <a name="dhcp-rfcs"></a><span data-ttu-id="8b91b-165">DHCP RFC</span><span class="sxs-lookup"><span data-stu-id="8b91b-165">DHCP RFCs</span></span>

<span data-ttu-id="8b91b-166">NetX DHCP는 RFC2132, RFC2131 및 관련 RFC와 호환됩니다.</span><span class="sxs-lookup"><span data-stu-id="8b91b-166">NetX DHCP is compliant with RFC2132, RFC2131, and related RFCs.</span></span>
