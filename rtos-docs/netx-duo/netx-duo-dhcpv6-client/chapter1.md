---
title: 챕터 1 - Azure RTOS NetX Duo DHCPv6 클라이언트 소개
description: IPv6 네트워크에서 DHCPv6는 DHCP 대신 DHCPv6 서버에서 동적 글로벌 IP 주소를 할당받고, 대부분의 동일한 기능과 여러 향상 기능을 제공합니다.
author: philmea
ms.author: philmea
ms.date: 06/04/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 3bf3b52c53bb26e2c9c2c736ae35817eb967f609
ms.sourcegitcommit: e3d42e1f2920ec9cb002634b542bc20754f9544e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104810914"
---
# <a name="chapter-1---introduction-to-azure-rtos-netx-duo-dhcpv6-client"></a><span data-ttu-id="c22bc-103">챕터 1 - Azure RTOS NetX Duo DHCPv6 클라이언트 소개</span><span class="sxs-lookup"><span data-stu-id="c22bc-103">Chapter 1 - Introduction to Azure RTOS NetX Duo DHCPv6 Client</span></span>

<span data-ttu-id="c22bc-104">IPv6 네트워크에서 DHCPv6는 DHCP 대신 DHCPv6 서버에서 동적 글로벌 IP 주소를 할당받고, 대부분의 동일한 기능과 여러 향상 기능을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-104">In IPv6 networks, DHCPv6 replaces DHCP for dynamic global IP address assignment from a DHCPv6 Server, and offers most of the same features as well as many enhancements.</span></span> <span data-ttu-id="c22bc-105">이 문서에서는 Azure RTOS NetX Duo DHCPv6 클라이언트 API를 사용하여 IPv6 주소를 얻는 방법에 대해 자세히 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-105">This document will explain in detail how the Azure RTOS NetX Duo DHCPv6 Client API is used to obtain IPv6 addresses.</span></span>

## <a name="dhcpv6-communication"></a><span data-ttu-id="c22bc-106">DHCPv6 통신</span><span class="sxs-lookup"><span data-stu-id="c22bc-106">DHCPv6 Communication</span></span>

<span data-ttu-id="c22bc-107">DHCPv6 프로토콜은 UDP를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-107">The DHCPv6 protocol uses UDP.</span></span> <span data-ttu-id="c22bc-108">클라이언트는 546 포트를 사용하고, 서버는 547 포트를 사용하여 DHCPv6 메시지를 교환합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-108">The Client uses port 546 and the Server uses port 547 to exchange DHCPv6 messages.</span></span> <span data-ttu-id="c22bc-109">클라이언트는 원본 주소에 대한 링크 로컬 주소를 사용하여 사용 가능한 DHCPv6 서버에 대한 DHCPv6 요청을 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-109">The Client uses its link local address for a source address to initiate the DHCPv6 requests to the DHCPv6 server(s) available.</span></span> <span data-ttu-id="c22bc-110">클라이언트는 네트워크의 모든 DHCPv6 서버에 메시지를 보낼 때 *All_DHCP_Relay_Agents_and_Servers* 멀티 캐스트 주소 *FF02::01:02* 를 사용합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-110">When the Client sends messages intended for all DHCPv6 servers on the network it uses the *All_DHCP_Relay_Agents_and_Servers* multicast address *FF02::01:02*.</span></span> <span data-ttu-id="c22bc-111">이는 예약된 링크 범위의 멀티 캐스트 주소입니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-111">This is a reserved, link-scoped multicast address.</span></span>

## <a name="dhcpv6-process-of-requesting-an-ipv6-address"></a><span data-ttu-id="c22bc-112">DHCPv6의 IPv6 주소 요청 프로세스</span><span class="sxs-lookup"><span data-stu-id="c22bc-112">DHCPv6 Process of Requesting an IPv6 Address</span></span>

<span data-ttu-id="c22bc-113">클라이언트는 글로벌 IPv6 주소 할당을 요청하는 프로세스를 시작하기 위해 먼저 *nx_dhcpv6_send_solicit* 서비스를 사용하여 SOLICIT 메시지를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-113">To begin the process of requesting a global IPv6 address assignment, a Client first sends a SOLICIT message using the *nx_dhcpv6_send_solicit* service:</span></span>

<span data-ttu-id="c22bc-114">**UINT nx_dhcpv6_request_solicit(NX_DHCPV6 \*dhcpv6_ptr)**</span><span class="sxs-lookup"><span data-stu-id="c22bc-114">**UINT nx_dhcpv6_request_solicit(NX_DHCPV6 \*dhcpv6_ptr)**</span></span>

<span data-ttu-id="c22bc-115">이 메시지는 *All_DHCP_Relay_Agents_and_Servers* 주소를 사용하여 모든 서버에 전송됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-115">This message is sent to all servers using the *All_DHCP_Relay_Agents_and_Servers* address.</span></span> <span data-ttu-id="c22bc-116">SOLICIT 요청에서 클라이언트는 서버에 대한 힌트로 특정 IPv6 주소 할당을 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-116">In the SOLICIT request, the Client may request the assignment of specific IPv6 address(es) as a hint to the Server.</span></span> <span data-ttu-id="c22bc-117">또한 SOLICIT 메시지의 옵션 요청에서 DNS 서버, NTP 서버 및 기타 옵션과 같은 서버의 다른 네트워크 구성 정보를 요청할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-117">It can also request other network configuration information from the Server such as DNS server, NTP server and other options in the Option Request in the SOLICIT message.</span></span>

<span data-ttu-id="c22bc-118">클라이언트 요청을 처리할 수 있는 DHCPv6 서버는 클라이언트에 할당할 수 있는 IPv6 주소, IPv6 주소 임대 시간 및 클라이언트가 요청한 추가 정보가 포함된 ADVERTISE 메시지를 사용하여 응답합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-118">A DHCPv6 Server that can service a Client request responds with an ADVERTISE message containing the IPv6 address(es) it can assign to the Client, the IPv6 address lease time and any additional information requested by the Client.</span></span> <span data-ttu-id="c22bc-119">DHCPv6 클라이언트 프로토콜은 클라이언트가 네트워크의 모든 DHCPv6 서버에서 ADVERTISE 메시지를 받을 때까지 일정 시간을 기다릴 것을 요구합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-119">The DHCPv6 Client protocol requires the Client to wait for a period of time to receive ADVERTISE messages from all DHCPv6 Servers on the network.</span></span> <span data-ttu-id="c22bc-120">각 ADVERTISE 메시지를 유효한 메시지로 미리 처리하고, 다양한 DHCPv6 매개 변수의 옵션 데이터를 검색합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-120">It pre-processes each ADVERTISE message to be a valid message, and scans the option data for various DHCPv6 parameters.</span></span> <span data-ttu-id="c22bc-121">또한 서버에서 제공하는 경우 기본 설정 옵션의 기본 설정 값을 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-121">It also checks the Preference value in the Preference Option, if supplied by the Server.</span></span> <span data-ttu-id="c22bc-122">둘 이상의 ADVERTISE 메시지가 수신될 경우 NetX DHCPv6 클라이언트가 대기 기간이 끝날 때 수신된 기본 설정 값이 가장 높은 서버를 선택합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-122">If more than one ADVERTISE message is received, the NetX DHCPv6 Client chooses the Server with the highest preference value received by the end of the wait period.</span></span>

<span data-ttu-id="c22bc-123">클라이언트에서 기본 설정 값이 255인 ADVERTISE 메시지를 수신하는 경우는 예외입니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-123">The exception is if the Client receives an ADVERTISE message with a preference value of 255.</span></span> <span data-ttu-id="c22bc-124">클라이언트는 이 메시지를 즉시 수락하고 모든 후속 ADVERTISE 메시지를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-124">It accepts that message immediately and discards all subsequent ADVERTISE messages.</span></span>

<span data-ttu-id="c22bc-125">대기 기간은 DHCPv6 클라이언트가 서버에서 응답을 받지 못한 경우 다른 SOLICIT 메시지를 보내기 전에 대기하는 재전송 기간으로 정의됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-125">The wait period is defined as the retransmission period that the DHCPv6 Client waits before sending another SOLICIT message if it has not received a response from any Server.</span></span> <span data-ttu-id="c22bc-126">SOLICIT 상태에서 초기 재전송 시간 제한은 RFC 3315에 설명된 DHCPv6 프로토콜에서 1초로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-126">The initial retransmission timeout in the SOLICIT state is defined by to the DHCPv6 protocol described in RFC 3315 to be 1 second.</span></span> <span data-ttu-id="c22bc-127">이후 재전송 간격은 DHCPv6 클라이언트가 유효한 서버 응답을 받지 못할 경우 최대 120초까지 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-127">Subsequent retransmission intervals, if the DHCPv6 Client fails to receive a valid Server response, are doubled up to a maximum of 120 seconds.</span></span>

<span data-ttu-id="c22bc-128">서버를 선택하면 클라이언트가 ADVERTISE 메시지에서 데이터를 추출한 후 다시 서버로 REQUEST 메시지를 보내 할당된 주소 정보와 임대 시간을 수락합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-128">Having chosen the Server, the Client extracts data from the ADVERTISE message and sends a REQUEST message back to the Server to accept the assigned address information and lease times.</span></span> <span data-ttu-id="c22bc-129">서버에서 REPLY 메시지를 사용하여 클라이언트에 할당된 클라이언트 IPv6 주소가 서버에 등록되었음을 확인하는 응답을 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-129">The Server responds with a REPLY message to confirm the Client IPv6 address(es) are registered with the Server as assigned to the Client.</span></span>

<span data-ttu-id="c22bc-130">DHCPv6 클라이언트가 할당된 IPv6 주소를 IP 인스턴스(예: NetX Duo)에 등록합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-130">The DHCPv6 Client registers the assigned IPv6 address(es) with the IP instance (e.g NetX Duo).</span></span> <span data-ttu-id="c22bc-131">DAD(중복 주소 검색) 프로토콜(기본적으로 사용하도록 설정됨)이 구성된 경우, NetX Duo는 할당된 주소가 네트워크에서 고유한지 확인하기 위해 Neighbor Solicit 메시지를 자동으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-131">If configured for the Duplicate Address Detection (DAD) protocol (enabled by default), NetX Duo will automatically send Neighbor Solicit messages to verify the assigned address(es) are unique on the network.</span></span> <span data-ttu-id="c22bc-132">고유한 것으로 확인되면 할당된 각 주소가 TENTATIVE에서 VALID로 승격되었을 때 DHCPv6 클라이언트에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-132">If so, it notifies the DHCPv6 Client when the each assigned address has been promoted from TENTATIVE to VALID.</span></span> <span data-ttu-id="c22bc-133">DHCPv6 클라이언트는 BOUND 상태로 승격되고, 디바이스는 해당 IPv6 주소를 사용하여 메시지를 보낼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-133">The DHCPv6 Client is promoted to the BOUND state and the device may use that IPv6 address to send and transmit messages.</span></span> <span data-ttu-id="c22bc-134">DAD 프로토콜이 실패하면 NetX Duo가 DHCPv6 클라이언트에 알리고, DHCPv6 클라이언트는 서버에 다시 할당된 IPv6 주소에 대한 DECLINE 메시지를 보내고, DHCPv6 클라이언트 상태를 다시 INIT 상태로 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-134">If the DAD protocol fails, NetX Duo notifies the DHCPv6 Client and the DHCPv6 Client sends a DECLINE message for the IPv6 address(es) assigned back to the Server and resets the DHCPv6 Client state to the INIT state.</span></span>

### <a name="notification-of-successful-address-assignment-and-validation"></a><span data-ttu-id="c22bc-135">성공한 주소 할당 및 유효성 검사 알림</span><span class="sxs-lookup"><span data-stu-id="c22bc-135">Notification of Successful Address Assignment and Validation</span></span>

<span data-ttu-id="c22bc-136">DHCPv6 클라이언트의 *nx_dhcpv6_client_create* 서비스에 상태 변경 콜백이 구성된 경우, 애플리케이션은 상태 변경을 통해 DHCPv6 클라이언트 주소 요청 결과를 확인할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-136">The application can determine the result of the DHCPv6 Client address solicitation from the state changes if the DHCPv6 Client is configured with the state change callback in the *nx_dhcpv6_client_create* service.</span></span> <span data-ttu-id="c22bc-137">서버에서 응답을 받지 못한 경우에는 SOLICIT에서 INIT로의 상태 변경이 관찰됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-137">If it receives no response from the Server the state change observed is from SOLICIT to INIT.</span></span> <span data-ttu-id="c22bc-138">서버에서 응답을 받았지만 서버가 주소를 할당할 수 없는 경우, 이 애플리케이션은 DHCPv6 클라이언트 서버 오류 콜백의 알림을 받습니다(*nx_dhcpv6_client_create* 에 구성된 경우).</span><span class="sxs-lookup"><span data-stu-id="c22bc-138">If it received a response from the Server but the Server is unable to assign the address, the application will be notified by the DHCPv6 Client server error callback if configured with one (also in *nx_dhcpv6_client_create).*</span></span> <span data-ttu-id="c22bc-139">클라이언트가 BOUND 상태를 달성했지만 DAD 확인에 실패한 경우 상태가 BOUND에서 INIT로 변경되는 것을 볼 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-139">If the Client achieved the BOUND state but then failed the DAD check, it will see a state change from BOUND to INIT.</span></span> <span data-ttu-id="c22bc-140">DAD가 설정된 애플리케이션은 요청 프로세스를 시작한 후에 DAD 확인 시간 동안 대기해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-140">Note that an application that is enabled for DAD must allow time for the DAD check after starting the request process.</span></span> <span data-ttu-id="c22bc-141">일반적으로 이는 약 400~500틱(대부분의 경우 4-5초)입니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-141">Typically this is about 400-500 ticks (4-5 seconds in most cases).</span></span>

### <a name="relinquishing-an-ipv6-address"></a><span data-ttu-id="c22bc-142">IPv6 주소 포기</span><span class="sxs-lookup"><span data-stu-id="c22bc-142">Relinquishing an IPv6 Address</span></span>

<span data-ttu-id="c22bc-143">클라이언트는 할당된 IPv6 주소를 해제해야 하는 경우에 *nx_dhcpv6_request_release* 서비스를 호출하여 DHCPv6 서버에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-143">If and when the Client needs to release an assigned IPv6 address, it informs the DHCPv6 server by calling the *nx_dhcpv6_request_release* service:</span></span>

### <a name="uint-nx_dhcpv6_request_releasenx_dhcpv6-dhcpv6_ptr"></a><span data-ttu-id="c22bc-144">UINT nx_dhcpv6_request_release(NX_DHCPV6 \*dhcpv6_ptr)</span><span class="sxs-lookup"><span data-stu-id="c22bc-144">UINT nx_dhcpv6_request_release(NX_DHCPV6 \*dhcpv6_ptr)</span></span>

<span data-ttu-id="c22bc-145">DHCPv6 클라이언트는 주소를 할당해 준 서버로 할당된 주소가 포함된 유니캐스트 RELEASE 메시지를 보내고, 서버에서 메시지 수신을 확인하는 REPLY를 보내 줄 때까지 기다립니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-145">The DHCPv6 Client sends a unicast RELEASE message containing the assigned addresses to the Server who assigned the address and waits for a REPLY confirming the Server received the message.</span></span>

<span data-ttu-id="c22bc-146">참고: 전원이 꺼지는 중이지만 다시 부팅된 후 할당된 IPv6 주소를 계속 사용할 예정인 클라이언트에는 다른 프로세스가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-146">Note: There is a different process for the Client that is powering down but plans to continue using the assigned IPv6 addresses on reboot.</span></span> <span data-ttu-id="c22bc-147">전원을 켤 때 새 주소를 요청하지 않을 예정인 경우에는 전원이 꺼지는 동안 RELEASE 메시지를 보내지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-147">It does not send the RELEASE message on powering down unless it plans to request a new address on power up.</span></span> <span data-ttu-id="c22bc-148">이러한 상황에 대한 설명은 "비휘발성 메모리 요구 사항"을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="c22bc-148">See “Non Volatile Memory Requirements” for explanation of this situation.</span></span>

### <a name="dhcpv6-lease-timeouts"></a><span data-ttu-id="c22bc-149">DHCPv6 임대 시간 제한</span><span class="sxs-lookup"><span data-stu-id="c22bc-149">DHCPv6 Lease Timeouts</span></span>

<span data-ttu-id="c22bc-150">서버에서 할당하는 IPv6 임대에는 각 ID 연결, 즉 비임시(IANA) 블록에 T1과 T2라는 두 개의 시간 제한 매개 변수가 포함되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-150">The IPv6 lease assigned by the Server contains two timeout parameters, T1 and T2 in each Identity Association – Non Temporary Addresses (IANA) block.</span></span> <span data-ttu-id="c22bc-151">IANA는 이 사용자 가이드의 다른 부분에서 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-151">An IANA is described in elsewhere in this User Guide.</span></span> <span data-ttu-id="c22bc-152">DHCPv6 클라이언트가 할당된 IPv6 주소에 바인딩된 시점부터 경과한 시간이 T1과 같으면 DHCPv6 클라이언트는 RENEW 메시지를 전송하여 IPv6 주소 갱신을 자동으로 시작합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-152">If the elapsed time from when the DHCPv6 Client was bound to the assigned IPv6 address equals T1, the DHCPv6 Client automatically starts renewing the IPv6 address by sending a RENEW message.</span></span> <span data-ttu-id="c22bc-153">경과된 시간이 T2와 같으면 DHCPv6 클라이언트는 RENEW 요청에 대한 응답을 받지 못한 경우 REBIND 메시지를 자동으로 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-153">If the elapsed time equals T2, DHCPv6 Client automatically sends a REBIND message if it received no responses to its RENEW requests.</span></span>

<span data-ttu-id="c22bc-154">다른 두 IPv6 임대 매개 변수(기본 및 유효 수명)는 IANA 블록에 포함된 각 IA(ID 연결) 블록을 사용하여 할당됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-154">Two other IPv6 lease parameters, preferred and valid lifetime, are assigned with each Identity Association (IA) block contained in the IANA block.</span></span> <span data-ttu-id="c22bc-155">기본 및 유효 수명은 할당된 IPv6 주소가 각각 사용되지 않거나 유효하지 않게 되는 시점을 말합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-155">The preferred and valid lifetimes are when the assigned IPv6 address is deprecated or invalid, respectively.</span></span> <span data-ttu-id="c22bc-156">T1은 기본 수명보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-156">T1 must be less than the preferred lifetime.</span></span> <span data-ttu-id="c22bc-157">T2는 유효 수명보다 작아야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-157">T2 must be less than the valid lifetime.</span></span>

### <a name="ip-thread-task-requirements"></a><span data-ttu-id="c22bc-158">IP 스레드 작업 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c22bc-158">IP Thread Task Requirements</span></span>

<span data-ttu-id="c22bc-159">NetX Duo DHCPv6 클라이언트에서 DHCPv6 클라이언트 서비스를 사용하려면 DHCPv6 클라이언트를 만들기 전에 NetX Duo IP 인스턴스를 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-159">The NetX Duo DHCPv6 Client requires creation of a NetX Duo IP instance previous to creating the DHCPv6 Client to use DHCPv6 Client services.</span></span>

<span data-ttu-id="c22bc-160">DHCPv6 클라이언트를 사용하기 전에 IP 인스턴스에서 UDP, IPv6 및 ICMPv6를 사용하도록 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-160">UDP, IPv6, and ICMPv6 must be enabled on the IP instance prior to the using DHCPv6 Client.</span></span>

  - <span data-ttu-id="c22bc-161">*nx_udp_enable*</span><span class="sxs-lookup"><span data-stu-id="c22bc-161">*nx_udp_enable*</span></span>

  - <span data-ttu-id="c22bc-162">*nxd_ipv6_enable*</span><span class="sxs-lookup"><span data-stu-id="c22bc-162">*nxd_ipv6_enable*</span></span>

  - <span data-ttu-id="c22bc-163">*nxd_icmp_enable*</span><span class="sxs-lookup"><span data-stu-id="c22bc-163">*nxd_icmp_enable*</span></span>

### <a name="packet-pool-requirements"></a><span data-ttu-id="c22bc-164">패킷 풀 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c22bc-164">Packet Pool Requirements</span></span>

<span data-ttu-id="c22bc-165">NetX Duo DHCPv6 클라이언트를 만들기 전에 먼저 DHCPv6 메시지를 보내는 데 사용되는 패킷 풀부터 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-165">NetX Duo DHCPv6 Client creation requires a previously created packet pool for sending DHCPv6 messages.</span></span> <span data-ttu-id="c22bc-166">패킷 페이로드 및 사용 가능한 패킷 수와 관련된 패킷 풀의 크기는 사용자가 구성할 수 있으며, 애플리케이션에서 보내는 DHCPv6 메시지 및 기타 전송의 예상 양에 따라 달라집니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-166">The size of the packet pool in terms of packet payload and number of packets available is user configurable, and depends on the anticipated volume of DHCPv6 messages and other transmissions the application will be sending.</span></span>

<span data-ttu-id="c22bc-167">일반적인 DHCPv6 메시지는 클라이언트에서 요청하는 IA 주소 및 DHCPv6 옵션 수에 따라 약 200바이트입니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-167">A typical DHCPv6 message is about 200 bytes depending on the number of IA addresses and DHCPv6 options requested by the Client.</span></span>

### <a name="network-requirements"></a><span data-ttu-id="c22bc-168">네트워크 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c22bc-168">Network Requirements</span></span>

<span data-ttu-id="c22bc-169">NetX Duo DHCPv6 클라이언트를 사용하려면 포트 546에 바인딩된 UDP 소켓을 만들어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-169">The NetX Duo DHCPv6 Client requires the creation of UDP socket bound to port 546.</span></span> <span data-ttu-id="c22bc-170">이 소켓은 DHCPv6 클라이언트 작업을 만들 때 생성됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-170">The socket is created when the DHCPv6 Client task is created.</span></span>

### <a name="non-volatile-memory-requirements"></a><span data-ttu-id="c22bc-171">비휘발성 메모리 요구 사항</span><span class="sxs-lookup"><span data-stu-id="c22bc-171">Non Volatile Memory Requirements</span></span>

<span data-ttu-id="c22bc-172">DHCPv6 클라이언트가 전원이 꺼질 때 DHCPv6 서버와 관련된 IPv6 임대를 해제하고 다시 부팅될 때 새 IPv6 주소를 요청하는 경우에는 비휘발성 메모리 스토리지가 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-172">If a DHCPv6 Client releases its IPv6 lease with the DHCPv6 Server when powering down, and requests new IPv6 address(es) on reboot, then non volatile memory storage is not required.</span></span> <span data-ttu-id="c22bc-173">클라이언트에서 할당된 임대를 계속 사용하려면 다시 부팅하는 동안 DHCPv6 클라이언트에 대한 특정 정보를 비휘발성 메모리에 저장해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-173">If a Client wishes to continue using its assigned lease, it must store certain information about the DHCPv6 Client to non volatile memory across reboots.</span></span>

<span data-ttu-id="c22bc-174">비휘발성 메모리 요구 사항과 DHCPv6 클라이언트 API는 챕터 2의 **NetX Duo DHCPv6 클라이언트 사용** 에 자세히 설명되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-174">Non volatile memory requirements and the DHCPv6 Client API are discussed further in **Using the NetX Duo DHCPv6 Client** in Chapter Two.</span></span>

### <a name="netx-duo-dhcpv6-client-limitations"></a><span data-ttu-id="c22bc-175">NetX Duo DHCPv6 클라이언트 제한 사항</span><span class="sxs-lookup"><span data-stu-id="c22bc-175">NetX Duo DHCPv6 Client Limitations</span></span>

<span data-ttu-id="c22bc-176">NetX Duo DHCPv6 클라이언트의 현재 릴리스에는 다음과 같은 제한 사항이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-176">The current release of the NetX Duo DHCPv6 Client has the following limitations:</span></span>

  - <span data-ttu-id="c22bc-177">NetX Duo DHCPv6 클라이언트는 서버에서 허용한다고 표시하더라도 유니캐스트 DHCPv6 메시지를 DHCPv6 서버로 보내기 위한 서버 유니캐스트 옵션을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-177">NetX Duo DHCPv6 Client does not support the Server Unicast option for sending unicast DHCPv6 messages to the DHCPv6 Server even if the Server indicates this is permitted.</span></span>

  - <span data-ttu-id="c22bc-178">NetX Duo DHCPv6 클라이언트는 서버에서 네트워크의 클라이언트에 대한 IPv6 주소 변경을 시작하는 다시 구성 요청을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-178">NetX Duo DHCPv6 Client does not support the Reconfigure request in which a Server initiates IPv6 address changes to the Clients on the network.</span></span>

  - <span data-ttu-id="c22bc-179">NetX Duo DHCPv6 클라이언트는 DHCPv6 고유 식별자 제어 블록에 대한 엔터프라이즈 형식을 지원하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-179">NetX Duo DHCPv6 Client does not support the Enterprise format for the DHCPv6 Unique Identifier control block.</span></span> <span data-ttu-id="c22bc-180">링크 계층 및 링크 계층+시간 형식만 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-180">It only supports Link Layer and Link Layer Plus Time format.</span></span>

  - <span data-ttu-id="c22bc-181">NetX Duo DHCPv6 클라이언트는 TA(임시 연결) 주소 요청을 지원하지 않지만 비임시(IANA) 옵션 요청은 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-181">NetX Duo DHCPv6 Client does not support Temporary Association (TA) address requests, but does support Non Temporary (IANA) option requests.</span></span>

## <a name="multihome-and-multiple-address-support"></a><span data-ttu-id="c22bc-182">멀티홈 및 다중 주소 지원</span><span class="sxs-lookup"><span data-stu-id="c22bc-182">Multihome and Multiple Address Support</span></span>

<span data-ttu-id="c22bc-183">DHCPv6 클라이언트는 인터페이스마다 다중 인터페이스와 다중 주소를 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-183">The DHCPv6 Client supports multiple interfaces and multiple addresses per interface.</span></span> <span data-ttu-id="c22bc-184">클라이언트 애플리케이션은 DHCPv6 클라이언트 서비스 *nx_dhcpv6_client_set_interface* 를 사용하여 DHCPv6 서버와 통신하는 데 사용할 네트워크 인터페이스를 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-184">The DHCPv6 Client service, *nx_dhcpv6_client_set_interface* enables the Client application to set the network interface on which the application will be communicating with the DHCPv6 Server.</span></span> <span data-ttu-id="c22bc-185">DHCPv6 클라이언트는 기본 인터페이스(인덱스 0)로 기본 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-185">The DHCPv6 Client defaults to the primary interface (index zero).</span></span>

<span data-ttu-id="c22bc-186">인터페이스별 다중 주소의 경우, DHCPv6 클라이언트는 인덱스 0부터 시작하는 주소의 내부 목록을 유지합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-186">For multiple addresses per interface, the DHCPv6 Client keeps an internal list of addresses starting at index 0.</span></span> <span data-ttu-id="c22bc-187">DHCPv6 클라이언트에 등록된 것과 동일한 주소가 인터페이스 주소에 대한 IP 테이블의 동일한 인덱스에 반드시 있어야 하는 것은 아닙니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-187">Note that the same address registered with the DHCPv6 Client may not necessarily be located at the same index in the IP table of interface addresses.</span></span>

<span data-ttu-id="c22bc-188">클라이언트 IPv6 주소 임대에 대한 정보를 검색하는 DHCPv6 클라이언트 서비스의 경우, 일부는 주소 인덱스를 지정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-188">For DHCPv6 Client services that retrieve information about the Client IPv6 address lease, some require an address index to be specified.</span></span> <span data-ttu-id="c22bc-189">기본 및 유효 수명을 가져오는 예는 다음과 같습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-189">An example for obtaining the preferred and valid lifetimes is shown below:</span></span>

```C
UINT nx_dhcpv6_get_valid_ip_address_lease_time(NX_DHCPV6 *dhcpv6_ptr, 
                                              UINT address_index, 
                                              NXD_ADDRESS *ip_address,
                                              ULONG preferred_lifetime, 
                                              ULONG *valid_lifetime)
```

<span data-ttu-id="c22bc-190">클라이언트 애플리케이션은 *nx_dhcpv6_get_valid_ip_address_count* 서비스에서 할당된 유효한 IPv6 주소 수를 검색할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-190">The Client application can also retrieve the number of valid IPv6 addresses assigned from the *nx_dhcpv6_get_valid_ip_address_count* service:</span></span>

```C
UINT nx_dhcpv6_get_valid_ip_address_count(NX_DHCPV6 *dhcpv6_ptr, 
                                          UINT *address_count)
```

<span data-ttu-id="c22bc-191">NetX Duo에서 다중 주소가 지원되기 전에 생성된 레거시 DHCPv6 클라이언트 서비스는 주소 인덱스를 허용하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-191">Legacy DHCPv6 Client services which were created before multiple addresses were supported in NetX Duo do not take an address index.</span></span> <span data-ttu-id="c22bc-192">따라서 이러한 서비스를 사용하면 클라이언트에 할당된 IA 주소 수에 관계없이 기본 글로벌 IA 주소에서 요청된 데이터를 가져옵니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-192">Therefore, with these services, the data requested is taken from the primary global IA address, regardless how many IA addresses are assigned to the Client.</span></span> <span data-ttu-id="c22bc-193">아래에 예가 나와 있습니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-193">An example is shown below:</span></span>

```C
UINT nx_dhcpv6_get_IP_address(NX_DHCPV6 *dhcpv6_ptr, 
                              NXD_ADDRESS *ip_address)
```

## <a name="netx-duo-dhcpv6-client-callback-functions"></a><span data-ttu-id="c22bc-194">NetX Duo DHCPv6 클라이언트 콜백 함수</span><span class="sxs-lookup"><span data-stu-id="c22bc-194">NetX Duo DHCPv6 Client Callback Functions</span></span>

<span data-ttu-id="c22bc-195">*nx_dhcpv6_state_change_callback*</span><span class="sxs-lookup"><span data-stu-id="c22bc-195">*nx_dhcpv6_state_change_callback*</span></span>

<span data-ttu-id="c22bc-196">DHCPv6 클라이언트가 DHCPv6 요청 처리의 결과로 새 DHCPv6 상태로 변경될 때 이 콜백 함수를 사용하여 애플리케이션에 알립니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-196">When the DHCPv6 Client changes to a new DHCPv6 state as a result of processing a DHCPv6 request, it notifies the application with this callback function.</span></span>

<span data-ttu-id="c22bc-197">*nx_dhcpv6_server_error_handler*</span><span class="sxs-lookup"><span data-stu-id="c22bc-197">*nx_dhcpv6_server_error_handler*</span></span>

<span data-ttu-id="c22bc-198">DHCPv6 클라이언트가 0(성공)이 아닌 상태의 *Status* 옵션을 포함하는 서버 회신을 받으면 이 콜백을 사용하여 애플리케이션에 알립니다(서버 오류 상태 코드 포함).</span><span class="sxs-lookup"><span data-stu-id="c22bc-198">When the DHCPv6 Client receives a Server reply containing a *Status* option with a non-zero (non successful) status, it notifies the application with this callback which includes the Server error status code.</span></span>

<span data-ttu-id="c22bc-199">참고: 이러한 콜백 함수는 DHCPv6 클라이언트 스레드 작업에서 호출되므로, 클라이언트 애플리케이션은 *nx_dhcpv6_start, nx_dhcpv6_stop* 과 같은 DHCPv6 클라이언트의 뮤텍스 제어를 요구하는 NetX Duo DHCPv6 클라이언트 서비스와 콜백에서 직접 메시지를 전송하는 API(예: *nx_dhcpv6_request_release*)를 호출하면 안 됩니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-199">Note: Since these callback functions are called from the DHCPv6 Client thread task, the Client application must NOT call any NetX Duo DHCPv6 Client services that require mutex control of the DHCPv6 Client such as *nx_dhcpv6_start, nx_dhcpv6_stop,* and any of the API that send messages directly from the callback e.g. *nx_dhcpv6_request_release*.</span></span>

## <a name="dhcpv6-rfcs"></a><span data-ttu-id="c22bc-200">DHCPv6 RFC</span><span class="sxs-lookup"><span data-stu-id="c22bc-200">DHCPv6 RFCs</span></span>

<span data-ttu-id="c22bc-201">NetX Duo DHCP는 RFC3315, RFC3646 및 관련 RFC를 준수합니다.</span><span class="sxs-lookup"><span data-stu-id="c22bc-201">NetX Duo DHCP is compliant with RFC3315, RFC3646, and related RFCs.</span></span>