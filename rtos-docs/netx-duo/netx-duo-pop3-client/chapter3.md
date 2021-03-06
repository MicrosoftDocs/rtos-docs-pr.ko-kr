---
title: 3장 - POP3 클라이언트 서비스 설명
description: 이 장에서는 아래에 나열된 모든 NetX Duo POP3 클라이언트 서비스를 알파벳 순서로 설명합니다.
author: philmea
ms.author: philmea
ms.date: 07/09/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: c8608f3894eba4db557f0c67b1042f2c88362cb0ca4bf6034bff9ae591fe26bc
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116797180"
---
# <a name="chapter-3---description-of-pop3-client-services"></a>3장 - POP3 클라이언트 서비스 설명

이 장에서는 아래에 나열된 모든 NetX Duo POP3 클라이언트 서비스를 알파벳 순서로 설명합니다.

> [!NOTE]
> 다음 API 설명의 "반환 값" 섹션에서 **BOLD** 의 값은 API 오류 검사를 사용하지 않도록 설정하는 데 사용되는 **NX_DISABLE_ERROR_CHECKING** 정의의 영향을 받지 않지만, 굵게 표시되지 않은 값은 완전히 사용하지 않도록 설정됩니다.

## <a name="nx_pop3_client_create"></a>nx_pop3_client_create

IPv4에 대한 POP3 클라이언트 인스턴스 만들기

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_create(NX_POP3_CLIENT *client_ptr,
    UINT APOP_authentication, NX_IP *ip_ptr,
    NX_PACKET_POOL *packet_pool_ptr,
    ULONG server_ip_address, ULONG server_port,
    CHAR *client_name, CHAR *client_password);
```

### <a name="description"></a>Description

이 서비스는 POP3 클라이언트의 인스턴스를 만듭니다. IPv4 POP3 서버 주소만 지원합니다.

먼저 디바이스 애플리케이션에서 패킷을 전송하기 위해 POP3 클라이언트에 대한 IP 인스턴스 및 패킷 풀을 만들어야 합니다. 이 패킷 풀은 POP3 클라이언트 작업에서 단독으로 사용하거나 IP 인스턴스를 만들 때 사용되는 것과 동일한 패킷 풀을 사용하여 생성됩니다. 패킷 풀은 이더넷 드라이버 패킷 풀과 공유될 수도 있지만 이 경우에는 POP3 클라이언트가 비교적 작은 POP3 메시지 패킷을 서버에 보내기 위해 페이로드가 잠재적으로 많은 패킷 페이로드를 수신하기 위한 페이로드가 있는 대량 패킷 풀을 사용할 때의 단점이 있습니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 만들 클라이언트에 대한 포인터
- **APOP_authentication** APOP 인증 사용
- **ip_ptr** IP 인스턴스에 대한 포인터
- **packet_pool_ptr** 클라이언트 패킷 풀에 대한 포인터
- **server_ip_address** POP3 서버 IPv4 주소
- **server_port** POP3 서버 포트
- **client_name** 클라이언트 이름에 대한 포인터
- **client_password** 클라이언트 암호에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 클라이언트를 성공적으로 만듦
- **status** NetX Duo 및 ThreadX 서비스 호출의 상태 완료
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수
- NX_POP3_PARAM_ERROR (0xB1) 잘못된 포인터가 아닌 입력

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
/* Create the POP3 Client. Note that the Client uses its password for its APOP shared
    secret. */

/* Set up user defined callback services. */
/* Create username and password for our POP3 Client mail drop. */
#define LOCALHOST "recipient@expresslogic.com"
#define LOCALHOST_PASSWORD "pass"
#define POP3_SERVER_ADDRESS IP_ADDRESS(192,2,2,92)
#define POP3_SERVER_PORT 110

/* Create a NetX POP3 Client instance. */
status = nx_pop3_client_create(&demo_client,
    NX_FALSE /* disable APOP authentication */,
    &client_ip, &client_packet_pool,
    POP3_SERVER_ADDRESS, POP3_SERVER_PORT,
    LOCALHOST, LOCALHOST_PASSWORD);

/* If the Client was successfully created, status = NX_SUCCESS. */
```

## <a name="nxd_pop3_client_create"></a>nxd_pop3_client_create

IPv4 또는 IPv6에 대한 POP3 클라이언트 인스턴스 만들기

### <a name="prototype"></a>프로토타입

```C
UINT nxd_pop3_client_create(NX_POP3_CLIENT *client_ptr,
    UINT APOP_authentication, NX_IP *ip_ptr,
    NX_PACKET_POOL *packet_pool_ptr,
    NXD_ADDRESS *server_ip_address,
    ULONG server_port,
    CHAR *client_name, CHAR *client_password);
```

### <a name="description"></a>Description

이 서비스는 POP3 클라이언트의 인스턴스를 만듭니다. IPv4 및 IPv6 POP3 서버 주소를 모두 지원합니다. POP3 클라이언트 만들기 프로세스에 대한 자세한 내용은 앞에서 설명한 *nx_pop3_client_create* 서비스를 참조하세요.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 만들 클라이언트에 대한 포인터
- **APOP_authentication** APOP 인증 사용
- **ip_ptr** IP 인스턴스에 대한 포인터
- **packet_pool_ptr** 클라이언트 패킷 풀에 대한 포인터
- **server_ip_address** POP3 서버 IPv6 또는 IPv4 주소
- **server_port** POP3 서버 포트
- **client_name** 클라이언트 이름에 대한 포인터
- **client_password** 클라이언트 암호에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 클라이언트를 성공적으로 만듦
- **status** NetX Duo 및 ThreadX 서비스 호출의 상태 완료
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수
- NX_POP3_PARAM_ERROR (0xB1) 잘못된 포인터가 아닌 입력

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
/* Create the POP3 Client. */

/* Create username and password for our POP3 Client mail drop. */
#define LOCALHOST "recipient@expresslogic.com"
#define LOCALHOST_PASSWORD "pass"
#define POP3_SERVER_PORT 110

/* Create a NetX POP3 Client instance. Also note the details of IPv6 address validation
    and enabling IPv6 and ICMPv6 services on the IP task are not shown here. See the
    NetX Duo User Guide for more details on this process.
*/
NXD_ADDRESS server_ip_address;

/* Set client IP interface address. */
server_ip_address.nxd_ip_version = NX_IP_VERSION_V6;
server_ip_address.nxd_ip_address.v6[0] = 0x20010db8;
server_ip_address.nxd_ip_address.v6[1] = 0xf101;
server_ip_address.nxd_ip_address.v6[2] = 0;
server_ip_address.nxd_ip_address.v6[3] = 0x101;
status = nxd_pop3_client_create(&demo_client,
    NX_FALSE /* disable APOP authentication */,
    &client_ip, &client_packet_pool,
    &server_ip_address, POP3_SERVER_PORT,
    LOCALHOST, LOCALHOST_PASSWORD);

/* If the Client was successfully created, status = NX_SUCCESS. */
```

## <a name="nx_pop3_client_delete"></a>nx_pop3_client_delete

POP3 클라이언트 인스턴스 삭제

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_delete(NX_POP3_CLIENT *client_ptr);
```

### <a name="description"></a>Description

이 서비스는 이전에 만든 POP3 클라이언트를 삭제합니다. 이 서비스는 POP3 클라이언트 패킷 풀을 삭제하지 않습니다. 디바이스 애플리케이션은 더 이상 패킷 풀을 사용하지 않는 경우 이 리소스를 개별적으로 삭제해야 합니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 삭제할 클라이언트에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 클라이언트를 성공적으로 삭제함
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
/* Delete the POP3 Client. */
status = nx_pop3_client_delete (&demo_client);

/* If the Client was successfully deleted, status = NX_SUCCESS. */
```

## <a name="nx_pop3_client_mail_item_delete"></a>nx_pop3_client_mail_item_delete

클라이언트 maildrop에서 지정된 메일 항목 삭제

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_mail_items_delete(NX_POP3_CLIENT *client_ptr,
    UINT mail_index);
```

### <a name="description"></a>Description

이 서비스는 클라이언트 maildrop에서 지정된 메일 항목을 삭제합니다. 이는 메일 항목을 다운로드한 후에 사용할 수 있지만 일부 POP3 서버는 클라이언트가 요청한 후에 메일 항목을 자동으로 삭제할 수 있습니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 클라이언트 인스턴스에 대한 포인터
- **mail_index** 클라이언트 maildrop에 대한 인덱스

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 삭제 요청 성공
- **NX_POP3_INVALID_MAIL_ITEM** (0xB2) 잘못된 메일 항목 인덱스
- **NX_POP3_INSUFFICIENT_PACKET_PAYLOAD** (0xB6) POP3 요청에 대한 클라이언트 패킷 페이로드가 너무 작습니다.
- **NX_POP3_SERVER_ERROR_STATUS** (0xB4) 서버가 오류 상태로 응답
- NX_POP3_CLIENT_INVALID_INDEX (0xB8) 잘못된 메일 인덱스 입력
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
ULONG item_index;

/* Delete the POP3 Client mail item. */
status = nx_pop3_client_mail_item_delete(&demo_client, item_index);

/* If the server accepts the DELE request (and deletes the mail item), status =
    NX_SUCCESS. */
```

## <a name="nx_pop3_client_mail_item_get"></a>nx_pop3_client_mail_item_get

지정된 메일 항목 검색

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_mail_item_get(NX_POP3_CLIENT *client_ptr,
    UINT mail_item, ULONG *item_size);
```

### <a name="description"></a>Description

이 서비스는 mail_item 인덱스에 지정된 클라이언트 maildrop에서 메일 항목을 검색하는 RETR 요청을 만듭니다. RETR 요청을 만들고 서버에서 긍정 응답을 받은 후 클라이언트는 *nx_pop3_client_mail_item_message_get* 서비스를 사용하여 메일 메시지 다운로드를 시작할 수 있습니다. 또한 서비스는 서버 회신에서 추출된 요청된 메일 항목의 크기를 제공합니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 클라이언트 인스턴스에 대한 포인터
- **mail_item** 클라이언트 maildrop에 대한 인덱스
- **item_size** 메일 메시지의 크기에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 메일 항목을 성공적으로 검색함
- **NX_POP3_INVALID_MAIL_ITEM** (0xB2) 잘못된 메일 항목 인덱스
- **NX_POP3_INSUFFICIENT_PACKET_PAYLOAD** (0xB6) POP3 요청에 대한 클라이언트 패킷 페이로드가 너무 작습니다.
- **NX_POP3_SERVER_ERROR_STATUS** (0xB4) 서버가 오류 상태로 응답
- NX_POP3_CLIENT_INVALID_INDEX (0xB8) 잘못된 메일 인덱스 입력
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
ULONG item_size;

/* Retrieve the POP3 Client mail item. */
status = nx_pop3_client_mail_item_get (&demo_client, 1, &item_size);

/* If the mail item was successfully obtained, status = NX_SUCCESS. */
```

## <a name="nx_pop3_client_mail_items_get"></a>nx_pop3_client_mail_items_get

maildrop의 메일 항목 수 검색

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_mail_items_get(NX_POP3_CLIENT *client_ptr,
    UINT *number_mail_items,
    ULONG *maildrop_total_size);
```

### <a name="description"></a>Description

이 서비스는 클라이언트 maildrop에서 메일 항목 수와 메일 메시지 데이터의 총 크기를 검색하는 STAT 요청을 만듭니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 클라이언트 인스턴스에 대한 포인터
- **number_mail_item** 클라이언트 maildrop의 메일 수
- **maildrop_total_size** 모든 메일 메시지의 크기에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 메일 항목을 성공적으로 검색함
- **NX_POP3_INVALID_MAIL_ITEM** (0xB2) 잘못된 메일 항목 인덱스
- **NX_POP3_INSUFFICIENT_PACKET_PAYLOAD** (0xB6) POP3 요청에 대한 클라이언트 패킷 페이로드가 너무 작습니다.
- **NX_POP3_SERVER_ERROR_STATUS** (0xB4) 서버가 오류 상태로 응답
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
UINT number_mail_items;

ULONG maildrop_total_size;

/* Retrieve the size and number of items in POP3 Client maildrop. */

status = nx_pop3_client_mail_item_get (&demo_client, 1, &number_mail_items,
    &maildrop_total_size);

/* If the maildrop data was successfully obtained, status = NX_SUCCESS. */
```

## <a name="nx_pop3_client_mail_item_message_get"></a>nx_pop3_client_mail_item_message_get

지정된 메일 항목 메시지 검색

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_mail_item_message_get(
    NX_POP3_CLIENT *client_ptr,
    NX_PACKET **recv_packet_ptr,
    ULONG *bytes_retrieved,
    UINT *final_packet);
```

### <a name="description"></a>Description

이 서비스는 메일 항목 메시지, 메일 메시지의 크기 및 메일 메시지의 마지막 패킷인지 여부를 검색합니다. final_packet이 NX_TRUE인 경우 recv_packet_ptr가 가리키는 패킷이 메일 항목 메시지의 최종 패킷입니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 클라이언트 인스턴스에 대한 포인터
- **recv_packet_ptr** 메시지 데이터의 받은 패킷
- **number_mail_item** 클라이언트 maildrop의 메일 수
- **maildrop_total_size** 모든 메일 메시지의 크기에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 메일 항목을 성공적으로 검색함
- **NX_POP3_CLIENT_INVALID_STATE** (0xB7) POP3 요청에 대한 클라이언트 패킷 페이로드가 너무 작습니다.
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
NX_PACKET *recv_packet_ptr;

ULONG bytes_retrieved;

UINT final_packet;

/* Retrieve the size and number of items in POP3 Client maildrop. */

status = nx_pop3_client_mail_item_message_get (&demo_client, &recv_packet_ptr,
    bytes_retrieved, final_packet);

/* If the maildrop message packet was successfully obtained, status = NX_SUCCESS. */
```

## <a name="nx_pop3_client_mail_item_size_get"></a>nx_pop3_client_mail_item_size_get

지정된 메일 항목의 크기 검색

### <a name="prototype"></a>프로토타입

```C
UINT nx_pop3_client_mail_item_size_get(
    NX_POP3_CLIENT *client_ptr,
    UINT mail_item, ULONG *size);
```

### <a name="description"></a>Description

이 서비스는 지정된 메일 항목의 크기를 가져오는 LIST 요청을 만듭니다.

### <a name="input-parameters"></a>입력 매개 변수

- **client_ptr** 클라이언트 인스턴스에 대한 포인터
- **mail_item** 클라이언트 maildrop에 대한 인덱스
- **size** 메일 메시지의 크기에 대한 포인터

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 메일 항목을 성공적으로 검색함
- **NX_POP3_INVALID_MAIL_ITEM** (0xB2) 잘못된 메일 항목 인덱스
- **NX_POP3_INSUFFICIENT_PACKET_PAYLOAD** (0xB6) POP3 요청에 대한 클라이언트 패킷 페이로드가 너무 작습니다.
- **NX_POP3_SERVER_ERROR_STATUS** (0xB4) 서버가 오류 상태로 응답
- NX_POP3_CLIENT_INVALID_INDEX (0xB8) 잘못된 메일 인덱스 입력
- NX_PTR_ERROR (0x07) 잘못된 입력 포인터 매개 변수

### <a name="allowed-from"></a>허용 위치

애플리케이션 코드

### <a name="example"></a>예제

```C
ULONG size;

UINT mail_item;

/* Retrieve the size of the specified mail item in POP3 Client maildrop. */

status = nx_pop3_client_mail_item_size_get (&demo_client, mail_item, &size);

/* If the maildrop message packet was successfully obtained, status = NX_SUCCESS. */
```
