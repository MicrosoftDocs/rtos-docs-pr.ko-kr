---
title: 6장 - Azure RTOS FileX 내결함성 모듈
description: 이 장에는 미디어의 전원이 꺼지거나 파일 쓰기 작업 도중 배출되는 경우 파일 시스템 무결성을 유지하기 위해 설계된 Azure RTOS FileX 내결함성 모듈에 대한 설명이 포함되어 있습니다.
author: philmea
ms.author: philmea
ms.date: 05/19/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 66bffa2dbf52bc458bfaf124aa006a79e810100ac2e926c17444daf090519e66
ms.sourcegitcommit: 93d716cf7e3d735b18246d659ec9ec7f82c336de
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2021
ms.locfileid: "116783751"
---
# <a name="chapter-6---azure-rtos-filex-fault-tolerant-module"></a>6장 - Azure RTOS FileX 내결함성 모듈

이 장에는 미디어의 전원이 꺼지거나 파일 쓰기 작업 도중 배출되는 경우 파일 시스템 무결성을 유지하기 위해 설계된 Azure RTOS FileX 내결함성 모듈에 대한 설명이 포함되어 있습니다.

## <a name="filex-fault-tolerant-module-overview"></a>FileX 내결함성 모듈 개요

애플리케이션이 파일에 데이터를 쓸 때 FileX는 데이터 클러스터와 시스템 정보를 모두 업데이트합니다. 이러한 업데이트는 파일 시스템의 정보를 일관되게 유지하는 원자성 작업으로 완료되어야 합니다. 예를 들어, 파일에 데이터를 추가할 때 FileX는 미디어에서 사용 가능한 클러스터를 찾고, FAT 체인을 업데이트하고, 디렉터리 항목에 기록되는 길이를 업데이트하고, 디렉터리 항목에서 시작 클러스터 번호를 업데이트해야 합니다. 전원 오류나 미디어 배출로 인해 업데이트 시퀀스가 중단될 수 있으며, 이로 인해 파일 시스템이 일관되지 않은 상태가 됩니다. 일관되지 않은 상태가 교정되지 않으면 업데이트되는 데이터가 손실될 수 있으며, 시스템 정보 손상으로 인해 후속 파일 시스템 작업 시 미디어의 다른 파일 또는 디렉터리가 손상될 수 있습니다.

FileX 내결함성 모듈은 파일 시스템에 이러한 단계를 적용하기 *전에* 파일 업데이트에 필요한 저널링 단계를 따라 작동합니다. 파일 업데이트에 성공하면 로그 항목이 제거됩니다. 하지만 파일 업데이트가 중단되면 로그 항목이 미디어에 저장됩니다. 다음 번에 미디어가 탑재될 때 FileX는 이전의(완료되지 않은) 쓰기 작업에서 이러한 로그 항목을 검색합니다. 이러한 경우 FileX는 파일 시스템에 이미 적용된 변경 사항을 롤백하거나 이전 작업을 완료하는 데 필요한 변경 사항을 다시 적용하여 오류를 복구할 수 있습니다. 이러한 방식으로 업데이트 작업 중에 미디어 전원이 꺼진 경우 FileX 내결함성 모듈은 파일 시스템 무결성을 유지합니다.

> [!IMPORTANT]
> *FileX 내결함성 모듈은 유효한 데이터가 포함된 물리적 미디어 손상으로 인해 발생하는 파일 시스템 손상을 방지하도록 설계되지 않았습니다.*

> [!IMPORTANT]
> *FileX 내결함성 모듈이 미디어를 보호한 후에는 내결함성 사용이 설정된 FileX 이외의 방법을 통해 미디어를 탑재해서는 안 됩니다. 이렇게 하면 파일 시스템의 로그 항목이 미디어의 시스템 정보와 일치하지 않을 수 있습니다. 다른 파일 시스템에서 미디어를 업데이트한 이후 FileX 내결함성 모듈에서 로그 항목을 처리하려고 하면 복구 절차가 실패하여 전체 파일 시스템이 예측할 수 없는 상태가 될 수 있습니다.*

## <a name="use-of-the-fault-tolerant-module"></a>내결함성 모듈 사용

FileX 내결함성 기능은 FileX에서 지원하는 모든 FAT 파일 시스템(FAT12, FAT16, FAT32, exFAT 등)에서 사용할 수 있습니다. 내결함성 기능을 사용하도록 설정하려면 **FX_ENABLE_FAULT_TOLERANT** 기호가 정의된 FileX가 빌드되어야 합니다. 런타임에서 애플리케이션은 **_fx_fault_tolerant_enable_ *_을 호출(_* _fx_media_open_** 호출 직후)하여 내결함성 서비스를 시작합니다. 내결함성 서비스를 사용한 후에는 지정된 미디어에 대한 모든 파일 쓰기 작업이 보호됩니다. 기본적으로 내결함성 모듈은 사용하도록 설정되어 있지 않습니다.

> [!IMPORTANT]
> *애플리케이션에서 ***fx_fault_tolerant_enable** _ 호출 전에 파일 시스템이 액세스되지 않는지 확인해야 합니다. 내결함성을 사용하도록 설정하기 전에 애플리케이션에서 파일 시스템에 데이터를 쓰는 경우 이전 쓰기 작업이 완료되지 않으면 쓰기 작업 시 미디어가 손상될 수 있으며, 파일 시스템은 내결함성 로그 항목을 사용하여 복구되지 않습니다.

## <a name="filex-fault-tolerant-module-log"></a>FileX 내결함성 모듈 로그

FileX 내결함성 로그는 플래시에서 하나의 논리 클러스터를 차지합니다. 해당 클러스터의 시작 클러스터 번호에 대한 인덱스가 미디어의 부트 섹터에서 기록되고, **FX_FAULT_TOLERANT_BOOT_INDEX** 기호에 의해 오프셋이 지정됩니다. 기본적으로 이 기호는 116으로 정의됩니다. FAT12/16/32 및 exFAT 사양에서 예약된 것으로 표시되기 때문에 이 위치가 선택됩니다.

그림 5, "로그 구조 레이아웃"은 로그 구조의 일반적인 레이아웃을 보여줍니다. 로그 구조는 로그 헤더, FAT 체인 및 로그 항목의 세 가지 섹션으로 구성되어 있습니다.

> [!IMPORTANT]
> *로그 항목에 저장된 모든 멀티바이트 값은 Little Endian 형식입니다.*

![로그 구조 레이아웃](./media/user-guide/log-structure-layout.png)

**그림 5. 로그 구조 레이아웃**

로그 헤더 영역에는 전체 로그 구조를 설명하고 각 필드에 대해 자세히 설명하는 정보가 포함되어 있습니다.

**표 8. 로그 헤더 영역**

|필드|크기(바이트)|Description|
|-----|--------------|-----------|
|ID|4|FileX 내결함성 로그 구조를 식별합니다. ID 값이 0x46544C52가 아닌 경우 로그 구조가 잘못된 것으로 간주됩니다.|
|전체 크기|2|전체 로그 구조의 전체 크기(바이트)를 나타냅니다.|
|헤더 체크섬|2|로그 헤더 영역을 변환하는 체크섬입니다. 헤더 필드가 체크섬 확인에 실패하는 경우 로그 구조가 잘못된 것으로 간주됩니다.|
|버전 번호|2|FileX 내결함성 주 버전 번호 및 부 버전 번호입니다.|
|예약됨|2|향후 확장용입니다.|

로그 헤더 영역 뒤에는 FAT 체인 로그 영역이 있습니다. 그림 9에는 FAT 체인을 수정하는 방법을 설명하는 정보가 포함되어 있습니다. 이 로그 영역에는 파일에 할당되는 클러스터, 파일에서 제거되는 클러스터, 삽입/삭제가 이루어져야 하는 위치에 관한 정보가 포함되고, FAT 체인 로그 영역의 각 필드를 설명합니다.

**표 9. FAT 체인 로그 영역**

|필드|크기(바이트)|Description|
|-----|--------------|-----------|
|FAT 체인 로그 체크섬|2|전체 FAT 체인 로그 영역의 체크섬입니다. 체크섬 확인에 실패하는 경우 FAT 체인 로그 영역은 잘못된 것으로 간주됩니다.|
|플래그|1|유효한 플래그는 다음과 같습니다.<br/>0x01 FAT 체인 유효함<br />0x02 비트맵 사용 중|
|예약됨|1|나중에 사용하도록 예약되어 있습니다.|
|삽입 지점 – 전면|4|새로 만든 체인이 연결될 클러스터(원래 FAT 체인에 속함).|
|새 FAT 체인의 헤드 클러스터|4|새로 만든 FAT 체인의 첫 번째 클러스터|
|원래 FAT 체인의 헤드 클러스터|4|제거할 원래 FAT 체인 부분의 첫 번째 클러스터입니다.|
|삽입 지점 - 후면|4|새로 만든 FAT 체인이 조인하는 원래 클러스터입니다.|
|다음 삭제 지점|4|이 필드는 FAT 체인 정리 프로시저를 지원합니다.|

로그 항목 영역에는 오류를 복구하는 데 필요한 변경 사항을 설명하는 로그 항목이 포함되어 있습니다. FileX 내결함성 모듈에서 지원하는 로그 항목에는 FAT 로그 항목, 디렉터리 로그 항목, 비트맵 로그 항목의 세 가지 유형이 있습니다.

다음 3개의 그림 및 표는 이러한 로그 항목을 자세히 설명합니다.

![FAT 로그 항목](./media/user-guide/fat-log-entry.png)

**그림 6. FAT 로그 항목**

**표 10. FAT 로그 항목**

|필드|크기(바이트)|Description|
|-----|--------------|-----------|
Type|2|항목의 유형(FX_FAULT_TOLERANT_FAT_LOG_TYPE이어야 함)|
|크기|2|이 항목의 크기|
|클러스터 번호|4|클러스터 번호|
|값|4|FAT 항목에 쓸 값|

![디렉터리 로그 항목](./media/user-guide/directory-log-entry.png)

**그림 7. 디렉터리 로그 항목**

**표 11. 디렉터리 로그 항목**

|필드|크기(바이트)|Description|
|-----|--------------|-----------|
|Type|2|항목의 유형(FX_FAULT_TOLERANT_DIRECTORY_LOG_TYPE이어야 함)|
|크기|2|이 항목의 크기|
|섹터 오프셋|4|이 디렉터리가 있는 섹터의 오프셋(바이트)입니다.|
|로그 섹터|4|디렉터리 항목이 있는 섹터입니다.|
|로그 데이터|변수|디렉터리 항목의 내용|

![비트맵 로그 항목](./media/user-guide/bitmap-log-entry.png)

**그림 8. 비트맵 로그 항목**

**표 12. 비트맵 로그 항목**

|필드|크기(바이트)|Description|
|-----|--------------|-----------|
|Type|2|항목의 유형(FX_FAULT_TOLERANT_BITMAP_LOG_TYPE이어야 함)|
|크기|2|이 항목의 크기|
|클러스터 번호|4|클러스터 번호|
|값|4|FAT 항목에 쓸 값|

## <a name="fault-tolerant-protection"></a>내결함성 보호

FileX 내결함성 모듈은 시작 후 먼저 미디어에서 기존 내결함성 로그 파일을 검색합니다. 유효한 로그 파일을 찾을 수 없는 경우 FileX는 미디어를 보호되지 않는 미디어로 간주합니다. 이 경우 FileX는 미디어에 내결함성 로그 파일을 만듭니다.

> [!IMPORTANT]
> *FileX 내결함성 모듈 시작 전에 파일 시스템이 손상된 경우 FileX는 파일 시스템을 보호할 수 없습니다. *

내결함성 로그 파일이 있는 경우 FileX는 기존 로그 항목을 확인합니다. 로그 항목이 없는 로그 파일은 이전 파일 작업이 성공적이며 모든 로그 항목이 제거되었음을 나타냅니다. 이 경우 애플리케이션은 내결함성 보호가 포함된 파일 시스템 사용을 시작할 수 있습니다.

하지만 로그 항목이 있는 경우 FileX는 이전 파일 작업을 완료하거나 파일 시스템에 이미 적용된 변경 사항을 되돌려야 하므로 실질적으로는 변경 사항을 취소해야 합니다. 두 경우 모두 파일 시스템에 로그 항목이 적용된 후 파일 시스템은 일관된 상태로 복원되며, 애플리케이션은 파일 시스템을 다시 사용하여 시작할 수 있습니다.

FileX로 보호되는 미디어의 경우 파일 업데이트 작업 도중 데이터 부분을 미디어에 직접 쓰게 됩니다. FileX는 데이터를 쓰고, 디렉터리 항목, FAT 테이블에 적용해야 하는 변경 사항도 기록합니다. 이 정보는 파일 허용 로그 항목에 기록됩니다. 이 방법을 사용하면 데이터를 미디어에 쓴 이후 파일 시스템에 대한 업데이트가 보장됩니다. 데이터 쓰기 단계에서 미디어를 꺼낼 경우 중요한 파일 시스템 정보가 아직 변경되지 않게 됩니다. 따라서 파일 시스템이 중단의 영향을 받지 않습니다.

모든 데이터를 미디어에 성공적으로 쓴 후에는 FileX는 로그 항목의 정보를 따라 시스템 정보에 대한 변경 사항을 한 번에 하나씩 적용합니다. 모든 시스템 정보를 미디어에 커밋한 후에는 로그 항목이 오류 허용 로그에서 제거됩니다. 이 시점에서 FileX는 파일 업데이트 작업을 완료합니다.

파일 업데이트 작업을 수행하는 동안 파일은 현재 위치에서 업데이트되지 않습니다. 내결함성 모듈은 데이터에 대한 섹터를 할당하여 새 데이터를 쓴 다음, 덮어쓸 데이터가 포함된 섹터를 제거하고, 관련 FAT 항목을 업데이트하여 새 섹터를 체인에 연결합니다. 클러스터의 부분 데이터를 수정해야 하는 경우 FileX는 항상 새 클러스터를 할당하고, 업데이트된 데이터가 있는 이전 클러스터의 전체 데이터를 새 클러스터에 쓴 다음, 이전 클러스터를 제거합니다. 이렇게 하면 파일 업데이트가 중단된 경우에도 원본 파일이 그대로 유지됩니다. 애플리케이션은 FileX 내결함성 보호 상태에서 파일의 데이터를 업데이트하려면 이전 데이터가 포함된 섹터를 제거하기 전에 새 데이터를 저장할 수 있는 충분한 여유 공간이 미디어에 있어야 함을 파악해야 합니다. 미디어에 새 데이터를 저장할 충분한 공간이 없는 경우 업데이트 작업은 실패합니다.
