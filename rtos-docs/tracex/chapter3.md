---
title: 챕터 3 - Azure RTOS TraceX 설명
description: 이 챕터에서는 GUI의 전체 기능을 포함하여 Azure RTOS TraceX 시스템 분석 도구의 전체 기능에 대해 설명합니다.
author: philmea
ms.service: rtos
ms.topic: article
ms.date: 5/19/2020
ms.author: philmea
ms.openlocfilehash: bb466427374659027bf91c7bb46c74e7d2ff561d200db9dab1a2bddbe6635ef4
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116789537"
---
# <a name="chapter-3---description-of-azure-rtos-tracex"></a>챕터 3 - Azure RTOS TraceX 설명

이 챕터에서는 GUI의 전체 기능을 포함하여 Azure RTOS TraceX 시스템 분석 도구의 전체 기능에 대해 설명합니다. 

## <a name="display-overview"></a>표시 개요

**그림 4** 에서는 TraceX 시스템 분석 도구의 기본 표시 창을 보여 줍니다. 레이아웃은 간단합니다. 실행 컨텍스트는 왼쪽의 세로 요소(예: 초기화, 인터럽트, 유휴 및 다양한 스레드 항목)로 표시됩니다. 각 컨텍스트에서 발생하는 이벤트는 동일한 컨텍스트 줄에 가로로 표시됩니다. 예를 들어 아래에 표시된 **QR** 이벤트는 **_스레드 2_ *_에서 _* _tx_queue_receive_** 를 연속적으로 호출하고 있음을 보여 줍니다.

![TraceX 시스템 분석 도구의 기본 표시 창에 대한 스크린샷](./media/user-guide/screen_shot_10.png)


**그림 5**

컨텍스트 변경은 컨텍스트 줄을 연결하는 검은색 세로 선으로 표시됩니다. 현재 선택한 이벤트는 빨간색 세로 실선으로 표시됩니다. 이 예에서는 494 이벤트가 선택됩니다.

## <a name="title-bar"></a>제목 표시줄

TraceX 제목 표시줄은 몇 가지 유용한 정보를 제공합니다. 첫 번째는 TraceX의 현재 버전입니다. 두 번째는 현재 열려 있는 추적 파일의 전체 경로입니다. **그림 6** 의 예에서는 **_TraceX_ *버전 _* _6.0.0_ *_ 에서 _* _demo_threadx.trx_** 추적 파일을 표시하고 있음을 보여 줍니다.

![TraceX 제목 표시줄의 스크린샷](./media/user-guide/screen_shot_11.png)

**그림 6**

## <a name="tool-bar"></a>Tool Bar

TraceX 도구 모음은 추적 파일을 열고 해당 표시 요소를 제어하는 몇 가지 단추를 제공합니다.

![TraceX 도구 모음의 스크린샷](./media/user-guide/screen_shot_12.png)


**그림 7**

TraceX 도구 모음 단추(왼쪽에서 오른쪽으로)는 다음과 같이 정의됩니다.
                                             
| **Button**                         | **Function** |
| ---------------------------------- | ----------------------------------------------------------------------------------------------- |
| ![추적 파일 열기 단추](./media/user-guide/screen_shot_13.png)      | 추적 파일을 엽니다. |
| ![사용자 가이드 열기 단추](./media/user-guide/screen_shot_14.png)      | 해당 사용자 가이드를 엽니다. |
| ![실행 프로필 생성 단추](./media/user-guide/screen_shot_15.png)       | 실행 프로필을 생성합니다. |
| ![ 성능 통계 생성 단추](./media/user-guide/screen_shot_16.png)       | 성능 통계를 생성합니다. |
| ![스레드 스택 사용량 생성 단추](./media/user-guide/screen_shot_17.png)       | 스레드 스택 사용량을 생성합니다. |
| ![선택한 이벤트 표시 단추](./media/user-guide/screen_shot_18.png)       | 현재 선택한 이벤트를 표시합니다. |
| ![검색 단추](./media/user-guide/screen_shot_19.png)      | 이벤트를 검색합니다. |
| ![확대 단추](./media/user-guide/screen_shot_20.png)      | 확대 |
| ![표시 확대/축소 단추](./media/user-guide/screen_shot_21.png)      | 표시 확대/축소 비율을 선택합니다. 여기서 100%는 전체 추적 파일이 현재 보기 내에 표시됨을 의미합니다. |
| ![축소 단추](./media/user-guide/screen_shot_22.png)      | 축소 |
| ![첫 번째 이벤트 선택 단추](./media/user-guide/screen_shot_23.png)      | 첫 번째 이벤트를 선택합니다. |
| ![이전 이벤트 페이지 표시 단추](./media/user-guide/screen_shot_24.png)      | 이전 이벤트 페이지를 표시합니다. |
| ![이전 이벤트 표시 단추](./media/user-guide/screen_shot_25.png)      | 이전 이벤트를 표시합니다. |
| ![다음/이전 탐색 단추](./media/user-guide/screen_shot_26.png)      | 다음/이전 탐색 단추가 작동하는 방식을 결정합니다. ***이벤트** _를 선택하면 다음/이전 이벤트에 대한 탐색이 수행됩니다. _*_컨텍스트_*_ 를 선택하면 지정한 컨텍스트의 다음/이전 이벤트에서 탐색이 수행됩니다. _*_개체_*_ 를 선택하면 지정한 개체의 다음/이전 이벤트(예: 특정 큐와 연결된 이벤트)에 대한 탐색이 수행됩니다. _*_스위치_*_ 를 선택하면 컨텍스트의 다음/이전 변경에 대한 탐색이 수행됩니다. _ *_ID_**를 선택하면 지정한 이벤트 ID의 다음/이전 이벤트에 대한 탐색이 수행됩니다. |
| ![다음 이벤트 표시 단추](./media/user-guide/screen_shot_27.png)      | 다음 이벤트를 표시합니다. |
| ![다음 이벤트 페이지 표시 단추](./media/user-guide/screen_shot_28.png)      | 다음 이벤트 페이지를 표시합니다. |
| ![마지막 이벤트 선택 단추](./media/user-guide/screen_shot_29.png)      | 마지막 이벤트를 선택합니다. |

## <a name="display-mode-tabs"></a>표시 모드 탭

TraceX는 시스템 이벤트를 *순차* 및 *상대 시간* 의 두 가지 다른 방법으로 표시합니다. 기본 모드는 순차 모드이며, **그림 8** 에서 보여 주는 모드입니다.

모드를 변경하는 것은 TraceX 창에서 ***순차 보기** _ 또는 _*_시간 보기_*_ 탭을 선택하는 것만큼 간단합니다. _*그림 8**에서는 **_순차 보기_*_ 및 _ *_시간 보기_** 탭을 보여 줍니다.

![순차 보기 및 시간 보기 탭의 스크린샷](./media/user-guide/screen_shot_30.png)

**그림 8**

## <a name="sequential-view-mode"></a>순차 보기 모드

순차 보기 모드는 _*그림 8***에서 보여 주는 ***순차 보기** _ 탭에서 선택합니다. 이것이 기본 모드입니다. 이 모드에서는 이벤트 사이에 경과된 시간에 관계없이 이벤트가 바로 다음에 표시됩니다. **그림 8** 의 표시 영역 위에는 눈금자도 있습니다. 여기에는 추적 시작을 기준으로 하는 상대 이벤트 번호가 표시됩니다.

이 모드는 기본 모드이며, 시스템에서 진행되는 상황에 적절한 개요를 얻는 데 유용합니다.

## <a name="time-view-mode"></a>시간 보기 모드

시간 보기 모드는 ***시간 보기** _ 단추를 통해 선택됩니다. _ *그림 9**는 시간 보기 모드를 제외하고 **그림 8** 과 동일한 이벤트 추적을 보여 줍니다. 이 모드에서는 이벤트가 상대 시간 방식으로 표시되며, 녹색 막대가 이벤트 간의 실행을 표시하는 데 사용됩니다. 이 모드는 시스템에서 대량 처리가 수행되는 위치를 확인하는 데 유용하며, 개발자가 성능 및/또는 응답성을 높이기 위해 시스템을 튜닝하는 데 도움이 될 수 있습니다.

![시간 보기 탭의 스크린샷](./media/user-guide/screen_shot_31.png)

**그림 9**

**그림 9** 의 이벤트 표시 위에도 눈금자가 있습니다. 이 눈금자는 ThreadX 내부의 이벤트 추적 로깅에서 계측된 타임스탬프에서 파생된 추적 시작을 기준으로 하는 상대 틱 수를 표시합니다. 타임스탬프가 너무 가까이 있으면(빈도가 낮은 타이머) 이벤트가 동시에 실행됩니다. 반대로 타임스탬프가 너무 멀리 떨어져 있으면(빈도가 높은 타이머) 이벤트가 너무 멀리 떨어져 있습니다. 올바른 빈도 타임스탬프를 선택하는 것은 상대 시간 보기를 의미 있게 만드는 데 중요한 고려 사항입니다.

## <a name="system-summary-line"></a>시스템 요약 줄

TraceX는 동일한 줄의 모든 이벤트가 포함된 단일 요약 줄(**그림 10** 의 위쪽 컨텍스트)도 제공합니다. 이렇게 하면 복잡한 시스템의 개요를 쉽게 확인할 수 있습니다. 요약 표시줄은 스레드가 많은 시스템에서 특히 유용합니다. 이러한 요약 줄이 없으면 실행 컨텍스트를 따라 세로 스크롤 막대를 사용하여 복잡한 시스템 상호 작용을 수행해야 합니다.

![순차 보기 탭의 시스템 요약 줄에 대한 스크린샷](./media/user-guide/screen_shot_32.png)


**그림 10**

요약 줄에는 컨텍스트 요약 및 아래의 해당 이벤트 요약이 포함됩니다. **그림 10** 의 예에서 **_스레드 2_ *_가 실행되어 중단되었음을 쉽게 확인할 수 있습니다. 인터럽트는 _* _스레드 3_**, ***스레드 6** _, _*_스레드 4_*_ 및 _*_스레드 7_*_ 에서 선점되고, 그 후에 _ *_스레드 2_* *에서 실행을 다시 시작합니다.

## <a name="system-contexts"></a>시스템 컨텍스트

TraceX는 **그림 11** 과 같이 시스템 컨텍스트를 표시 왼쪽에 나열합니다. 특정 컨텍스트에서 발생하는 이벤트는 해당 컨텍스트의 오른쪽에 있는 가로줄에 표시됩니다. 이렇게 하면 이벤트가 발생한 컨텍스트를 쉽게 확인할 수 있을 뿐만 아니라 해당 컨텍스트 줄을 따라 특정 컨텍스트에서 발생한 모든 이벤트를 확인할 수도 있습니다.

첫 번째 견인 컨텍스트 항목은 항상 ***인터럽트** _ 및 _*_초기화/유휴_*_ 컨텍스트입니다. _*_인터럽트_*_ 컨텍스트는 IRS(인터럽트 서비스 루틴)에서 생성된 모든 시스템 이벤트를 나타냅니다. _*_초기화/유휴_*_ 컨텍스트는 ThreadX의 두 가지 컨텍스트를 나타냅니다. _*_tx_application_define_*_ 중에 발생하는 이벤트는 _*_초기화/유휴_*_ 컨텍스트입니다. 시스템이 유휴 상태이고 이로 인해 이벤트가 발생하지 않는 경우 시간 보기에서 _*_실행 중_*_ 을 나타내는 녹색 막대가 _ *_초기화/유휴_** 컨텍스트에 그려집니다.

![표시 왼쪽에 있는 시스템 컨텍스트의 스크린샷](./media/user-guide/screen_shot_33.png)

**그림 11**

**그림 11** 의 예에는 **_시스템 타이머 스레드_ *_ 컨텍스트에서 시작하는 9개의 스레드 컨텍스트가 있습니다. 개별 컨텍스트에 대한 추가 정보는 마우스를 해당 컨텍스트에 놓으면 사용할 수 있습니다. 추가 정보에는 스레드의 시작 스택 주소, 끝 스택 주소, 전체 크기, 사용한 비율, 상대 실행 백분율, 일시 중단 횟수, 재개 횟수 및 추적 중 가장 높은/낮은 우선 순위가 포함됩니다. _* 그림 12** 에서는 **_스레드 0_** 에 대한 정보를 보여 줍니다.

![스레드 0에 대한 정보의 스크린샷](./media/user-guide/screen_shot_34.png)


**그림 12**

컨텍스트는 더 큰 관심이 있는 컨텍스트를 그룹화하기 위해 이동할 수도 있습니다. 이는 컨텍스트를 끌어서 놓거나 마우스 오른쪽 단추로 컨텍스트를 클릭하여 수행할 수 있습니다. 마우스 오른쪽 단추로 컨텍스트를 클릭하면 컨텍스트를 위쪽 또는 아래쪽으로 이동할 수 있는 대화 상자가 생성됩니다. 

***맨 위로 이동** _을 선택하면 _*그림 13**과 같이 _*_스레드 3_*_ 컨텍스트가 컨텍스트 목록의 맨 위로 이동합니다.

![컨텍스트 목록의 맨 위로 이동되는 컨텍스트의 스크린샷](./media/user-guide/screen_shot_35.png)


**그림 13**

## <a name="thread-status-information"></a>스레드 상태 정보

사용하도록 설정되면 TraceX에서 스레드의 컨텍스트에 대해 색이 지정된 선을 통해 각 스레드의 상태를 표시합니다. 녹색 선은 스레드가 "준비" 상태이고, 다른 색의 선은 스레드가 일시 중단되었음을 나타냅니다. 일시 중단된 스레드의 경우 선의 색은 스레드가 일시 중단된 ThreadX 개체의 형식을 나타냅니다. 예를 들어 **그림 13** 에서 이벤트 147에서 시작하는 **_시스템 타이머 스레드_ *_ 컨텍스트의 녹색 선은 _* _시스템 타이머 스레드_ *_가 준비되었음을 나타냅니다. 이벤트 147 이전 및 이벤트 154 이후에 녹색 선이 없으면 _* _시스템 타이머 스레드_ *_가 준비되었음을 나타냅니다. 녹색 선이 이벤트 147 이전 및 이벤트 154 이후에 없으면 _* _시스템 타이머 스레드_** 가 일시 중단되었음을 나타냅니다.

![스레드의 컨텍스트에서 색이 지정된 선을 통한 각 스레드의 상태에 대한 스크린샷](./media/user-guide/screen_shot_36.png)

**그림 14**

세 가지 스레드 상태 표시 모드가 있으며, ***옵션 -> 상태 줄** _ 메뉴를 통해 사용할 수 있습니다. _*_준비만_*_ 옵션은 준비(녹색) 상태 줄만 표시하고, 일시 중단 상태 줄은 표시하지 않습니다. 이는 TraceX의 기본 옵션입니다. _ *_모두 설정_** 옵션을 사용하면 모든 상태 줄(준비 및 일시 중단)을 표시할 수 있습니다.

마지막으로 ***모두 해제*** 옵션을 사용하면 모든 상태 줄을 표시하지 않습니다.

## <a name="event-information-display"></a>이벤트 정보 표시

TraceX는 ThreadX, FileX, NetX, NetX Duo 및 USBX API 호출 및 내부 이벤트를 포함하여 약 600개의 런타임 이벤트에 대한 자세한 정보를 제공합니다. 또한 TraceX는 최대 61,439개의 고유한 사용자 정의 이벤트를 추가로 지원합니다.

순차 또는 시간 표시 모드의 선택 여부에 관계없이 마우스를 표시 영역의 이벤트 위에 놓으면 이벤트 근처에 자세한 이벤트 정보가 표시됩니다. _*그림 15**에서는 마우스를 데모 ***demo_threadx.trx** _ 추적 파일의 이벤트 143 위에 놓았습니다.

![샘플 추적 파일의 이벤트 143 위에 놓은 마우스에 대한 스크린샷](./media/user-guide/screen_shot_37.png)

**그림 15**

표시되는 각 이벤트에는 ***컨텍스트**, _*_상대 시간_*_ 및 _*_타임스탬프_*_ 에 대한 표준 정보가 모두 포함됩니다. 컨텍스트 필드에는 이벤트가 발생한 컨텍스트가 표시됩니다. 정확히 스레드, 유휴, ISR 및 초기화의 4가지 컨텍스트가 있습니다. 스레드 컨텍스트에서 이벤트가 발생하면 스레드의 이름과 해당 우선 순위가 해당 시간에 수집되어 위와 같이 표시됩니다. _*_상대 시간_*_ 에는 추적 시작 시점을 기준으로 하는 상대 타이머 틱 수가 표시됩니다. _ *_원시 타임스탬프_**에는 이벤트의 원시 시간 원본이 표시됩니다. 마지막으로 모든 이벤트 관련 정보가 표시됩니다. 이 정보는 이 챕터의 나머지 부분에서 자세히 설명합니다.

이벤트를 두 번 클릭하면 자세한 이벤트 정보도 사용할 수 있습니다. **그림 16** 에서는 이벤트 143을 두 번 클릭하는 것을 보여 줍니다.

![이벤트를 두 번 클릭하는 경우의 자세한 이벤트 정보에 대한 스크린샷](./media/user-guide/screen_shot_38.png)

**그림 16**

여러 이벤트를 한 번에 볼 수 있으면 사용자에게 발생한 상황에 대해 더 풍부한 보기가 제공됩니다. 많은 이벤트가 서로 관련되어 있으므로 나란히 표시하는 것이 매우 유용합니다. 이는 여러 이벤트를 두 번 클릭하여 수행합니다.

## <a name="current-event-display"></a>현재 이벤트 표시

TraceX에서 사용자가 ***보기 -> 현재 이벤트*** 를 통해 선택하거나 도구 모음에서 현재 이벤트 단추를 클릭하면 현재 이벤트를 별도의 창에 표시합니다. 선택되면 TraceX에서 현재 선택한 이벤트를 독립 실행형 창에 표시하고, 다른 이벤트를 선택할 때마다 이 창을 새로 고칩니다.

## <a name="event-searching"></a>이벤트 검색

TraceX는 광범위한 이벤트 검색 기능을 제공합니다. 각 이벤트의 이벤트 ID 및 정보 필드는 기본 검색 매개 변수입니다. 검색 매개 변수 값을 지정하지 않으면 매개 변수가 검색에서 해당 매개 변수를 효과적으로 제거함을 나타냅니다. 또한 검색은 검색된 매개 변수에서 검색을 충족하거나 모든 매개 변수를 검색하여 검색을 충족해야 하는 방식으로 수행할 수 있습니다. 검색은 특정 컨텍스트로 제한되거나 추적의 모든 컨텍스트를 포함할 수도 있습니다. 이벤트 검색 호출은 _*그림 17**과 같이 도구 모음에서 ***값으로 검색** _ 단추를 선택하여 수행됩니다. 선택하면 검색에 대한 모든 매개 변수를 지정하는 검색 대화 상자가 표시됩니다. 그런 다음, 검색 대화 상자의 **_다음_*_ 및 _*_이전_*_ 단추를 사용하여 지정된 검색 조건과 일치하는 다음 및 이전 이벤트를 찾을 수 있습니다. _ *그림 17**에서는 검색 대화 상자를 보여 줍니다.

![이벤트 검색의 스크린샷](./media/user-guide/screen_shot_39.png)

**그림 17**

![검색 대화 상자의 스크린샷](./media/user-guide/screen_shot_40.png)

**그림 18**

## <a name="zooming-in-and-out"></a>확대 및 축소

기본적으로 TraceX는 이벤트를 전체 크기로 표시합니다. 원하는 대로 확대하거나 축소할 수 있습니다. 축소는 추적에서 캡처된 전체 이벤트를 확인하는 데 유용하며, 확대는 타임스탬프 원본의 해상도로 인해 이벤트가 겹치는 조건에서 유용합니다. **그림 19** 에서는 추적 파일의 100%를 표시하도록 축소된 **_demo_threadx.trx_** 파일을 보여 줍니다.

![추적 파일의 100%를 표시하도록 축소된 샘플 파일의 스크린샷](./media/user-guide/screen_shot_41.png)

**그림 19**

100% 축소하여 현재 표시 페이지 내의 전체 추적을 표시하면 추적에서 캡처된 모든 컨텍스트 실행과 해당 컨텍스트 내에서 발생하는 일반 이벤트를 쉽게 확인할 수 있습니다. **그림 16** 에서는 **_스레드 1_ *_ 및 _* _스레드 2_** 가 가장 자주 실행되었습니다. 또한 해당 이벤트에 대한 파란색 지정은 이러한 스레드에서 큐 서비스 호출을 수행하고 있음을 나타냅니다(큐 이벤트는 파란색임).

전체 아이콘 보기로 복원하는 것도 마찬가지로 쉽습니다. 확대 단추를 반복적으로 선택하거나 일부 비율을 100으로 입력할 수 있습니다.

## <a name="delta-ticks-between-events"></a>이벤트 간 델타 틱 수

다양한 이벤트 간의 틱 수는 TraceX에서 쉽게 확인할 수 있습니다. 시작 이벤트를 클릭하고 마우스를 끝 이벤트로 끕니다. 이벤트 간의 델타 틱 수는 **그림 17** 과 같이 표시의 오른쪽 위 모서리에 표시됩니다.

![이벤트 간 델타 틱 수의 스크린샷](./media/user-guide/screen_shot_42.png)

**그림 17**

**그림 17** 에 표시된 델타 틱 수는 이벤트 125와 이벤트 154 사이에서 5,032개 틱이 경과했음을 보여 줍니다. 이는 각 이벤트의 상대 타임스탬프를 확인하고 뺄셈을 수행하여 수동으로 계산할 수도 있지만 GUI를 사용하는 것이 쉽고 즉각적입니다.

## <a name="actual-time-display"></a>실제 시간 표시

사용하도록 설정하면 TraceX에서 ***시간 보기** _ 및 TraceX에서 표시하는 다양한 델타 시간 정보에 대한 실제 시간(마이크로초)을 표시합니다. 실제 시간 표시는 기본적으로 사용하지 않도록 설정됩니다. 실제 시간 표시를 사용하도록 설정하려면 _ *_옵션 -> 마이크로초당 틱 수_** 메뉴 선택을 통해 마이크로초당 틱 수를 입력해야 합니다(입력하는 값은 대상에 대한 TraceX 이벤트 로깅에 사용되는 하드웨어 타이머 원본을 통해 결정됨).

## <a name="priority-inversions"></a>우선 순위 반전

TraceX는 추적 파일에서 검색된 우선 순위 반전을 자동으로 표시합니다. 우선 순위 반전은 우선 순위가 낮은 스레드에서 현재 소유하고 있는 뮤텍스를 얻으려고 시도하는 우선 순위가 높은 스레드가 차단되는 조건으로 정의됩니다. 시스템이 이러한 방식으로 작동하도록 설정되었으므로 이 조건을 *결정적* 이라고 합니다. 사용자에게 알리기 위해 TraceX는 *결정적* 우선 순위 반전 범위를 연한 연어 색으로 표시합니다.

TraceX는 *비결정적* 우선 순위 반전도 표시합니다. 이러한 우선 순위 반전은 다른 우선 순위 수준의 다른 스레드가 *결정적* 우선 순위 반전의 중간에 실행되었다는 점에서 *결정적* 우선 순위 반전과 다릅니다. 따라서 우선 순위 반전 내의 시간은 다소 *비결정적* 입니다. 이 조건은 사용자가 알 수 없는 경우가 많으며 매우 심각할 수 있습니다. 사용자에게 이 조건을 경고하기 위해 TraceX는 *비결정적* 우선 순위 반전을 더 밝은 연어 색으로 표시합니다. **그림 18** 에서는 *결정적* 및 *비결정적* 우선 순위 반전을 모두 보여 줍니다.

![추적 파일의 우선 순위 반전에 대한 스크린샷](./media/user-guide/screen_shot_43.png)

**그림 18**

**그림 18** 에서는 이벤트 398에서 이벤트 402까지의 *결정적* 우선 순위 반전을 보여 줍니다. 이 범위에서 우선 순위가 높은 ***스레드 0** _은 우선 순위가 낮은 _*_스레드 1_*_ 에서 소유하는 뮤텍스를 차단합니다. 이벤트 402에서 _ *_thread 1_**은 뮤텍스를 해제하므로 우선 순위 반전이 종료됩니다.

더 밝은 음영 처리 영역에서는 이벤트 408에서 이벤트 420 사이의 *비결정적* 우선 순위 반전을 보여 줍니다. 이 *비결정적* 우선 순위를 만드는 것은 ***스레드 1** _에서 우선 순위가 높은 _*_스레드 0_*_ 이 차단되는 뮤텍스를 보유하는 동안 _* _스레드 2_**를 다시 시작하는 인터럽트가 발생한다는 것입니다. 그 후에는 시스템에서 우선 순위 반전에 있는 시간을 실행하고 늘립니다. 이 조건은 매우 심각하고 식별하기 어려울 수 있지만, TraceX를 사용하면 쉽게 식별할 수 있습니다.
