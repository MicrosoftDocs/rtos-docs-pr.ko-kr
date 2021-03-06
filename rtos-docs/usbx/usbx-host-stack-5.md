---
title: 5장 - USBX 호스트 클래스 API
description: USBX 호스트 클래스 API에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 5/19/2020
ms.service: rtos
ms.topic: article
ms.openlocfilehash: 62750ab4a5540b243665a7a7d9000a0d60c4435313b6de2e1579ae7f1c20fe55
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116790669"
---
# <a name="chapter-5---usbx-host-classes-api"></a>5장 - USBX 호스트 클래스 API

이 장에서는 USBX 호스트 클래스의 모든 노출된 API에 대해 설명합니다. 각 클래스를 위한 다음 API에 대해 자세히 설명합니다.

- HID 클래스
- CDC-ACM 클래스
- CDC-ECM 클래스
- 스토리지 클래스

## <a name="ux_host_class_hid_client_register"></a>ux_host_class_hid_client_register

HID 클라이언트를 HID 클래스에 등록합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_client_register(
    UCHAR *hid_client_name,
    UINT (*hid_client_handler)
    (struct UX_HOST_CLASS_HID_CLIENT_COMMAND_STRUCT *));
```

### <a name="description"></a>Description

이 함수는 HID 클라이언트를 HID 클래스에 등록하는 데 사용됩니다. HID 클래스는 이 디바이스에서 데이터를 요청하기 전에 HID 디바이스와 HID 클라이언트 간에 일치 항목을 찾아야 합니다.

> [!NOTE]
> hid_client_name의 C 문자열은 NULL로 종료되어야 하며 길이(NULL 종결자 제외)는 **UX_HOST_CLASS_HID_MAX_CLIENT_NAME_LENGTH** 보다 크지 않아야 합니다.

### <a name="parameters"></a>매개 변수

- **hid_client_name** HID 클라이언트 이름에 대한 포인터입니다.
- **hid_client_handler** HID 클라이언트 처리기에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 데이터 전송이 완료되었습니다.
- **UX_MEMORY_INSUFFICIENT**(0x12) 클라이언트를 위한 메모리 할당에 실패했습니다.
- **UX_MEMORY_ARRAY_FULL**(0x1a) 최대 클라이언트가 이미 등록되어 있습니다.
- **UX_HOST_CLASS_ALREADY_INSTALLED**(0x58) 이 클래스는 이미 있습니다.

### <a name="example"></a>예제

```c
UINT status;

/* The following example illustrates how to register a HID client, in
this case a USB mouse, to the HID class. */

status = ux_host_class_hid_client_register("ux_host_class_hid_client_mouse",
    ux_host_class_hid_mouse_entry);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_report_callback_register"></a>ux_host_class_hid_report_callback_register

HID 클래스에서 콜백을 등록합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_report_callback_register(
    UX_HOST_CLASS_HID *hid,
    UX_HOST_CLASS_HID_REPORT_CALLBACK *call_back);
```

### <a name="description"></a>Description

이 함수는 보고서를 받을 때 HID 클래스에서 HID 클라이언트로 콜백을 등록하는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

- **hid** HID 클래스 인스턴스에 대한 포인터입니다.
- **call_back** call_back 구조체에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 데이터 전송이 완료되었습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) 잘못된 HID 인스턴스입니다.
- **UX_HOST_CLASS_HID_REPORT_ERROR**(0x79) 보고서 콜백 등록에 오류가 있습니다.

### <a name="example"></a>예제

```c
UINT status;

/* This example illustrates how to register a HID client, in this case a USB mouse, to the HID class. In this case, the HID client is asking the HID class to call the client for each usage received in the HID report. */

call_back.ux_host_class_hid_report_callback_id = 0;
call_back.ux_host_class_hid_report_callback_function = ux_host_class_hid_mouse_callback;

call_back.ux_host_class_hid_report_callback_buffer = UX_NULL;
call_back.ux_host_class_hid_report_callback_flags = UX_HOST_CLASS_HID_REPORT_INDIVIDUAL_USAGE;

call_back.ux_host_class_hid_report_callback_length = 0;

status = ux_host_class_hid_report_callback_register(hid, &call_back);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_periodic_report_start"></a>ux_host_class_hid_periodic_report_start

HID 클래스 인스턴스의 주기적 엔드포인트를 시작합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_periodic_report_start(UX_HOST_CLASS_HID *hid);
```

### <a name="description"></a>Description

이 함수는 이 HID 클라이언트에 바인딩되는 HID 클래스의 인스턴스에 대한 주기적(인터럽트) 엔드포인트를 시작하는 데 사용됩니다. HID 클래스는 HID 클라이언트가 활성화될 때까지 주기적 엔드포인트를 시작할 수 없으므로 보고서를 받기 위해 엔드포인트를 시작하는 것은 HID 클라이언트에게 맡겨집니다.

### <a name="input-parameter"></a>입력 매개 변수

- **hid** HID 클래스 인스턴스에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS** (0x00) 주기적 보고를 성공적으로 시작했습니다.
- **ux_host_class_hid_PERIODIC_REPORT_ERROR**(0x7A) 주기적 보고서에 오류가 있습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.

### <a name="example"></a>예제

```c
UINT status;

/* The following example illustrates how to start the periodic
endpoint. */

status = ux_host_class_hid_periodic_report_start(hid);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_periodic_report_stop"></a>ux_host_class_hid_periodic_report_stop

HID 클래스 인스턴스의 주기적 엔드포인트를 중지합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_periodic_report_stop(UX_HOST_CLASS_HID *hid);
```

### <a name="description"></a>Description

이 함수는 이 HID 클라이언트에 바인딩되는 HID 클래스의 인스턴스에 대한 주기적(인터럽트) 엔드포인트를 중지하는 데 사용됩니다. HID 클래스는 HID 클라이언트가 비활성화될 때까지 주기적 엔드포인트를 중지할 수 없으며, 모든 리소스가 해제되므로 이 엔드포인트를 중지하는 것은 HID 클라이언트에 맡겨집니다.

### <a name="input-parameter"></a>입력 매개 변수

- **hid** HID 클래스 인스턴스에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 주기적 보고를 성공적으로 중지했습니다.
- **ux_host_class_hid_PERIODIC_REPORT_ERROR**(0x7A) 주기적 보고서에 오류가 있습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.

### <a name="example"></a>예제

```c
UINT status;

/* The following example illustrates how to stop the periodic endpoint. */

status = ux_host_class_hid_periodic_report_stop(hid);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_report_get"></a>ux_host_class_hid_report_get

HID 클래스 인스턴스에서 보고서를 가져옵니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_report_get(
    UX_HOST_CLASS_HID *hid,
    UX_HOST_CLASS_HID_CLIENT_REPORT *client_report);
```

### <a name="description"></a>Description

이 함수는 주기적 엔드포인트에 의존하지 않고 디바이스에서 직접 보고서를 수신하는 데 사용됩니다. 이 보고서는 제어 엔드포인트에서 제공되지만 처리는 주기적 엔드포인트에서 제공되는 것과 동일합니다.

### <a name="parameters"></a>매개 변수

- **hid** HID 클래스 인스턴스에 대한 포인터입니다.
- **client_report** HID 클라이언트 보고서에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 보고서가 성공적으로 수신되었습니다.
- **UX_HOST_CLASS_HID_REPORT_ERROR**(0x70) 클라이언트 보고서가 유효하지 않거나 전송 중에 오류가 발생했습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.
- **UX_BUFFER_OVERFLOW**(0x5d) 제공된 버퍼가 압축되지 않은 보고서를 수용할 만큼 크지 않습니다.

### <a name="example"></a>예제

```c
UX_HOST_CLASS_HID_CLIENT_REPORT input_report;

UINT status;

/* The following example illustrates how to get a report. */

input_report.ux_host_class_hid_client_report = hid_report;
input_report.ux_host_class_hid_client_report_buffer = buffer;
input_report.ux_host_class_hid_client_report_length = length;
input_report.ux_host_class_hid_client_flags = UX_HOST_CLASS_HID_REPORT_INDIVIDUAL_USAGE;

status = ux_host_class_hid_report_get(hid, &input_report);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_report_set"></a>ux_host_class_hid_report_set

보고서 보내기

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_report_set(
    UX_HOST_CLASS_HID *hid,
    UX_HOST_CLASS_HID_CLIENT_REPORT *client_report);
```

### <a name="description"></a>Description

이 함수는 보고서를 디바이스에 직접 보내는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

- **hid** HID 클래스 인스턴스에 대한 포인터입니다.
- **client_report** HID 클라이언트 보고서에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 보고서가 성공적으로 전송되었습니다.
- **UX_HOST_CLASS_HID_REPORT_ERROR**(0x70) 클라이언트 보고서가 유효하지 않거나 전송 중에 오류가 발생했습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.
- **UX_HOST_CLASS_HID_REPORT_OVERFLOW**(0x5d) 제공된 버퍼가 압축되지 않은 보고서를 수용할 만큼 크지 않습니다.

### <a name="example"></a>예제

```c
/* The following example illustrates how to send a report. */

UX_HOST_CLASS_HID_CLIENT_REPORT input_report;

input_report.ux_host_class_hid_client_report = hid_report;
input_report.ux_host_class_hid_client_report_buffer = buffer;
input_report.ux_host_class_hid_client_report_length = length;
input_report.ux_host_class_hid_client_report_flags = UX_HOST_CLASS_HID_REPORT_INDIVIDUAL_USAGE;

status = ux_host_class_hid_report_set(hid, &input_report);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_mouse_buttons_get"></a>ux_host_class_hid_mouse_buttons_get

마우스 단추를 가져옵니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_mouse_buttons_get(
    UX_HOST_CLASS_HID_MOUSE *mouse_instance,
    ULONG *mouse_buttons);
```

### <a name="description"></a>Description

이 함수는 마우스 단추를 가져오는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

- **mouse_instance** HID 마우스 인스턴스에 대한 포인터입니다.
- **mouse_buttons** 반환 단추에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 마우스 단추를 성공적으로 검색했습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.

### <a name="example"></a>예제

```c
/* The following example illustrates how to obtain mouse buttons. */

UX_HOST_CLASS_HID_MOUSE *mouse_instance;

ULONG mouse_buttons;

status = ux_host_class_hid_mouse_button_get(mouse_instance, &mouse_buttons);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_mouse_position_get"></a>ux_host_class_hid_mouse_position_get

마우스 위치를 가져옵니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_mouse_position_get(
    UX_HOST_CLASS_HID_MOUSE *mouse_instance,
    SLONG *mouse_x_position, 
    SLONG *mouse_y_position);
```

### <a name="description"></a>Description

이 함수는 x 및 y 좌표에서 마우스 위치를 가져오는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

- **mouse_instance** HID 마우스 인스턴스에 대한 포인터입니다.
- **mouse_x_position** X 좌표에 대한 포인터입니다.
- **mouse_y_position** Y 좌표에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) X &amp; Y 좌표를 성공적으로 검색했습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.

### <a name="example"></a>예제

```c
/* The following example illustrates how to obtain mouse coordinates. */

UX_HOST_CLASS_HID_MOUSE *mouse_instance;

SLONG mouse_x_position;
SLONG mouse_y_position;

status = ux_host_class_hid_mouse_position_get(mouse_instance,
    &mouse_x_position, &mouse_y_position);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_keyboard_key_get"></a>ux_host_class_hid_keyboard_key_get

키보드 키 및 상태를 가져옵니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_keyboard_key_get(
    UX_HOST_CLASS_HID_KEYBOARD *keyboard_instance,
    ULONG *keyboard_key, 
    ULONG *keyboard_state);
```

### <a name="description"></a>Description

이 함수는 키보드 키와 상태를 가져오는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

- **keyboard_instance** HID 키보드 인스턴스에 대한 포인터입니다.
- **keyboard_key** 키보드 키 컨테이너에 대한 포인터입니다.
- **keyboard_state** 키보드 상태 컨테이너에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 키와 상태를 성공적으로 검색했습니다.
- **UX_ERROR**(0xff) 보고할 내용이 없습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.

키보드 상태에 사용할 수 있는 값은 다음과 같습니다.

- **UX_HID_KEYBOARD_STATE_KEY_UP** 0x10000
- **UX_HID_KEYBOARD_STATE_NUM_LOCK** 0x0001
- **UX_HID_KEYBOARD_STATE_CAPS_LOCK** 0x0002
- **UX_HID_KEYBOARD_STATE_SCROLL_LOCK** 0x0004
- **UX_HID_KEYBOARD_STATE_MASK_LOCK** 0x0007
- **UX_HID_KEYBOARD_STATE_LEFT_SHIFT** 0x0100
- **UX_HID_KEYBOARD_STATE_RIGHT_SHIFT** 0x0200
- **UX_HID_KEYBOARD_STATE_SHIFT** 0x0300
- **UX_HID_KEYBOARD_STATE_LEFT_ALT** 0x0400
- **UX_HID_KEYBOARD_STATE_RIGHT_ALT** 0x0800
- **UX_HID_KEYBOARD_STATE_ALT** 0x0a00
- **UX_HID_KEYBOARD_STATE_LEFT_CTRL** 0x1000
- **UX_HID_KEYBOARD_STATE_RIGHT_CTRL** 0x2000
- **UX_HID_KEYBOARD_STATE_CTRL** 0x3000
- **UX_HID_KEYBOARD_STATE_LEFT_GUI** 0x4000
- **UX_HID_KEYBOARD_STATE_RIGHT_GUI** 0x8000
- **UX_HID_KEYBOARD_STATE_GUI** 0xa000

### <a name="example"></a>예제

```c
while (1)
{

    /* Get a key/state from the keyboard. */
    status = ux_host_class_hid_keyboard_key_get(keyboard,
        &keyboard_char, &keyboard_state);

    /* Check if there is something. */
    if (status == UX_SUCCESS)
    {

        #ifdef UX_HOST_CLASS_HID_KEYBOARD_EVENTS_KEY_CHANGES_MODE
            if (keyboard_state & UX_HID_KEYBOARD_STATE_KEY_UP)
            {
                /* The key was released. */
            } else
            {
                /* The key was pressed. */
            }
        #endif

        /* We have a character in the queue. */
        keyboard_queue[keyboard_queue_index] = (UCHAR) keyboard_char;

        /* Can we accept more ? */
        if(keyboard_queue_index < 1024)
            keyboard_queue_index++;
    }

    tx_thread_sleep(10);

}
```

## <a name="ux_host_class_hid_keyboard_ioctl"></a>ux_host_class_hid_keyboard_ioctl

HID 키보드에 대해 IOCTL 함수를 수행합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_keyboard_ioctl(
    UX_HOST_CLASS_HID_KEYBOARD *keyboard_instance,
    ULONG ioctl_function, VOID *parameter);
```

### <a name="description"></a>Description

이 함수는 HID 키보드에 대해 특정 ioctl 함수를 수행합니다. 호출이 차단되고 오류가 있거나 명령이 완료된 경우에만 반환됩니다.

### <a name="parameters"></a>매개 변수

- **keyboard_instance** HID 키보드 인스턴스에 대한 포인터입니다.
- **ioctl_function** 수행할 ioctl 함수입니다. 허용되는 ioctl 함수 중 하나에 대해서는 아래 표를 참조하세요.
- **parameter** ioctl에 특정한 매개 변수에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) ioctl 함수가 성공적으로 완료되었습니다.
- **UX_FUNCTION_NOT_SUPPORTED**(0x54) 알 수 없는 IOCTL 함수입니다.

### <a name="ioctl-functions"></a>IOCTL 함수

- UX_HID_KEYBOARD_IOCTL_SET_LAYOUT
- UX_HID_KEYBOARD_IOCTL_KEY_DECODING_ENABLE
- UX_HID_KEYBOARD_IOCTL_KEY_DECODING_DISABLE

### <a name="example--change-keyboard-layout"></a>예 – 키보드 레이아웃 변경

```c
UINT status;

/* This example shows usage of the SET_LAYOUT IOCTL function.
    USBX receives raw key values from the device (these raw values
    are defined in the HID usage table specification) and optionally
    decodes them for application usage. The decoding is performed
    based on a set of arrays that act as maps – which array is used
    depends on the raw key value (i.e. keypad and non-keypad) and
    the current state of the keyboard (i.e. shift, caps lock, etc.). */

/* When the shift condition is not present and the raw key value
    is not within the keypad value range, this array will be used to decode the raw key value. */

static UCHAR keyboard_layout_raw_to_unshifted_map[] =
{
    0,0,0,0,
    'a','b','c','d','e','f','g',
    'h','i','j','k','l','m','n',
    'o','p','q','r','s','t',
    'u','v','w','x','y','z',
    '1','2','3','4','5','6','7','8','9','0',
    0x0d,0x1b,0x08,0x07,0x20,'-','=','[',']',
    '\\','#',';',0x27,'`',',','.','/',0xf0,
    0xbb,0xbc,0xbd,0xbe,0xbf,0xc0,0xc1,0xc2,0xc3,0xc4,0xc5,0xc6,
    0x00,0xf1,0x00,0xd2,0xc7,0xc9,0xd3,0xcf,0xd1,0xcd,0xcd,0xd0,0xc8,0xf2,
    '/','*','-','+',
    0x0d,'1','2','3','4','5','6','7','8','9','0','.','\\',0x00,0x00,'=',
    0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
};

/* When the shift condition is present and the raw key value
    is not within the keypad value range, this array will be used to decode the raw key value. */

static UCHAR keyboard_layout_raw_to_shifted_map[] =
{
    0,0,0,0,
    'A','B','C','D','E','F','G',
    'H','I','J','K','L','M','N',
    'O','P','Q','R','S','T',
    'U','V','W','X','Y','Z',
    '!','@','#','$','%','^','&','*','(',')',
    0x0d,0x1b,0x08,0x07,0x20,'_','+','{','}',
    '|','~',':','"','~','<','>','?',0xf0,
    0xbb,0xbc,0xbd,0xbe,0xbf,0xc0,0xc1,0xc2,0xc3,0xc4,0xc5,0xc6,
    0x00,0xf1,0x00,0xd2,0xc7,0xc9,0xd3,0xcf,0xd1,0xcd,0xcd,0xd0,0xc8,0xf2,
    '/','*','-','+',
    0x0d,'1','2','3','4','5','6','7','8','9','0','.','\\',0x00,0x00,'=',
    0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,0x00,
};

/* When numlock is on and the raw key value is within the keypad
    value range, this array will be used to decode the raw key value. */

static UCHAR keyboard_layout_raw_to_numlock_on_map[] =
{
    '/','*','-','+',
    0x0d,'1','2','3','4','5','6','7','8','9','0','.','\\',0x00,0x00,'=',
};

/* When numlock is off and the raw key value is within the keypad value
    range, this array will be used to decode the raw key value. */
static UCHAR keyboard_layout_raw_to_numlock_off_map[] =
{
    '/','*','-','+',
    0x0d,0xcf,0xd0,0xd1,0xcb,'5',0xcd,0xc7,0xc8,0xc9,0xd2,0xd3,'\\',0x00,0x
    00,'=',
};

/* Specify the keyboard layout for USBX usage. */
static UX_HOST_CLASS_HID_KEYBOARD_LAYOUT keyboard_layout =
{
    keyboard_layout_raw_to_shifted_map,
    keyboard_layout_raw_to_unshifted_map,
    keyboard_layout_raw_to_numlock_on_map,
    keyboard_layout_raw_to_numlock_off_map,
    /* The maximum raw key value. Values larger than this are discarded. */
    UX_HID_KEYBOARD_KEYS_UPPER_RANGE,
    /* The raw key value for the letter 'a'. */
    UX_HID_KEYBOARD_KEY_LETTER_A,
    /* The raw key value for the letter 'z'. */
    UX_HID_KEYBOARD_KEY_LETTER_Z,
    /* The lower range raw key value for keypad keys - inclusive. */
    UX_HID_KEYBOARD_KEYS_KEYPAD_LOWER_RANGE,
    /* The upper range raw key value for keypad keys. */
    UX_HID_KEYBOARD_KEYS_KEYPAD_UPPER_RANGE
};

/* Call the IOCTL function to change the keyboard layout. */
status = ux_host_class_hid_keyboard_ioctl(keyboard,
    UX_HID_KEYBOARD_IOCTL_SET_LAYOUT, (VOID *)&keyboard_layout);

/* If status equals UX_SUCCESS, the operation was successful. */
```

### <a name="example--disable-keyboard-key-decode"></a>예 – 키보드 키 디코드 사용 안 함

```c
UINT status;

/* The following example illustrates IOCTL function of Disable key decode from keyboard layout. */
status = ux_host_class_hid_keyboard_ioctl(keyboard,
    UX_HID_KEYBOARD_IOCTL_DISABLE_KEYS_DECODE, UX_NULL);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_hid_remote_control_usage_get"></a>ux_host_class_hid_remote_control_usage_get

원격 제어 사용을 가져옵니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_hid_remote_control_usage_get(
    UX_HOST_CLASS_HID_REMOTE_CONTROL *remote_control_instance,
    LONG *usage, 
    ULONG *value);
```

### <a name="description"></a>Description

이 함수는 원격 제어 사용을 가져오는 데 사용됩니다.

### <a name="parameters"></a>매개 변수

- **remote_control_instance** HID 원격 제어 인스턴스에 대한 포인터입니다.
- **usage** 사용에 대한 포인터입니다.
- **value** 사용을 위한 값에 대한 포인터입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 데이터 전송이 완료되었습니다.
- **UX_ERROR**(0xff) 보고할 내용이 없습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) HID 클래스 인스턴스가 없습니다.

가능한 모든 사용의 목록이 너무 길어서 이 사용자 가이드에 맞지 않습니다. 전체 설명을 위해 ux_host_class_hid.h에는 가능한 값의 전체 집합이 있습니다.

### <a name="example"></a>예제

```c
/* Read usages and values as the user changes the vol/bass/treble buttons on the speaker */

while (remote_control != UX_NULL)
{
    status = ux_host_class_hid_remote_control_usage_get(remote_control, &usage, &value);
    if (status == UX_SUCCESS)
    {
        /* We have something coming from the HID remote control,
        we filter the usage here and only allow the
        volume usage which can be VOLUME, VOLUME_INCREMENT or VOLUME_DECREMENT */
        switch(usage)
        {
            case UX_HOST_CLASS_HID_CONSUMER_VOLUME :
            case UX_HOST_CLASS_HID_CONSUMER_VOLUME_INCREMENT :
            case UX_HOST_CLASS_HID_CONSUMER_VOLUME_DECREMENT :

            if (value<0x80)
            {
                if (current_volume + audio_control.ux_host_class_audio_control_res < 0xffff)
                    current_volume = current_volume + audio_control.ux_host_class_audio_control_res;
                } else {
                if (current_volume > audio_control.ux_host_class_audio_control_res)
                    current_volume = current_volumeaudio_control.ux_host_class_audio_control_res;
            }

            audio_control.ux_host_class_audio_control_channel = 1;
            audio_control.ux_host_class_audio_control = UX_HOST_CLASS_AUDIO_VOLUME_CONTROL;
            audio_control.ux_host_class_audio_control_cur = current_volume;
            status = ux_host_class_audio_control_value_set(audio, &audio_control);
            audio_control.ux_host_class_audio_control_channel = 2;
            audio_control.ux_host_class_audio_control = UX_HOST_CLASS_AUDIO_VOLUME_CONTROL;
            audio_control.ux_host_class_audio_control_cur = current_volume;
            status = ux_host_class_audio_control_value_set(audio, &audio_control);
            break;

        }
    }
    tx_thread_sleep(10);

}
```

### <a name="ux_host_class_cdc_acm_read"></a>ux_host_class_cdc_acm_read

cdc_acm 인터페이스에서 읽습니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_cdc_acm_read(
    UX_HOST_CLASS_CDC_ACM *cdc_acm,
    UCHAR *data_pointer,
    ULONG requested_length,
    ULONG *actual_length);
```

### <a name="description"></a>Description

이 함수는 cdc_acm 인터페이스에서 읽습니다. 호출이 차단되고 오류가 있거나 전송이 완료된 경우에만 반환됩니다.

> [!Note]
> 이 함수는 디바이스에서 원시 대량 데이터를 읽으므로 버퍼가 가득 차거나 디바이스가 짧은 패킷(Zero Length Packet 포함)으로 전송을 종료할 때까지 보류 상태를 유지합니다. 자세한 내용은 [**대량 전송에 대한 일반적인 고려 사항**](usbx-device-stack-5.md#general-considerations-for-bulk-transfer)을 참조하세요.

### <a name="parameters"></a>매개 변수

- **cdc_acm** cdc_acm 클래스 인스턴스에 대한 포인터입니다.
- **data_pointer** 데이터 페이로드의 버퍼 주소에 대한 포인터입니다.
- **requested_length** 수신될 길이입니다.
- **actual_length** 실제로 수신된 길이입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 데이터 전송이 완료되었습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) cdc_acm 인스턴스가 유효하지 않습니다.
- **UX_TRANSFER_TIMEOUT**(0x5c) 전송 제한 시간이 초과되었습니다. 읽기가 완료되지 않았습니다.

### <a name="example"></a>예제

```c
UINT status;

/* The following example illustrates this service. */

status = ux_host_class_cdc_acm_read(cdc_acm, data_pointer,
    requested_length, &actual_length);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_cdc_acm_write"></a>ux_host_class_cdc_acm_write

cdc_acm 인터페이스에 씁니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_cdc_acm_write(
    UX_HOST_CLASS_CDC_ACM *cdc_acm,
    UCHAR *data_pointer, 
    ULONG requested_length, 
    ULONG *actual_length);
```

### <a name="description"></a>Description

이 함수는 cdc_acm 인터페이스에 씁니다. 호출이 차단되고 오류가 있거나 전송이 완료된 경우에만 반환됩니다.

### <a name="parameters"></a>매개 변수

- **cdc_acm** cdc_acm 클래스 인스턴스에 대한 포인터입니다.
- **data_pointer** 데이터 페이로드의 버퍼 주소에 대한 포인터입니다.
- **requested_length** 전송될 길이입니다.
- **actual_length** 실제로 전송된 길이입니다.

### <a name="return-values"></a>반환 값

- **UX_SUCCESS**(0x00) 데이터 전송이 완료되었습니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) cdc_acm 인스턴스가 유효하지 않습니다.
- **UX_TRANSFER_TIMEOUT**(0x5c) 전송 제한 시간이 초과되었습니다. 쓰기가 완료되지 않았습니다.

### <a name="example"></a>예제

```c
UINT status;

/* The following example illustrates this service. */

status = ux_host_class_cdc_acm_write(cdc_acm, data_pointer,
    requested_length, &actual_length);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_cdc_acm_ioctl"></a>ux_host_class_cdc_acm_ioctl

cdc_acm 인터페이스에 대한 IOCTL 함수를 수행합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_cdc_acm_ioctl(
    UX_HOST_CLASS_CDC_ACM *cdc_acm,
    ULONG ioctl_function, 
    VOID *parameter);
```

### <a name="description"></a>Description

이 함수는 cdc_acm 인터페이스에 대한 특정 ioctl 함수를 수행합니다. 호출이 차단되고 오류가 있거나 명령이 완료된 경우에만 반환됩니다.

### <a name="parameters"></a>매개 변수

- **cdc_acm** cdc_acm 클래스 인스턴스에 대한 포인터입니다.
- **ioctl_function** 수행할 ioctl 함수입니다. 허용되는 ioctl 함수 중 하나에 대해서는 아래 표를 참조하세요.
- **parameter** ioctl 호출과 관련된 매개 변수에 대한 포인터

### <a name="return-value"></a>반환 값

- **UX_SUCCESS**(0x00) 데이터 전송이 완료되었습니다.
- **UX_MEMORY_INSUFFICIENT**(0x12) 메모리가 부족합니다.
- **UX_HOST_CLASS_INSTANCE_UNKNOWN**(0x5b) CDC-ACM 인스턴스가 유효하지 않은 상태입니다.
- **UX_FUNCTION_NOT_SUPPORTED**(0x54) 알 수 없는 IOCTL 함수입니다.

### <a name="ioctl-functions"></a>IOCTL 함수:

- UX_HOST_CLASS_CDC_ACM_IOCTL_SET_LINE_CODING
- UX_HOST_CLASS_CDC_ACM_IOCTL_GET_LINE_CODING
- UX_HOST_CLASS_CDC_ACM_IOCTL_SET_LINE_STATE
- UX_HOST_CLASS_CDC_ACM_IOCTL_SEND_BREAK
- UX_HOST_CLASS_CDC_ACM_IOCTL_ABORT_IN_PIPE
- UX_HOST_CLASS_CDC_ACM_IOCTL_ABORT_OUT_PIPE
- UX_HOST_CLASS_CDC_ACM_IOCTL_NOTIFICATION_CALLBACK
- UX_HOST_CLASS_CDC_ACM_IOCTL_GET_DEVICE_STATUS

```c
UINT status;

/* The following example illustrates this service. */

status = ux_host_class_cdc_acm_ioctl(cdc_acm,
    UX_HOST_CLASS_CDC_ACM_IOCTL_GET_LINE_CODING, (VOID *)&line_coding);

/* If status equals UX_SUCCESS, the operation was successful. */
```

## <a name="ux_host_class_cdc_acm_reception_start"></a>ux_host_class_cdc_acm_reception_start

디바이스에서 데이터의 백그라운드 수신을 시작합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_cdc_acm_reception_start(
    UX_HOST_CLASS_CDC_ACM *cdc_acm,
    UX_HOST_CLASS_CDC_ACM_RECEPTION *cdc_acm_reception);
```

### <a name="description"></a>Description

이 함수는 USBX가 백그라운드에서 계속해서 디바이스에서 데이터를 읽도록 합니다. 각 트랜잭션이 완료되면 애플리케이션에서 트랜잭션 데이터의 추가 처리를 수행할 수 있도록 **cdc_acm_reception** 에 지정된 콜백을 호출합니다.

> [!NOTE]
> 백그라운드 수신을 사용하는 동안에는 **ux_host_class_cdc_acm_read** 를 사용하지 않아야 합니다.

### <a name="parameters"></a>매개 변수

- **cdc_acm** cdc_acm 클래스 인스턴스에 대한 포인터입니다.
- **cdc_acm_reception** 백그라운드 수신 동작을 정의하는 값을 포함하는 매개 변수에 대한 포인터입니다. 이 매개 변수의 레이아웃은 다음과 같습니다.

```c
typedef struct UX_HOST_CLASS_CDC_ACM_RECEPTION_STRUCT
{
    ULONG ux_host_class_cdc_acm_reception_state;
    ULONG ux_host_class_cdc_acm_reception_block_size;
    UCHAR *ux_host_class_cdc_acm_reception_data_buffer;
    ULONG ux_host_class_cdc_acm_reception_data_buffer_size;
    UCHAR *ux_host_class_cdc_acm_reception_data_head;
    UCHAR *ux_host_class_cdc_acm_reception_data_tail;
    VOID (*ux_host_class_cdc_acm_reception_callback)(struct UX_HOST_CLASS_CDC_ACM_STRUCT *cdc_acm,
        UINT status, UCHAR *reception_buffer, ULONG reception_size);
} UX_HOST_CLASS_CDC_ACM_RECEPTION;
```

### <a name="return-value"></a>반환 값

- **UX_SUCCESS**(0x00) 백그라운드 수신을 성공적으로 시작했습니다.
- **UX_HOST_CLASS_INSTANCE _UNKNOWN**(0x5b) 잘못된 클래스 인스턴스입니다.

```c
UINT status;
UX_HOST_CLASS_CDC_ACM_RECEPTION cdc_acm_reception;

/* Setup the background reception parameter. */

/* Set the desired max read size for each transaction.
    For example, if this value is 64, then the maximum amount of
    data received from the device in a single transaction is 64.
    If the amount of data received from the device is less than this value,
    the callback will still be invoked with the actual amount of data received. */
cdc_acm_reception.ux_host_class_cdc_acm_reception_block_size = block_size;

/* Set the buffer where the data from the device is read to. */
cdc_acm_reception.ux_host_class_cdc_acm_reception_data_buffer = cdc_acm_reception_buffer;

/* Set the size of the data reception buffer.
    Note that this should be at least as large as ux_host_class_cdc_acm_reception_block_size. */
cdc_acm_reception.ux_host_class_cdc_acm_reception_data_buffer_size = cdc_acm_reception_buffer_size;

/* Set the callback that is to be invoked upon each reception transfer completion. */
cdc_acm_reception.ux_host_class_cdc_acm_reception_callback = reception_callback;

/* Start background reception using the values we defined in the reception parameter. */
status = ux_host_class_cdc_acm_reception_start(cdc_acm_host_data, &cdc_acm_reception);

/* If status equals UX_SUCCESS, background reception has successfully started. */
```

## <a name="ux_host_class_cdc_acm_reception_stop"></a>ux_host_class_cdc_acm_reception_stop

패킷의 백그라운드 수신을 중지합니다.

### <a name="prototype"></a>프로토타입

```c
UINT ux_host_class_cdc_acm_reception_stop(
    UX_HOST_CLASS_CDC_ACM *cdc_acm,
    UX_HOST_CLASS_CDC_ACM_RECEPTION *cdc_acm_reception);
```

### <a name="description"></a>Description

이 함수를 통해 USBX가 이전에 **ux_host_class_cdc_acm_reception_start** 에서 시작한 백그라운드 수신을 중지도록 합니다.

### <a name="parameters"></a>매개 변수

- **cdc_acm** cdc_acm 클래스 인스턴스에 대한 포인터입니다.
- **cdc_acm_reception** 백그라운드 수신을 시작하는 데 사용된 것과 동일한 매개 변수에 대한 포인터입니다. 이 매개 변수의 레이아웃은 다음과 같습니다.

```c
typedef struct UX_HOST_CLASS_CDC_ACM_RECEPTION_STRUCT
{
    ULONG ux_host_class_cdc_acm_reception_state;
    ULONG ux_host_class_cdc_acm_reception_block_size;
    UCHAR *ux_host_class_cdc_acm_reception_data_buffer;
    ULONG ux_host_class_cdc_acm_reception_data_buffer_size;
    UCHAR *ux_host_class_cdc_acm_reception_data_head;
    UCHAR *ux_host_class_cdc_acm_reception_data_tail;
    VOID (*ux_host_class_cdc_acm_reception_callback)(
            struct UX_HOST_CLASS_CDC_ACM_STRUCT *cdc_acm, UINT status,
            UCHAR *reception_buffer, ULONG reception_size);
} UX_HOST_CLASS_CDC_ACM_RECEPTION;
```

### <a name="return-value"></a>반환 값

- **UX_SUCCESS**(0x00) 백그라운드 수신을 성공적으로 중지했습니다.
- **UX_HOST_CLASS_INSTANCE _UNKNOWN**(0x5b) 잘못된 클래스 인스턴스입니다.

```c
UINT status;
UX_HOST_CLASS_CDC_ACM_RECEPTION cdc_acm_reception;

/* Stop background reception. The reception parameter should be the same
    that was passed to ux_host_class_cdc_acm_reception_start. */
status = ux_host_class_cdc_acm_reception_stop(cdc_acm, &cdc_acm_reception);

/* If status equals UX_SUCCESS, background reception has successfully stopped. */
```
