---
title: 챕터 4 - mDNS 서비스 설명
description: 이 챕터에는 모든 NetX mDNS 서비스에 대한 설명이 포함되어 있습니다.
author: philmea
ms.author: philmea
ms.date: 07/09/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 6e37698ac6023b4cff6cb4fc05330a73b678ef3d2a813a706c9b821381e123db
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116797571"
---
# <a name="chapter-4---description-of-mdns-services"></a>챕터 4 - mDNS 서비스 설명

이 챕터에는 모든 NetX mDNS 서비스에 대한 설명이 포함되어 있습니다(아래 참조).

> [!NOTE]
> 다음 API 설명의 "반환 값" 섹션에서 **BOLD** 의 값은 API 오류 검사를 사용하지 않도록 설정하는 데 사용되는 **NX_DISABLE_ERROR_CHECKING** 정의의 영향을 받지 않지만, 굵게 표시되지 않은 값은 완전히 사용하지 않도록 설정됩니다.

## <a name="nx_mdns_create"></a>nx_mdns_create

mDNS 인스턴스 만들기

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_create(NX_MDNS *mdns_ptr, NX_IP *ip_ptr,
    NX_PACKET_POOL *packet_pool,
    UINT priority, VOID *stack_ptr,
    UINT stack_size, UCHAR *host_name,
    VOID *local_service_cache,
    UINT local_service_cache_size,
    VOID *peer_service_cache,
    UINT peer_service_cache_size,
    VOID (*probing_notify)(NX_MDNS *mdns_ptr,
        UCHAR *name, UINT probing_state));
```

### <a name="description"></a>Description

이 서비스는 특정 IP 인스턴스 및 관련 리소스에 mDNS 인스턴스를 만듭니다. 들어오는 mDNS 메시지를 처리하고, 쿼리에 응답하고, 쿼리 메시지를 정기적으로 전송하는 스레드도 만듭니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **ip_ptr** 연결된 IP 인스턴스에 대한 포인터입니다.
- **packet_pool** 유효한 패킷 풀에 대한 포인터입니다.
- **priority** mDNS 스레드의 우선 순위입니다.
- **stack_ptr** mDNS 스레드의 스택 영역에 대한 포인터입니다.
- **stack_size** 스택 영역의 크기입니다.
- **host_name** 이 노드에 할당된 호스트 이름입니다.
- **local_service_cache** 로컬 등록 서비스에 대한 스토리지 공간입니다.
- **local_service_cache_size** 로컬 서비스 캐시의 크기입니다.
- **peer_service_cache** 수신된 서비스 정보에 대한 스토리지 공간입니다.
- **peer_service_cache_size** 피어 서비스 캐시의 크기입니다.
- **probing_notify** 검색 작업이 끝날 때 호출되는 선택적 콜백 함수입니다. 호스트 이름(로컬 인터페이스에서 mDNS를 사용하도록 설정하는 경우) 또는 서비스 이름(서비스를 등록한 후)이 고유한지 여부를 애플리케이션에 알립니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) mDNS 인스턴스를 성공적으로 만들었습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
UCHAR stack_ptr[2048];
UCHAR local_cache_ptr[2048];
UCHAR peer_cache_ptr[8192];

/* Create a mDNS instance. */
status = nx_mdns_create(&my_mdns, &ip_0, &pool_0,
    3, stack_ptr, 2048,
    “NETX-MDNS-HOST”,
    local_cache_ptr, 2048,
    peer_cache_ptr, 8192,
    probing_notify);

/* If status is NX_SUCCESS, mDNS instance was created. */
```

## <a name="nx_mdns_delete"></a>nx_mdns_delete

mDNS 인스턴스 삭제

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_delete(NX_MDNS *mdns_ptr);
```

### <a name="description"></a>Description

이 서비스는 mDNS 인스턴스를 삭제하고 해당 리소스를 해제합니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) mDNS 인스턴스를 성공적으로 삭제했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Delete a previously created mDNS instance. */

status = nx_mdns_delete(&my_mdns);

/* If status is NX_SUCCESS, the mDNS instance has been deleted. */
```

## <a name="nx_mdns_enable"></a>nx_mdns_enable

mDNS 서비스 시작

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_enable(NX_MDNS *mdns_ptr, UINT interface_index);
```

### <a name="description"></a>Description

이 API는 특정 실제 인터페이스에서 mDNS 서비스를 사용하도록 설정합니다. 서비스를 사용하도록 설정하면 mDNS 모듈이 인터페이스에서 받은 쿼리에 응답하기 전에 먼저 네트워크에서 모든 고유한 서비스 이름을 검색합니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 인스턴스 제어 블록에 대한 포인터입니다.
- **interface_index** 서비스를 사용하도록 설정할 인터페이스에 대한 인덱스입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00)에서 서비스를 성공적으로 사용하도록 설정했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Enable mDNS service on specific interface. */

status = nx_mdns_enable(&my_mdns, 0);

/* If status is NX_SUCCESS, mDNS service was enabled. */
```

## <a name="nx_mdns_disable"></a>nx_mdns_disable

mDNS 서비스 사용 안 함

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_disable(NX_MDNS *mdns_ptr, UINT interface_index);
```

### <a name="description"></a>Description

이 API는 특정 실제 인터페이스에서 mDNS 서비스를 사용하지 않도록 설정합니다. 서비스를 사용하지 않도록 설정하면 mDNS가 인터페이스에 연결된 네트워크에 모든 로컬 서비스에 대한 "종료" 메시지를 보내므로 인접 노드에 알림이 표시됩니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **interface_index** 서비스를 사용하지 않도록 설정할 인터페이스에 대한 인덱스입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00)에서 서비스를 성공적으로 사용하지 않도록 설정했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Disable mDNS service on specific interface. */

status = nx_mdns_disable(&my_mdns, 0);

/* If status is NX_SUCCESS, mDNS service was disabled. */
```

## <a name="nx_mdns_cache_notify_set"></a>nx_mdns_cache_notify_set

mDNS 캐시 가득참 알림 함수

### <a name="prototype"></a>프로토타입

```c
UINT nx_mdns_cache_notify_set(NX_MDNS *mdns_ptr,
    VOID (*cache_full_notify_cb)(NX_MDNS *mdns_ptr,
        UINT state, UINT cache_type));
```

### <a name="description"></a>Description

이 서비스는 로컬 서비스 캐시 또는 피어 서비스 캐시가 가득찰 때 호출되는 사용자 제공 콜백 함수를 설치합니다. 서비스 캐시가 가득차면 더 이상 mDNS 리소스 레코드를 추가할 수 없습니다. 다양한 문자열 길이의 서비스를 추가 및 제거하면 내부 조각화로 인해 서비스 캐시가 가득찰 수 있습니다. 피어 서비스 캐시에 대한 캐시 가득참 알림이 수신되면 애플리케이션이 "*nx_mdns_service_cache_clear"* 서비스를 사용하여 피어 서비스 캐시의 모든 항목을 지울 수 있습니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) mDNS 캐시 알림 콜백 함수를 성공적으로 설치했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Set mDNS cache notify callback. */

status = nx_mdns_cache_notify_set(&my_mdns, cache_full_nofiy_cb);

/* If status is NX_SUCCESS, mDNS cache notify callback was set. */
```

## <a name="nx_mdns_cache_notify_clear"></a>nx_mdns_cache_notify_clear

mDNS 서비스 캐시 가득참 알림 함수 지우기

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_cache_notify_clear(NX_MDNS *mdns_ptr);
```

### <a name="description"></a>Description

이 서비스는 사용자 제공 서비스 캐시 알림 콜백 함수를 지웁니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) mDNS 서비스 캐시 알림 콜백 함수를 성공적으로 지웠습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Clear mDNS cache notify callback. */

status = nx_mdns_cache_notify_clear(&my_mdns);

/* If status is NX_SUCCESS, mDNS cache notify callback clear. */
```

## <a name="nx_mdns_domain_name_set"></a>nx_mdns_domain_name_set

도메인 이름 설정

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_domain_name_set(NX_MDNS *mdns_ptr, CHAR *domain_name);
```

### <a name="description"></a>Description

이 서비스는 기본 로컬 도메인 이름을 설정합니다. mDNS 인스턴스를 만들 때 기본 로컬 도메인 이름은 ".local"로 설정됩니다. 이 API를 사용하면 애플리케이션에서 기본 로컬 도메인 이름을 덮어쓸 수 있습니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **domain_name** 사용할 도메인 이름입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00)에서 로컬 도메인을 성공적으로 구성했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Set the domain name. */

status = nx_mdns_domain_name_set(&my_mdns, “home”);

/* If status is NX_SUCCESS, the “home” domain name was set. */
```

## <a name="nx_mdns_service_announcement_timing_set"></a>nx_mdns_service_announcement_timing_set

서비스 공지 메시지의 타이밍 매개 변수 설정

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_announcement_timing_set(NX_MDNS *mdns_ptr,
    UINT t, UINT p, UINT k, UINT retrans_interval,
    UINT period_interval, UINT max_time);
```

### <a name="description"></a>Description

이 서비스는 서비스 공지를 보낼 때 mDNS에서 사용하는 타이밍 매개 변수를 재구성합니다. 게시 기간은 *t* 틱에서 시작되고 2의 *k* 계수 제곱으로 망원식으로 확장될 수 있습니다. 보급 알림당 반복 횟수는 *p* 이고, 반복되는 각 보급 알림 사이의 간격은 *interval* 틱이며, 공지 기간 수는 max_time입니다. 기본적으로 초기 기간은 k = 1(기간이 두 배씩 증가), *p = 1*(반복 안 함), retrans_interval = 0(시간 간격 없음), period_interval = 0xFFFFFFFF(최대 기간 간격) 및 max_time = 3(보급 알림 수)을 사용하여 1초로 설정됩니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **t** 초기 기간의 틱 수입니다. 기본값은 100틱(1초)입니다.
- **p** 반복 횟수입니다. 기본값은 1입니다.
- **k** 망원식 계수입니다. 기본값은 1입니다.
- **retrans_interval** 반복된 공지 메시지를 보내기 전에 대기할 틱 수입니다. 기본값은 0입니다.
- **period_interval** 두 공지 기간 사이의 틱 수입니다. 기본값은 0xFFFFFFFF입니다.
- **max_time** 보급 알림에 사용할 공지 기간의 수입니다. *max_time* 공지 기간 후에는 더 이상 공지 메시지가 전송되지 않습니다. 기본값은 3입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 타이밍 값을 성공적으로 설정했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Set the service announcement timing. */

status = nx_mdns_service_announcement_timing_set(&my_mdns, 100,
    1, 1, 0, 0xFFFFFFFF, 3);

/* If status is NX_SUCCESS, the service announcement timing was set. */
```

## <a name="nx_mdns_service_add"></a>nx_mdns_service_add

로컬 서비스 추가

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_add(NX_MDNS *mdns_ptr, CHAR *instance,
    CHAR *service, CHAR *subtype, UINT ttl, USHORT priority,
    USHORT weight, USHORT port, UCHAR *text, UCHAR is_unique,
    UINT interface_index);
```

### <a name="description"></a>Description

이 API는 애플리케이션에서 제공하는 서비스를 등록합니다. *is_unique* 플래그를 설정하면 mDNS가 네트워크에서 서비스를 알리기 전에 로컬 네트워크에서 서비스 이름을 검색하여 고유한지 확인합니다. *Instance* 는 서비스 이름의 인스턴스 부분입니다. *service* 는 서비스 이름의 서비스 부분입니다. 예를 들어 "_http._tcp"는 서비스입니다. 하위 유형으로 서비스를 설명하려면 호출자가 *subtype* 매개 변수를 사용해야 합니다. 예를 들어 원하는 서비스가 “_printer._sub._http._tcp”이면 서비스 필드는 “_http._tcp:”이고, 하위 유형 필드는 “_printer”입니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **instance** 서비스의 인스턴스 이름에 대한 포인터입니다.
- **service** 하위 유형 정보를 제외한 mDNS 서비스 유형에 대한 포인터입니다.
- **subtype** mDNS 서비스의 하위 유형 부분에 대한 포인터입니다(해당하는 경우).
- **priority** 서비스 우선 순위입니다.
- **weight** 서비스 가중치입니다.
- **port** 서비스에서 사용하는 TCP 또는 UDP 포트 번호입니다.
- **text** 추가 텍스트 정보입니다.
- **is_unique** 서비스가 공유되는지, 고유한지를 나타내는 부울 플래그입니다. 고유한 것으로 등록된 서비스의 경우 mDNS가 서비스를 제공하기 전에 네트워크에서 서비스를 검색해야 합니다.
- **Interface_index** 서비스가 제공되는 실제 인터페이스입니다. 연결된 서비스를 통해 제공되는 서비스의 경우 *NX_MDNS_ALL_INTERFACES* 값이 사용됩니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 서비스를 성공적으로 등록했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Add local service. */

status = nx_mdns_service_add(&my_mdns, “NETX-SERVICE”,
    “_http._tcp”, NX_NULL,
    NX_NULL, 0, 0, 0, 80, NX_TRUE, 0);

/* If status is NX_SUCCESS, The service (NetX-SERVICE._http._tcp.local) was added. */
```

## <a name="nx_mdns_service_delete"></a>nx_mdns_service_delete

이전에 등록된 서비스 제거

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_delete(NX_MDNS *mdns_ptr,
    CHAR *instance, CHAR *service,
    CHAR *subtype);
```

### <a name="description"></a>Description

이 API는 이전에 등록된 서비스를 삭제합니다. 서비스가 삭제되면 "종료" 메시지가 로컬 네트워크로 전송되어 인접 노드에 알림이 표시됩니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **instance** 서비스의 인스턴스 이름에 대한 포인터입니다.
- **service** 하위 유형 정보를 제외한 mDNS 서비스 유형에 대한 포인터입니다.
- **subtype** mDNS 서비스의 하위 유형 부분에 대한 포인터입니다(해당하는 경우).

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 서비스를 성공적으로 삭제했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Delete local service. */

status = nx_mdns_service_delete(&my_mdns, “NETX-SERVICE”, “_http._tcp”, NX_NULL);

/* If status is NX_SUCCESS, The service (NetX-SERVICE._http._tcp.local) was deleted. */
```

## <a name="nx_mdns_service_one_shot_query"></a>nx_mdns_service_one_shot_query

원샷 서비스 검색 시작

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_one_shot_query(NX_MDNS *mdns_ptr,
    UCHAR *instance,
    UCHAR *service,
    UCHAR *subtype,
    NX_MDNS_SERVICE *service_ptr, ULONG wait_option);
```

### <a name="description"></a>Description

이 서비스는 원샷 mDNS 쿼리를 수행합니다. 지정된 서비스가 피어 서비스 캐시에서 발견되면 첫 번째 인스턴스가 반환됩니다. 로컬 피어 서비스 캐시에서 서비스를 찾을 수 없는 경우 mDNS 모듈은 쿼리 명령을 실행하고 응답을 기다립니다. 첫 번째 응답이 수신되거나 쿼리 시간이 초과될 때까지 서비스가 차단됩니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **instance** 서비스의 인스턴스 이름에 대한 포인터입니다(해당하는 경우).
- **service** 하위 유형 정보를 제외한 mDNS 서비스 유형에 대한 포인터입니다. 애플리케이션이 서비스 유형을 지정해야 합니다.
- **subtype** mDNS 서비스의 하위 유형 부분에 대한 포인터입니다(해당하는 경우).
- **service_ptr** 쿼리 결과를 저장하는 NX_MDNS_SERVICE 구조체에 대한 사용자 제공 포인터입니다.
- **wait_option** 응답을 기다리는 시간(틱)입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 서비스 정보를 성공적으로 가져왔습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Start service one shot query. */

status = nx_mdns_service_one_shot_query(&my_mdns, “NETX-SERVICE”, “_http._tcp”,
    NX_NULL, service_ptr, 500);

/* If status is NX_SUCCESS, The query with
    name: NetX-SERVICE._http._tcp.local,
     type: ANY (SRV and TXT) was sent.
    And the answer was stored in service_ptr if success. */
```

## <a name="nx_mdns_service_continuous_query"></a>nx_mdns_service_continuous_query

연속 서비스 검색 시작

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_continous_query(NX_MDNS *mdns_ptr,
    CHAR *instance, CHAR *service, CHAR *subtype);
```

### <a name="description"></a>Description

이 서비스는 연속 쿼리를 시작합니다. 서비스는 즉시 반환됩니다. 연속 쿼리를 실행하면 애플리케이션이 API *nx_mdns_service_lookup* 을 사용하여 서비스 레코드를 검색할 수 있습니다. 애플리케이션은 연속 쿼리를 중지할 때 API *nx_mdns_service_query_stop* 을 사용할 수 있습니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **instance** 서비스의 인스턴스 이름에 대한 포인터입니다(해당하는 경우).
- **service** 하위 유형 정보를 제외한 mDNS 서비스 유형에 대한 포인터입니다(해당하는 경우).
- **subtype** mDNS 서비스의 하위 유형 부분에 대한 포인터입니다(해당하는 경우).

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 연속 쿼리를 성공적으로 시작했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Start service continuous query. */

status = nx_mdns_service_continuous_query(&my_mdns,
    “NETX-SERVICE”, “_http._tcp”, NX_NULL);

/* If status is NX_SUCCESS, The continuous query with
    name: NetX-SERVICE._http._tcp.local,
    type: ANY (SRV and TXT) was added.
    And the query will be periodically sent. */
```

## <a name="nx_mdns_service_query_stop"></a>nx_mdns_service_query_stop

이전에 실행된 연속 서비스 검색 중단

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_query_stop(NX_MDNS *mdns_ptr,
    CHAR *instance, CHAR *service, CHAR *subtype);
```

### <a name="description"></a>Description

이 API는 이전에 실행된 연속 서비스 검색을 종료합니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **instance** 서비스의 인스턴스 이름에 대한 포인터입니다.
- **service** 하위 유형 정보를 제외한 mDNS 서비스 유형에 대한 포인터입니다.
- **subtype** mDNS 서비스의 하위 유형 부분에 대한 포인터입니다(해당하는 경우).

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 연속 쿼리를 성공적으로 중지했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Stop service continuous query. */

status = nx_mdns_service_query_stop(&my_mdns, “NETX-SERVICE”, “_http._tcp”, NX_NULL);

/* If status is NX_SUCCESS, The continuous query with
    name: NetX-SERVICE._http._tcp.local,
    type: ANY (SRV and TXT) was stopped. */
```

## <a name="nx_mdns_service_lookup"></a>nx_mdns_service_lookup

로컬 피어 서비스 캐시에서 서비스 검색

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_lookup(NXD_MDNS *mdns_ptr,
    CHAR *instance, CHAR *service,
    CHAR *subtype, UINT instance_index,
    NXD_MDNS_SERVICE *service_ptr);
```

### <a name="description"></a>Description

이 서비스는 로컬 피어 서비스 캐시에서 인스턴스 이름(제공된 경우) 및 서비스 유형과 일치하는 서비스를 조회합니다. 애플리케이션은 캐시에서 설명과 일치하는 첫 번째 서비스를 찾기 위해 0으로 설정된 *instance_index* 를 사용하여 서비스 조회를 시작해야 합니다. 서비스가 캐시의 끝을 나타내는 *NX_NO_MORE_ENTRIES* 를 반환할 때까지, 애플리케이션은 캐시에서 추가 서비스를 검색할 때마다 *instance_index* 값을 늘려 가면서 이 서비스를 계속 사용해야 합니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **instance** 서비스의 인스턴스 이름에 대한 포인터입니다(해당하는 경우).
- **service** 하위 유형 정보를 제외한 mDNS 서비스 유형에 대한 포인터입니다(해당하는 경우).
- **subtype** mDNS 서비스의 하위 유형 부분에 대한 포인터입니다(해당하는 경우).
- **Instance_index** 반환할 인스턴스의 인덱스 번호입니다.
- **service_ptr** 조회 결과를 저장하는 NX_MDNS_SERVICE 구조체에 대한 사용자 제공 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 서비스를 성공적으로 검색했습니다.
- **NX_NO_MORE_ENTRIES** (0x17) 지정된 인덱스 번호에서 서비스 항목을 찾을 수 없습니다. 이 오류 코드는 검색의 끝을 나타냅니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Lookup the service on specific index. */

status = nx_mdns_service_lookup(&my_mdns, “NETX-SERVICE”, “_http._tcp”, NX_NULL,
    0, service_ptr);

/* If status is NX_SUCCESS, The service with
    name: NetX-SERVICE._http._tcp.local, was retrieved. */
```

## <a name="nx_mdns_service_ignore_set"></a>nx_mdns_service_ignore_set

서비스 무시 집합 구성

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_ignore_set(NX_MDNS *mdns_ptr, ULONG service_mask);
```

### <a name="description"></a>Description

이 API는 *service_mask* 비트 마스크로 지정된 서비스를 무시하도록 마스크를 구성합니다. 사용자는 필요에 따라 service_mask를 사용하여 캐시하지 않으려는 서비스 유형을 선택할 수 있습니다. 서비스 목록은 *nxd_mdns.c* 의 *nx_mdns_service_types* 테이블에 정의되어 있습니다. nx_mdns_service_types[]에서 첫 번째 서비스 형식의 해당 마스크는 0x00000001이고, 두 번째 서비스 형식의 마스크는 0x00000002인 식입니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **service_mask** 무시할 사용자 정의 서비스 형식입니다. 마스크는 32비트 ULONG 형식입니다. 각 비트는 사용자 정의 *nx_mdns_service_types* 배열의 항목을 나타냅니다. 비트가 설정되면 *nx_mdns_service_type* 배열에 지정된 해당 서비스 형식이 피어 서비스 캐시에 저장되지 않습니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 서비스 무시 마스크를 성공적으로 설정했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Set the service mask to ignore the specified service. */

status = nx_mdns_service_ignore_set(&my_mdns, 0x00000003);

/* If status is NX_SUCCESS, The service with
    type “_device-info” and “_http” will be ignored. */
```

## <a name="nx_mdns_service_notify_set"></a>nx_mdns_service_notify_set

서비스 변경 알림 콜백 함수 구성

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_notify_set(NX_MDNS *mdns_ptr, ULONG service_mask,
    VOID (*service_change_notify)(NX_MDNS *mdns_ptr,
    NX_MDNS_SERVICE *service_ptr, UINT state));
```

### <a name="description"></a>Description

이 API는 서비스 변경 알림 콜백 함수를 구성합니다. 이 콜백 함수는 네트워크의 다른 노드에서 제공하는 서비스가 추가, 변경되거나 더 이상 사용할 수 없을 때 호출됩니다. 사용자는 필요에 따라 service_mask를 사용하여 모니터링하려는 서비스 유형을 선택할 수 있습니다. 모니터링되는 서비스 목록은 *nxd_mdns.c* 의 *nx_mdns_service_types* 테이블에 하드 코딩됩니다.

nx_mdns_service_types[]에서 첫 번째 서비스 형식의 해당 마스크는 0x00000001이고, 두 번째 서비스 형식의 마스크는 0x00000002인 식입니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **service_mask** 모니터링할 사용자 정의 서비스 형식입니다. 마스크는 32비트 ULONG 형식입니다. 각 비트는 *nx_mdns_service_types* 배열의 항목을 나타냅니다.
- **service_change_notify** 지정된 서비스가 변경될 때 호출되는 콜백 함수입니다. 자세한 서비스 정보는 *service_ptr* 이 가리키는 메모리에 반환됩니다. 알림 콜백 함수에서 반환된 메모리의 콘텐츠는 더 이상 유효하지 않습니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 콜백 함수를 성공적으로 설치했습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Set the service mask to notify the specified service. */

status = nx_mdns_service_notify_set(&my_mdns, 0x00000002, service_change_notify);

/* If status is NX_SUCCESS, the callback will be called
    if received the service with type “_http”. */
```

## <a name="nx_mdns_service_notify_clear"></a>nx_mdns_service_notify_clear

서비스 변경 알림 콜백 함수 지우기

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_service_notify_clear(NX_MDNS *mdns_ptr);
```

### <a name="description"></a>Description

이 API는 서비스 변경 알림 콜백 함수를 지웁니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 콜백 함수를 성공적으로 지웠습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Clear the service notify. */

status = nx_mdns_service_notify_clear(&my_mdns);

/* If status is NX_SUCCESS, the service notify function clear. */
```

## <a name="nx_mdns_host_address_get"></a>nx_mdns_host_address_get

호스트 주소 가져오기

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_host_address_get(NX_MDNS *mdns_ptr,
    UCHAR *host_name, ULONG *ipv4_address,
    ULONG *ipv6_address, ULONG wait_option);
```

### <a name="description"></a>Description

이 서비스는 호스트 IPv4 및 IPv6 주소에 대한 mDNS 쿼리를 수행합니다. 지정된 호스트 이름의 주소가 피어 서비스 캐시에 있으면 주소가 반환됩니다. 피어 서비스 캐시에서 주소를 찾을 수 없는 경우 mDNS 모듈은 A 및 AAAA 형식 쿼리를 실행하고 응답을 기다립니다. 이 API는 응답이 수신되거나 쿼리 시간이 초과될 때까지 차단됩니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.
- **host_name** 호스트 이름에 대한 포인터입니다.
- **ipv4_address** IPv4 주소 스토리지 공간의 4바이트 정렬 주소에 대한 포인터입니다. 사용자는 IPv4 주소에 대해 4바이트의 공간을 할당해야 합니다. 애플리케이션에서 IPv4 주소를 검색할 필요가 없는 경우 NX_NULL 주소를 이 API에 전달할 수 있습니다.
- **ipv6_address** IPv6 주소 스토리지 공간의 4바이트 정렬 주소에 대한 포인터입니다. 사용자는 IPv6 주소에 대해 16바이트의 공간을 할당해야 합니다. 애플리케이션에서 IPv6 주소를 검색할 필요가 없는 경우 NX_NULL 주소를 이 API에 전달할 수 있습니다.
- **wait_option** 응답을 기다리는 시간(틱)입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 호스트 주소를 성공적으로 가져왔습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
ULONG ipv4_address;
ULONG ipv6_address[4];

/* Get the IP address of specified host. */
status = nx_mdns_host_address_get(&my_mdns, “MDNS-Host”, &ipv4_address, ipv6_address, 500);

/* If status is NX_SUCCESS, the IP address of specified host was retrieved. */
```

## <a name="nx_mdns_local_cache_clear"></a>nx_mdns_local_cache_clear

모든 로컬 서비스 지우기

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_local_cache_clear(NX_MDNS *mdns_ptr);
```

### <a name="description"></a>Description

이 서비스는 종료 메시지를 보낸 후 로컬 서비스 캐시에서 모든 항목을 지웁니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 캐시의 모든 항목을 성공적으로 지웠습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Clear the local cache, delete all local service. */

status = nx_mdns_local_cache_clear(&my_mdns);

/* If status is NX_SUCCESS, all services and resource records of local cache were deleted. */
```

## <a name="nx_mdns_peer_cache_clear"></a>nx_mdns_peer_cache_clear

검색된 모든 서비스 지우기

### <a name="prototype"></a>프로토타입

```C
UINT nx_mdns_peer_cache_clear(NX_MDNS *mdns_ptr);
```

### <a name="description"></a>Description

이 서비스는 피어 서비스 캐시의 모든 항목을 지웁니다.

### <a name="input-parameters"></a>입력 매개 변수

- **mdns_ptr** mDNS 제어 블록에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **NX_SUCCESS** (0x00) 캐시의 모든 항목을 성공적으로 지웠습니다.

### <a name="allowed-from"></a>허용된 원본

스레드

### <a name="example"></a>예제

```C
/* Clear the peer cache, delete all peer service. */

status = nx_mdns_peer_cache_clear(&my_mdns);

/* If status is NX_SUCCESS, all services and resource records of peer cache were deleted. */
```
