---
title: 2장 - Azure RTOS NetX Duo DNS 클라이언트의 설치 및 사용
description: 이 장에서는 Azure RTOS NetX Duo DNS 클라이언트의 설치, 설정, 사용과 관련된 다양한 문제에 관해 설명합니다.
author: philmea
ms.author: philmea
ms.date: 06/04/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: d18e6444f13f069347719901b24234aebe04cdc5cd6230212e0781a50ed245d1
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116792012"
---
# <a name="chapter-2---installation-and-use-of-azure-rtos-netx-duo-dns-client"></a>2장 - Azure RTOS NetX Duo DNS 클라이언트의 설치 및 사용

이 장에서는 Azure RTOS NetX Duo DNS 클라이언트의 설치, 설정, 사용과 관련된 다양한 문제에 관해 설명합니다.

## <a name="product-distribution"></a>제품 배포

NetX Duo DNS 클라이언트는 [https://github.com/azure-rtos/netxduo](https://github.com/azure-rtos/netxduo)에서 얻을 수 있습니다. 이 패키지에는 다음과 같이 소스 파일 두 개와 이 문서가 포함된 PDF 파일이 들어 있습니다.

- **nxd_dns.h** NetX Duo DNS 클라이언트의 헤더 파일
- **nxd_dns.c** NetX Duo DNS 클라이언트의 C 소스 파일
- **nxd_dns.pdf** NetX Duo DNS 클라이언트의 PDF 설명서

## <a name="dns-client-installation"></a>DNS 클라이언트 설치

NetX Duo DNS 클라이언트를 사용하려면 소스 코드 파일 *nx_dns.c* 와 *nx_dns.h* 를 NetX Duo의 설치 디렉터리에 복사합니다. 예를 들어 NetX Duo가 “ *\threadx\arm7\green*” 디렉터리에 설치된 경우 *nxd_dns.h* 와 *nxd_dns.c* 파일을 이 디렉터리에 복사해야 합니다.

## <a name="using-the-dns-client"></a>DNS 클라이언트 사용

NetX Duo DNS 클라이언트는 사용하기 쉽습니다. 기본적으로 ThreadX와 NetX Duo를 각각 사용하기 위해 애플리케이션 코드는 *nxd_dns.h* 와 *tx_api.h* 를 포함한 후, *nxd_dns.h* 를 포함해야 합니다. *nxd_dns.h* 가 포함되면 애플리케이션 코드가 이 가이드의 뒷부분에 나오는 DNS 함수를 호출할 수 있습니다. 또한 애플리케이션에서 빌드 프로세스에 *nxd_dns.c* 도 추가해야 합니다. 이 파일은 다른 애플리케이션 파일과 동일한 방식으로 컴파일해야 하며, 해당 개체 형식은 애플리케이션의 파일과 함께 연결해야 합니다. 이렇게 간단하게 NetX Duo DNS를 사용할 수 있습니다.

> [!NOTE]
> DNS는 NetX Duo UDP 서비스를 사용하므로 DNS를 사용하기 전에 *nx_udp_enable* 호출을 통해 UDP를 사용하도록 설정해야 합니다.

## <a name="small-example-system-for-netx-duo-dns-client"></a>NetX Duo DNS 클라이언트에 관한 간단한 예제 시스템 

NetX Duo DNS 클라이언트는 기존 NetX DNS 애플리케이션과 호환됩니다. 레거시 서비스와 동일한 NetX Duo 서비스 목록은 다음과 같습니다.

| NetX DNS API 서비스(IPv4만 해당) | NetX Duo DNS API 서비스(IPv4와 IPv6 지원됨) |
|----------------------------------|----------------------------------------------------|
| nx_dns_host_by_name_get          | nxd_dns_host_by_name_get                           |
| nx_dns_host_by_address_get       | nxd_dns_host_by_address_get                        |
| nx_dns_server_get                | nxd_dns_server_get                                 |
| nx_dns_server_add                | nxd_dns_server_add                                 |
| nx_dns_server_remove             | nxd_dns_server_remove                              |

자세한 내용은 3장에서 NetX Duo DNS 클라이언트 API 서비스에 대한 설명을 참조하세요.

이 섹션에서 제공하는 예제 DNS 애플리케이션 프로그램에서 *nxd_dns.h* 는 줄 6에 포함되어 있습니다. DNS 클라이언트 애플리케이션이 DNS 클라이언트용 패킷 풀을 만들 수 있게 하는 NX_DNS_CLIENT_USER_CREATE_PACKET_POOL은 줄 24~26에서 선언합니다. 이 패킷 풀은 DNS 메시지를 보내기 위한 패킷을 할당하는 데 사용됩니다. NX_DNS_CLIENT_USER_CREATE_PACKET_POOL을 정의한 경우 패킷 풀은 줄 78~98에서 생성됩니다. 이 옵션을 사용하지 않는 경우 DNS 클라이언트는 *nxd_dns.h* 의 구성 매개 변수가 설정한 패킷 페이로드와 풀 크기에 따라 자체 패킷 풀을 만듭니다. 이에 대해서는 이 장의 다른 부분에서 설명합니다.

내부 NetX Duo 작업에 사용되는 클라이언트 IP 인스턴스를 위해 줄 100~112에서 다른 패킷 풀을 만듭니다. 그런 다음, 줄 115에서 *nx_ip_create* 호출을 사용하여 IP 인스턴스를 만듭니다. IP 작업과 DNS 클라이언트가 동일한 패킷 풀을 공유할 수는 있으나, 일반적으로 DNS 클라이언트는 IP 작업에서 보낸 제어 패킷보다 큰 메시지를 전송하므로 별도의 패킷 풀을 사용하면 메모리를 더 효율적으로 사용할 수 있습니다.

ARP와 UDP(IPv4 네트워크에서 사용)는 각각 줄 129와 141에서 사용하도록 설정됩니다.

>[!NOTE]
> 이 데모에서는 줄 44에서 선언되어 *nx_ip_create* 호출에 사용되는 ‘ram’ 드라이버를 사용합니다. 이 ram 드라이버는 NetX Duo 소스 코드와 함께 배포됩니다. 실제로 DNS 클라이언트를 실행하려면 애플리케이션이 DNS 서버로부터의 패킷 송수신을 위해 실제 네트워크 드라이버를 제공해야 합니다.

클라이언트 스레드 입력 함수 *thread_client_entry* 는 *tx_application_define* 함수 아래에 정의되어 있습니다. 처음에는 네트워크 드라이버에서 IP 작업 스레드를 초기화할 수 있도록 시스템에 제어 권한을 내어줍니다.

그런 다음, 줄 257에서 DNS 클라이언트를 만들고, 줄 267~278에서 DNS 캐시를 초기화하며, 줄 281~295에서 이전에 만든 패킷 풀을 DNS 클라이언트 인스턴스로 설정합니다. 그런 다음 줄 297~341에서 DNS 서버를 추가합니다.

예제 프로그램의 나머지 부분에서는 DNS 클라이언트 서비스를 사용하여 DNS 쿼리를 만듭니다. 호스트 IP 주소 조회는 줄 193과 207에서 수행됩니다. 두 서비스, *nxd_dns_host_by_name_get* 과 *nx_dns_host_by_name_get* 의 차이는 전자는 주소 데이터를 NXD_ADDRESS 데이터 형식으로 저장하는 반면에 후자는 해당 데이터를 ULONG 데이터 형식으로 저장한다는 점입니다. 또한 후자는 IPv4 네트워크로 제한되지만 전자는 IPv6 또는 IPv4 네트워크에서 사용할 수 있습니다. 이는 IP 인스턴스가 IPv6에 사용하도록 설정된 경우에만 가능합니다. IPv6 네트워킹에 IP 인스턴스를 사용하도록 설정하는 방법에 대한 자세한 내용은 NetX Duo 사용자 가이드를 참조하세요.

호스트 IP 주소 조회를 위한 다른 서비스인 *nx_dns_ipv4_address_by_name_get* 은 줄 464에 나와 있다. 이 서비스는 DNS 서버 회신에서 수신한 첫 번째 주소뿐만 아니라 도메인 이름에 대해 검색된 IPv4 주소를 모두(또는 제공된 버퍼에 맞게 최대한 많이) 반환한다는 점에서 *nx_dns_host_by_name_get* 과 다릅니다.

마찬가지로 줄 380에서 호출하는 *nxd_dns_ipv6_address_by_name_get* 서비스는 첫 번째 주소뿐만 아니라 DNS 클라이언트에서 검색한 모든 IPv6 주소를 반환합니다.

역방향 조회(IP 주소의 호스트 이름)는 606번 라인에서 수행되며(*nx_dns_host_by_address_get*) 564번과 588번에서 다시 수행됩니다(*nxd_dns_host_by_address_get*). *nx_dns_host_by_address_get* 은 IPv4 네트워크에서만 작동하고 *nxd_dns_host_by_address_get* 은 IPv4 또는 IPv6 네트워크에서 작동합니다(예: IP 인스턴스가 IPv4와 IPv6 네트워크에 대해 사용하도록 설정된 경우).

DNS 조회를 위한 다른 두 가지 추가 서비스인 CNAME과 TXT는 입력 도메인 이름의 CNAME과 도메인 이름 데이터를 검색하기 위해 각각 줄 627과 649에서 사용됩니다. NetX Duo DNS 클라이언트는 다른 레코드 형식(예: MX, NS, SRV, SOA)에 대해 유사하게 작동합니다. NetX Duo DNS 클라이언트에서 사용할 수 있는 모든 레코드 형식 조회에 대한 자세한 설명은 3장을 참조하세요.

줄 846에서 DNS 클라이언트가 *Nx_dns_delete* 서비스를 사용하여 삭제되었을 때, DNS 클라이언트가 자체 패킷 풀을 만든 경우가 아니라면 DNS 클라이언트에 대한 패킷 풀이 삭제되지 않습니다. 이 경우 더 이상 사용하지 않으려는 패킷 풀을 애플리케이션이 직접 삭제해야 합니다.

```C
/* This is a small demo of DNS Client for the high-performance NetX TCP/IP stack. */

#include   "tx_api.h"
#include   "nx_api.h"
#include   "nx_udp.h"
#include   "nxd_dns.h"

#ifdef FEATURE_NX_IPV6
#include   "nx_ipv6.h"
#endif

#define     DEMO_STACK_SIZE         4096

#define     NX_PACKET_PAYLOAD       1536
#define     NX_PACKET_POOL_SIZE     30 * NX_PACKET_PAYLOAD
#define     LOCAL_CACHE_SIZE        2048

/* Define the ThreadX and NetX object control blocks... */

NX_DNS                  client_dns;
TX_THREAD               client_thread;
NX_IP                   client_ip;
NX_PACKET_POOL          main_pool;
#ifdef NX_DNS_CLIENT_USER_CREATE_PACKET_POOL
NX_PACKET_POOL          client_pool;
#endif
UCHAR                   local_cache[LOCAL_CACHE_SIZE];

UINT                    error_counter = 0;

#ifdef FEATURE_NX_IPV6
/* If IPv6 is enabled in NetX Duo, allow DNS Client to try using IPv6 */
//#define USE_IPV6
#endif

#define CLIENT_ADDRESS      IP_ADDRESS(192,168,0,11)
#define DNS_SERVER_ADDRESS  IP_ADDRESS(192,168,0,1)

/* Define thread prototypes. */

void    thread_client_entry(ULONG thread_input);

/***** Substitute your ethernet driver entry function here *********/
extern  VOID _nx_ram_network_driver(NX_IP_DRIVER *driver_req_ptr);

/* Define main entry point. */

int main()
{

    /* Enter the ThreadX kernel. */
    tx_kernel_enter();
}

/* Define what the initial system looks like. */

void    tx_application_define(void *first_unused_memory)
{

CHAR    *pointer;
UINT    status;

    /* Setup the working pointer. */
    pointer =  (CHAR *) first_unused_memory;

    /* Create the main thread. */
    tx_thread_create(&client_thread, "Client thread", thread_client_entry, 0,
            pointer, DEMO_STACK_SIZE, 4, 4, TX_NO_TIME_SLICE, TX_AUTO_START);

    pointer =  pointer + DEMO_STACK_SIZE;

    /* Initialize the NetX system. */
    nx_system_initialize();

#ifdef NX_DNS_CLIENT_USER_CREATE_PACKET_POOL

    /* Create the packet pool for the DNS Client to send packets.

        If the DNS Client is configured for letting the host application create
        the DNS packet pool, (see NX_DNS_CLIENT_USER_CREATE_PACKET_POOL option), see
       nx_dns_create() for guidelines on packet payload size and pool size.
       packet traffic for NetX processes.
    */
    status =  nx_packet_pool_create(&client_pool, "DNS Client Packet Pool", NX_DNS_PACKET_PAYLOAD, pointer, NX_DNS_PACKET_POOL_SIZE);

    pointer = pointer + NX_DNS_PACKET_POOL_SIZE;

    /* Check for pool creation error. */
    if (status)
    {

        error_counter++;
        return;
     }
#endif
     /* Create the packet pool which the IP task will use to send packets. Also available to the host
       application to send packet. */
    status =  nx_packet_pool_create(&main_pool, "Main Packet Pool", NX_PACKET_PAYLOAD, pointer, NX_PACKET_POOL_SIZE);

    pointer = pointer + NX_PACKET_POOL_SIZE;

    /* Check for pool creation error. */
    if (status)
    {

        error_counter++;
        return;
     }

    /* Create an IP instance for the DNS Client. */
    status = nx_ip_create(&client_ip, "DNS Client IP Instance", CLIENT_ADDRESS, 0xFFFFFF00UL,
                          &main_pool, _nx_ram_network_driver, pointer, 2048, 1);

    pointer =  pointer + 2048;

    /* Check for IP create errors. */
    if (status)
    {

        error_counter++;
        return;
     }

    /* Enable ARP and supply ARP cache memory for the DNS Client IP. */
    status =  nx_arp_enable(&client_ip, (void *) pointer, 1024);
    pointer = pointer + 1024;

    /* Check for ARP enable errors. */
    if (status)
    {

        error_counter++;
        return;
     }

    /* Enable UDP traffic because DNS is a UDP based protocol. */
    status =  nx_udp_enable(&client_ip);

    /* Check for UDP enable errors. */
    if (status)
    {

        error_counter++;
        return;
     }
}

#define BUFFER_SIZE     200
#define RECORD_COUNT    10

/* Define the Client thread. */

void    thread_client_entry(ULONG thread_input)
{

UCHAR           record_buffer[200];
UINT            record_count;
UINT            status;
ULONG           host_ip_address;
#ifdef FEATURE_NX_IPV6
NXD_ADDRESS     host_ipduo_address;
NXD_ADDRESS     test_ipduo_server_address;
#ifdef USE_IPV6
NXD_ADDRESS     client_ipv6_address;
NXD_ADDRESS     dns_ipv6_server_address;
UINT            iface_index, address_index;
#endif
#endif
UINT            i;
ULONG           *ipv4_address_ptr[RECORD_COUNT];
NX_DNS_IPV6_ADDRESS
                *ipv6_address_ptr[RECORD_COUNT];
#ifdef NX_DNS_ENABLE_EXTENDED_RR_TYPES
NX_DNS_NS_ENTRY
                *nx_dns_ns_entry_ptr[RECORD_COUNT];
NX_DNS_MX_ENTRY
                *nx_dns_mx_entry_ptr[RECORD_COUNT];
NX_DNS_SRV_ENTRY
                *nx_dns_srv_entry_ptr[RECORD_COUNT];
NX_DNS_SOA_ENTRY
                *nx_dns_soa_entry_ptr;
ULONG           host_address;
USHORT          host_port;
#endif

    /* Give NetX IP task a chance to get initialized . */
    tx_thread_sleep(100);

#ifdef FEATURE_NX_IPV6
#ifdef USE_IPV6

    /* Make the DNS Client IPv6 enabled. */
    status = nxd_ipv6_enable(&client_ip);

    /* Check for enable errors. */
    if (status)
    {

        error_counter++;
        return;
     }
    status = nxd_icmp_enable(&client_ip);

    /* Check for enable errors. */
    if (status)
    {

        error_counter++;
        return;
    }

    client_ipv6_address.nxd_ip_address.v6[3] = 0x101;
    client_ipv6_address.nxd_ip_address.v6[2] = 0x0;
    client_ipv6_address.nxd_ip_address.v6[1] = 0x0000f101;
    client_ipv6_address.nxd_ip_address.v6[0] = 0x20010db8;
    client_ipv6_address.nxd_ip_version = NX_IP_VERSION_V6;


     /* Set the link local address with the host MAC address. */
    iface_index = 0;

    /* This assumes we are using the primary network interface (index 0). */
    status = nxd_ipv6_address_set(&client_ip, iface_index, NX_NULL, 10, &address_index);

    /* Check for link local address set error. */
    if (status)
    {

        error_counter++;
        return;
     }

    /* Set the host global IP address. We are assuming a 64
       bit prefix here but this can be any value (< 128). */
    status = nxd_ipv6_address_set(&client_ip, iface_index, &client_ipv6_address, 64, &address_index);

    /* Check for global address set error. */
    if (status)
    {

        error_counter++;
        return;
     }

    /* Wait while NetX Duo validates the link local and global address. */
    tx_thread_sleep(500);
#endif
#endif

    /* Create a DNS instance for the Client. Note this function will create
       the DNS Client packet pool for creating DNS message packets intended
       for querying its DNS server. */
    status =  nx_dns_create(&client_dns, &client_ip, (UCHAR *)"DNS Client");

    /* Check for DNS create error. */
    if (status)
    {

        error_counter++;
        return;
     }

#ifdef NX_DNS_CACHE_ENABLE
    /* Initialize the cache. */
    status = nx_dns_cache_initialize(&client_dns, local_cache, LOCAL_CACHE_SIZE);

    /* Check for DNS cache error. */
    if (status)
    {

        error_counter++;
        return;
     }
#endif

    /* Is the DNS client configured for the host application to create the pecket pool? */
#ifdef NX_DNS_CLIENT_USER_CREATE_PACKET_POOL

    /* Yes, use the packet pool created above which has appropriate payload size
       for DNS messages. */
     status = nx_dns_packet_pool_set(&client_dns, &client_pool);

     /* Check for set DNS packet pool error. */
     if (status)
     {

         error_counter++;
         return;
      }

#endif /* NX_DNS_CLIENT_USER_CREATE_PACKET_POOL */

#ifdef FEATURE_NX_IPV6
#ifdef USE_IPV6

    /* Add an IPv6 DNS server to the DNS client. */
    dns_ipv6_server_address.nxd_ip_address.v6[3] = 0x106;
    dns_ipv6_server_address.nxd_ip_address.v6[2] = 0x0;
    dns_ipv6_server_address.nxd_ip_address.v6[1] = 0x0000f101;
    dns_ipv6_server_address.nxd_ip_address.v6[0] = 0x20010db8;
    dns_ipv6_server_address.nxd_ip_version = NX_IP_VERSION_V6;

    status = nxd_dns_server_add(&client_dns, &dns_ipv6_server_address);

    /* Check for DNS add server error. */
    if (status)
    {

        error_counter++;
        return;
     }
#else

    /* Add an IPv4 server address to the Client list. */
    status = nx_dns_server_add(&client_dns, DNS_SERVER_ADDRESS);

    /* Check for DNS add server error. */
    if (status)
    {

        error_counter++;
        return;
     }
#endif
#else

    /* Add an IPv4 server address to the Client list. */
    status = nx_dns_server_add(&client_dns, DNS_SERVER_ADDRESS);

    /* Check for DNS add server error. */
    if (status)
    {

        error_counter++;
        return;
     }
#endif


/********************************************************************************/
/*                                  Type AAAA                                   */
/*     Send AAAA type DNS Query to its DNS server and get the IPv6 address. */
/********************************************************************************/
#ifdef FEATURE_NX_IPV6

    /* Send a DNS Client name query. Indicate the Client expects an IPv6 address (containing an AAAA record). The DNS
       Client will send AAAA type query to its DNS server. */
    status = nxd_dns_host_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &host_ipduo_address, 400, NX_IP_VERSION_V6);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test AAAA: \n");

        printf("IP address: %x:%x:%x:%x:%x:%x:%x:%x\n",
            (UINT)host_ipduo_address.nxd_ip_address.v6[0]  >>16 & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[0]  & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[1]  >>16 & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[1]  & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[2]  >>16 & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[2]  & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[3]  >>16 & 0xFFFF,
            (UINT)host_ipduo_address.nxd_ip_address.v6[3]  & 0xFFFF);
    }

#endif

    /* Look up IPv6 addresses(AAAA TYPE) to record multiple IPv6 addresses in record_buffer and return the IPv6 address count. */
    status = nxd_dns_ipv6_address_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, &record_count, 400);

    /* Check for DNS add server error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test AAAA: ");
        printf("record_count = %d \n", record_count);
    }

    /* Get the IPv6 addresses of host. */
    for(i =0; i< record_count; i++)
    {
        ipv6_address_ptr[i] = (NX_DNS_IPV6_ADDRESS *)(record_buffer + i * sizeof(NX_DNS_IPV6_ADDRESS));

        printf("record %d: IP address: %x:%x:%x:%x:%x:%x:%x:%x\n", i,
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[0]  >>16 & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[0]  & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[1]  >>16 & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[1]  & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[2]  >>16 & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[2]  & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[3]  >>16 & 0xFFFF),
                (UINT)(ipv6_address_ptr[i] -> ipv6_address[3]  & 0xFFFF));
    }


/********************************************************************************/
/*                                  Type A                                      */
/*       Send A type DNS Query to its DNS server and get the IPv4 address. */
/********************************************************************************/
#ifdef FEATURE_NX_IPV6
    /* Send a DNS Client name query. Indicate the Client expects an IPv4 address (containing an A record). If the DNS client
       is using an IPv6 DNS server it will send this query over IPv6; otherwise it will be sent over IPv4. */
    status = nxd_dns_host_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &host_ipduo_address, 400, NX_IP_VERSION_V4);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }
    else
    {

        printf("------------------------------------------------------\n");
        printf("Test A: \n");
        printf("IP address: %lu.%lu.%lu.%lu\n",
              host_ipduo_address.nxd_ip_address.v4 >> 24,
              host_ipduo_address.nxd_ip_address.v4 >> 16 & 0xFF,
              host_ipduo_address.nxd_ip_address.v4 >> 8 & 0xFF,
              host_ipduo_address.nxd_ip_address.v4 & 0xFF);
    }

#endif

    /* Look up an IPv4 address over IPv4. */
    status = nx_dns_host_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &host_ip_address, 400);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test A: \n");
        printf("IP address: %lu.%lu.%lu.%lu\n",
        host_ip_address >> 24,
        host_ip_address >> 16 & 0xFF,
        host_ip_address >> 8 & 0xFF,
        host_ip_address & 0xFF);
    }


    /* Look up IPv4 addresses to record multiple IPv4 addresses in record_buffer and return the IPv4 address count. */
    status = nx_dns_ipv4_address_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, &record_count, 400);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test A: ");
        printf("record_count = %d \n", record_count);
    }

    /* Get the IPv4 addresses of host. */
    for(i =0; i< record_count; i++)
    {
        ipv4_address_ptr[i] = (ULONG *)(record_buffer + i * sizeof(ULONG));
        printf("record %d: IP address: %lu.%lu.%lu.%lu\n", i,
                *ipv4_address_ptr[i] >> 24,
                *ipv4_address_ptr[i] >> 16 & 0xFF,
                *ipv4_address_ptr[i] >> 8 & 0xFF,
                *ipv4_address_ptr[i] & 0xFF);
    }


/********************************************************************************/
/*                                  Type A + CNAME response                     */
/*       Send A type DNS Query to its DNS server and get the IPv4 address. */
/********************************************************************************/
    /* Look up an IPv4 address over IPv4. */
    status = nx_dns_host_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &host_ip_address, 400);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test A + CNAME response: \n");
        printf("IP address: %lu.%lu.%lu.%lu\n",
        host_ip_address >> 24,
        host_ip_address >> 16 & 0xFF,
        host_ip_address >> 8 & 0xFF,
        host_ip_address & 0xFF);
    }


    /* Look up IPv4 addresses to record multiple IPv4 addresses in record_buffer and return the IPv4 address count. */
    status = nx_dns_ipv4_address_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, &record_count, 400);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test Test A + CNAME response: ");
        printf("record_count = %d \n", record_count);
    }

    /* Get the IPv4 addresses of host. */
    for(i =0; i< record_count; i++)
    {
        ipv4_address_ptr[i] = (ULONG *)(record_buffer + i * sizeof(ULONG));
        printf("record %d: IP address: %lu.%lu.%lu.%lu\n", i,
                *ipv4_address_ptr[i] >> 24,
                *ipv4_address_ptr[i] >> 16 & 0xFF,
                *ipv4_address_ptr[i] >> 8 & 0xFF,
                *ipv4_address_ptr[i] & 0xFF);
    }


/********************************************************************************/
/*                                  Type PTR                                    */
/*       Send PTR type DNS Query to its DNS server and get the host name. */
/********************************************************************************/

#ifdef FEATURE_NX_IPV6

    /* Look up a host name from an IPv6 address (reverse lookup). */

    /* Create an IPv6 address for a reverse lookup. */
    test_ipduo_server_address.nxd_ip_version = NX_IP_VERSION_V6;
    test_ipduo_server_address.nxd_ip_address.v6[0] = 0x24046800;
    test_ipduo_server_address.nxd_ip_address.v6[1] = 0x40050c00;
    test_ipduo_server_address.nxd_ip_address.v6[2] = 0x00000000;
    test_ipduo_server_address.nxd_ip_address.v6[3] = 0x00000065;

    /* This will be sent over IPv6 to the DNS server who should return a PTR record if it can find the information. */
    status = nxd_dns_host_by_address_get(&client_dns, &test_ipduo_server_address, &record_buffer[0], BUFFER_SIZE, 450);

    /* Check for DNS query error. */
    if (status != NX_SUCCESS)
    {
        error_counter++;
    }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test PTR: %s\n", record_buffer);
    }
#endif

#ifdef FEATURE_NX_IPV6

    /* Create an IPv4 address for the reverse lookup. If the DNS client is IPv6 enabled, it will send this over
       IPv6 to the DNS server; otherwise it will send it over IPv4. In either case the respective server will
       return a PTR record if it has the information. */
    test_ipduo_server_address.nxd_ip_version = NX_IP_VERSION_V4;
    test_ipduo_server_address.nxd_ip_address.v4 = IP_ADDRESS(74, 125, 71, 106);

    status = nxd_dns_host_by_address_get(&client_dns, &test_ipduo_server_address, &record_buffer[0], BUFFER_SIZE, 450);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test PTR: %s\n", record_buffer);
    }
#endif

     /* Look up host name over IPv4. */
     host_ip_address = IP_ADDRESS(74, 125, 71, 106);
     status = nx_dns_host_by_address_get(&client_dns, host_ip_address, &record_buffer[0], BUFFER_SIZE, 450);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {
        printf("------------------------------------------------------\n");
        printf("Test PTR: %s\n", record_buffer);
    }

#ifdef NX_DNS_ENABLE_EXTENDED_RR_TYPES
/********************************************************************************/
/*                                  Type CNAME                                  */
/*   Send CNAME type DNS Query to its DNS server and get the canonical name . */
/********************************************************************************/

     /* Send CNAME type to record the canonical name of host in record_buffer. */
     status = nx_dns_cname_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, 400);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test CNAME: %s\n", record_buffer);
    }


/********************************************************************************/
/*                                  Type TXT                                    */
/*      Send TXT type DNS Query to its DNS server and get descriptive text. */
/********************************************************************************/

     /* Send TXT type to record the descriptive test of host in record_buffer. */
     status = nx_dns_host_text_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, 400);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test TXT: %s\n", record_buffer);
    }


/********************************************************************************/
/*                                  Type NS                                     */
/*   Send NS type DNS Query to its DNS server and get the domain name server. */
/********************************************************************************/

     /* Send NS type to record multiple name servers in record_buffer and return the name server count.
        If the DNS response includes the IPv4 addresses of name server, record it similarly in record_buffer. */
     status = nx_dns_domain_name_server_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, &record_count, 400);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test NS: ");
        printf("record_count = %d \n", record_count);
    }

    /* Get the name server. */
    for(i =0; i< record_count; i++)
    {
        nx_dns_ns_entry_ptr[i] = (NX_DNS_NS_ENTRY *)(record_buffer + i * sizeof(NX_DNS_NS_ENTRY));

        printf("record %d: IP address: %d.%d.%d.%d\n", i,
                nx_dns_ns_entry_ptr[i] -> nx_dns_ns_ipv4_address  >> 24,
                nx_dns_ns_entry_ptr[i] -> nx_dns_ns_ipv4_address >> 16 & 0xFF,
                nx_dns_ns_entry_ptr[i] -> nx_dns_ns_ipv4_address >> 8 & 0xFF,
                nx_dns_ns_entry_ptr[i] -> nx_dns_ns_ipv4_address & 0xFF);
        if(nx_dns_ns_entry_ptr[i] -> nx_dns_ns_hostname_ptr)
            printf("hostname = %s\n", nx_dns_ns_entry_ptr[i] -> nx_dns_ns_hostname_ptr);
        else
            printf("hostname is not set\n");
    }

/********************************************************************************/
/*                                  Type MX                                     */
/*   Send MX type DNS Query to its DNS server and get the domain mail exchange. */
/********************************************************************************/

     /* Send MX DNS query type to record multiple mail exchanges in record_buffer and return the mail exchange count.
        If the DNS response includes the IPv4 addresses of mail exchange, record it similarly in record_buffer. */
     status = nx_dns_domain_mail_exchange_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, &record_count, 400);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test MX: ");
        printf("record_count = %d \n", record_count);
    }

    /* Get the mail exchange. */
    for(i =0; i< record_count; i++)
    {
        nx_dns_mx_entry_ptr[i] = (NX_DNS_MX_ENTRY *)(record_buffer + i * sizeof(NX_DNS_MX_ENTRY));

        printf("record %d: IP address: %d.%d.%d.%d\n", i,
                nx_dns_mx_entry_ptr[i] -> nx_dns_mx_ipv4_address >> 24,
                nx_dns_mx_entry_ptr[i] -> nx_dns_mx_ipv4_address >> 16 & 0xFF,
                nx_dns_mx_entry_ptr[i] -> nx_dns_mx_ipv4_address >> 8 & 0xFF,
                nx_dns_mx_entry_ptr[i] -> nx_dns_mx_ipv4_address & 0xFF);
        printf("preference = %d \n ", nx_dns_mx_entry_ptr[i] -> nx_dns_mx_preference);
        if(nx_dns_mx_entry_ptr[i] -> nx_dns_mx_hostname_ptr)
            printf("hostname = %s\n", nx_dns_mx_entry_ptr[i] -> nx_dns_mx_hostname_ptr);
        else
            printf("hostname is not set\n");
    }

/********************************************************************************/
/*                                  Type SRV                                    */
/*  Send SRV type DNS Query to its DNS server and get the location of services. */
/********************************************************************************/

     /* Send SRV DNS query type to record the location of services in record_buffer and return count.
        If the DNS response includes the IPv4 addresses of service name, record it similarly in record_buffer. */
     status = nx_dns_domain_service_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, &record_count, 400);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test SRV: ");
        printf("record_count = %d \n", record_count);
    }

    /* Get the location of services. */
    for(i =0; i< record_count; i++)
    {
        nx_dns_srv_entry_ptr[i] = (NX_DNS_SRV_ENTRY *)(record_buffer + i * sizeof(NX_DNS_SRV_ENTRY));

        printf("record %d: IP address: %d.%d.%d.%d\n", i,
                nx_dns_srv_entry_ptr[i] -> nx_dns_srv_ipv4_address >> 24,
                nx_dns_srv_entry_ptr[i] -> nx_dns_srv_ipv4_address >> 16 & 0xFF,
                nx_dns_srv_entry_ptr[i] -> nx_dns_srv_ipv4_address >> 8 & 0xFF,
                nx_dns_srv_entry_ptr[i] -> nx_dns_srv_ipv4_address & 0xFF);
        printf("port number = %d\n", nx_dns_srv_entry_ptr[i] -> nx_dns_srv_port_number );
        printf("priority = %d\n", nx_dns_srv_entry_ptr[i] -> nx_dns_srv_priority );
        printf("weight = %d\n", nx_dns_srv_entry_ptr[i] -> nx_dns_srv_weight );
        if(nx_dns_srv_entry_ptr[i] -> nx_dns_srv_hostname_ptr)
            printf("hostname = %s\n", nx_dns_srv_entry_ptr[i] -> nx_dns_srv_hostname_ptr);
        else
            printf("hostname is not set\n");
    }

    /* Get the service info, NetX old API.*/
    status = nx_dns_info_by_name_get(&client_dns, (UCHAR *)"www.my_example.com", &host_address, &host_port, 200);

    /* Check for DNS add server error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

    else
    {

        printf("------------------------------------------------------\n");
        printf("Test SRV: ");
        printf("IP address: %d.%d.%d.%d\n",
                host_address >> 24,
                host_address >> 16 & 0xFF,
                host_address >> 8 & 0xFF,
                host_address & 0xFF);
        printf("port number = %d\n", host_port);
    }

/********************************************************************************/
/*                                  Type SOA                                    */
/* Send SOA type DNS Query to its DNS server and get zone of start of authority.*/
/********************************************************************************/

     /* Send SOA DNS query type to record the zone of start of authority in record_buffer. */
     status = nx_dns_authority_zone_start_get(&client_dns, (UCHAR *)"www.my_example.com", &record_buffer[0], BUFFER_SIZE, 400);

     /* Check for DNS query error. */
     if (status != NX_SUCCESS)
     {
         error_counter++;
     }

     /* Get the loc*/
     nx_dns_soa_entry_ptr = (NX_DNS_SOA_ENTRY *) record_buffer;
     printf("------------------------------------------------------\n");
     printf("Test SOA: \n");
     printf("serial = %d\n", nx_dns_soa_entry_ptr -> nx_dns_soa_serial );
     printf("refresh = %d\n", nx_dns_soa_entry_ptr -> nx_dns_soa_refresh );
     printf("retry = %d\n", nx_dns_soa_entry_ptr -> nx_dns_soa_retry );
     printf("expire = %d\n", nx_dns_soa_entry_ptr -> nx_dns_soa_expire );
     printf("minmum = %d\n", nx_dns_soa_entry_ptr -> nx_dns_soa_minmum );
     if(nx_dns_soa_entry_ptr -> nx_dns_soa_host_mname_ptr)
         printf("host mname = %s\n", nx_dns_soa_entry_ptr -> nx_dns_soa_host_mname_ptr);
     else
         printf("host mame is not set\n");
     if(nx_dns_soa_entry_ptr -> nx_dns_soa_host_rname_ptr)
         printf("host rname = %s\n", nx_dns_soa_entry_ptr -> nx_dns_soa_host_rname_ptr);
     else
         printf("host rname is not set\n");


#endif

    /* Shutting down...*/

    /* Terminate the DNS Client thread. */
    status = nx_dns_delete(&client_dns);

    return;
}
```
## <a name="configuration-options"></a>구성 옵션

NetX용 DNS를 빌드하기 위한 몇 가지 구성 옵션이 있습니다. 해당 옵션은 *nxd_dns.h* 에서 다시 정의할 수 있습니다. 다음 목록에서 각각에 관해 자세히 설명합니다.  

- **NX_DNS_TYPE_OF_SERVICE**: DNS UDP 요청에 필요한 서비스 형식입니다. 기본적으로 이 값은 일반 IP 패킷 서비스에 대한 NX_IP_NORMAL로 정의됩니다.

- **NX_DNS_TIME_TO_LIVE**: 패킷을 버리기 전까지 패킷이 통과할 수 있는 최대 라우터 수를 지정합니다. 기본값은 0x80입니다.

- **NX_DNS_FRAGMENT_OPTION**: 나가는 패킷의 조각화를 허용하거나 금지하도록 소켓 속성을 설정합니다. 기본값은 NX_DONT_FRAGMENT입니다.

- **NX_DNS_QUEUE_DEPTH**: 소켓 수신 큐에 저장할 최대 패킷 수를 설정합니다. 기본값은 5입니다.

- **NX_DNS_MAX_SERVERS**: 클라이언트 서버 목록에서 최대 DNS 서버 수를 지정합니다. 기본값은 5입니다.

- **NX_DNS_MESSAGE_MAX**: DNS 쿼리를 보내기 위한 최대 DNS 메시지 크기입니다. 기본값은 512입니다. 이 값은 RFC 1035 섹션 2.3.4에 지정된 최대 크기이기도 합니다.

- **NX_DNS_PACKET_PAYLOAD_UNALIGNED**: 정의되지 않은 경우 이더넷, IP(또는 IPv6), UDP 헤더에서 정의한 클라이언트 패킷 페이로드의 크기와 NX_DNS_MESSAGE_MAX에서 지정한 최대 DNS 메시지 크기의 합입니다. 정의 여부와 관계없이 패킷 페이로드는 NX_DNS_PACKET_PAYLOAD에 4바이트로 맞춰져 저장됩니다.

- **NX_DNS_PACKET_POOL_SIZE**: NX_DNS_CLIENT_USER_CREATE_PACKET_POOL을 정의하지 않은 경우, DNS 쿼리를 보내기 위한 클라이언트 패킷 풀의 크기입니다. 기본값은 NX_DNS_PACKET_PAYLOAD에서 정의한 페이로드 크기의 패킷 16개에 충분할 만큼 크며, 4바이트로 맞춰집니다.

- **NX_DNS_MAX_RETRIES**: DNS 클라이언트가 다른 서버를 시도하거나 DNS 쿼리를 중단하기 전까지, 현재 DNS 서버를 쿼리하는 최대 횟수입니다. 기본값은 3입니다.

- **NX_DNS_MAX_RETRANS_TIMEOUT**: DNS 쿼리에서 특정 DNS 서버에 대한 최대 재전송 시간 제한입니다. 기본값은 64초(64 *NX_IP_PERIODIC_RATE)입니다.

- **NX_DNS_IP_GATEWAY_AND_DNS_SERVER**: 정의된 상태에서 클라이언트 IPv4 게이트웨이 주소가 0이 아닌 경우, DNS 클라이언트는 IPv4 게이트웨이를 클라이언트의 주 DNS 서버로 설정합니다. 기본값은 사용 안 함입니다.

- **NX_DNS_PACKET_ALLOCATE_TIMEOUT**: DNS 클라이언트 패킷 풀에서 패킷을 할당하는 시간 제한 옵션을 설정합니다. 기본값은 1초(1*NX_IP_PERIODIC_RATE)입니다.

- **NX_DNS_CLIENT_USER_CREATE_PACKET_POOL**: DNS 클라이언트에서 애플리케이션이 DNS 클라이언트 패킷 풀을 만들고 설정하게 할 수 있습니다. 기본적으로 이 옵션은 사용하지 않도록 설정되어 있고 DNS 클라이언트는 *nx_dns_create* 에서 자체 패킷 풀을 만듭니다.

- **NX_DNS_CLIENT_CLEAR_QUEUE**: DNS 클라이언트가 새 쿼리를 보내기 전에 수신 큐의 오래된 DNS 메시지를 지울 수 있습니다. 이전 DNS 쿼리에서 해당 패킷을 제거하면 DNS 클라이언트 소켓 큐에서 오버플로와 유효 패킷 폐기를 방지할 수 있습니다.

- **NX_DNS_ENABLE_EXTENDED_RR_TYPES**: DNS 클라이언트가 다른 DNS 레코드 형식(예: CNAME, NS, MX, SOA, SRV, TXT)에 대해 쿼리할 수 있습니다.

- **NX_DNS_CACHE_ENABLE**: DNS 클라이언트가 DNS 캐시에 응답 레코드를 저장할 수 있습니다.