---
title: 2장 - Azure RTOS ThreadX 설치 및 사용
description: 이 장에서는 고성능 Azure RTOS ThreadX 커널의 설치, 설정, 사용과 관련된 다양한 문제에 대해 설명합니다.
author: philmea
ms.author: philmea
ms.date: 05/19/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: e1bf85d363b07c81f226d494107eae9ba18114a7
ms.sourcegitcommit: e3d42e1f2920ec9cb002634b542bc20754f9544e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104810272"
---
# <a name="chapter-2---installation-and-use-of-azure-rtos-threadx"></a><span data-ttu-id="f5bee-103">2장 - Azure RTOS ThreadX 설치 및 사용</span><span class="sxs-lookup"><span data-stu-id="f5bee-103">Chapter 2 - Installation and Use of Azure RTOS ThreadX</span></span>

<span data-ttu-id="f5bee-104">이 장에서는 고성능 Azure RTOS ThreadX 커널의 설치, 설정, 사용과 관련된 다양한 문제에 대해 설명합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-104">This chapter contains a description of various issues related to installation, setup, and usage of the high-performance Azure RTOS ThreadX kernel.</span></span>

## <a name="host-considerations"></a><span data-ttu-id="f5bee-105">호스트 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f5bee-105">Host Considerations</span></span>

<span data-ttu-id="f5bee-106">임베디드 소프트웨어는 일반적으로 Windows 또는 Linux(Unix) 호스트 컴퓨터에서 개발됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-106">Embedded software is usually developed on Windows or Linux (Unix) host computers.</span></span> <span data-ttu-id="f5bee-107">애플리케이션이 호스트에서 컴파일, 링크 및 배치된 후 실행할 수 있도록 대상 하드웨어에 다운로드됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-107">After the application is compiled, linked, and located on the host, it is downloaded to the target hardware for execution.</span></span>

<span data-ttu-id="f5bee-108">일반적으로 대상 다운로드는 개발 도구의 디버거 내에서 수행됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-108">Usually the target download is done from within the development tool debugger.</span></span> <span data-ttu-id="f5bee-109">다운로드가 완료된 후에 디버거는 대상 실행 제어(이동, 중지, 중단점 등) 뿐만 아니라 메모리 및 프로세서 레지스터에 대한 액세스를 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-109">After the download has completed, the debugger is responsible for providing target execution control (go, halt, breakpoint, etc.) as well as access to memory and processor registers.</span></span>

<span data-ttu-id="f5bee-110">대부분의 개발 도구 디버거는 JTAG(IEEE 1149.1) 및 BDM(백그라운드 디버그 모드)과 같은 OCD(온-칩 디버그) 연결을 통해 대상 하드웨어와 통신합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-110">Most development tool debuggers communicate with the target hardware via on-chip debug (OCD) connections such as JTAG (IEEE 1149.1) and Background Debug Mode (BDM).</span></span> <span data-ttu-id="f5bee-111">또한 디버거는 ICE(회로 내 에뮬레이션) 연결을 통해 대상 하드웨어와 통신합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-111">Debuggers also communicate with target hardware through In-Circuit Emulation (ICE) connections.</span></span> <span data-ttu-id="f5bee-112">OCD와 ICE 연결은 모두 대상 상주 소프트웨어에 대한 최소 침입으로 강력한 솔루션을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-112">Both OCD and ICE connections provide robust solutions with minimal intrusion on the target resident software.</span></span>

<span data-ttu-id="f5bee-113">호스트에서 사용되는 리소스의 경우에는 ThreadX에 대한 소스 코드를 ASCII 형식으로 전달하고 호스트 컴퓨터의 하드 디스크에 약 1MB의 공간이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-113">As for resources used on the host, the source code for ThreadX is delivered in ASCII format and requires approximately 1 MByte of space on the host computer's hard disk.</span></span>

## <a name="target-considerations"></a><span data-ttu-id="f5bee-114">대상 고려 사항</span><span class="sxs-lookup"><span data-stu-id="f5bee-114">Target Considerations</span></span>

<span data-ttu-id="f5bee-115">ThreadX에는 대상에서 2KB~20KB의 ROM(읽기 전용 메모리)이 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-115">ThreadX requires between 2 KBytes and 20 KBytes of Read Only Memory (ROM) on the target.</span></span> <span data-ttu-id="f5bee-116">또한 목표 RAM(Random Access Memory) 외에 ThreadX 시스템 스택 및 기타 글로벌 데이터 구조를 위한 용량으로 1~2KB가 더 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-116">It also requires another 1 to 2 KBytes of the target's Random Access Memory (RAM) for the ThreadX system stack and other global data structures.</span></span>

<span data-ttu-id="f5bee-117">서비스 호출 시간 초과, 시간 조각화 및 애플리케이션 타이머와 같은 타이머 관련 기능이 작동하려면 기본 대상 하드웨어에서 정기적으로 인터럽트 소스를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-117">For timer-related functions like service call time-outs, time-slicing, and application timers to function, the underlying target hardware must provide a periodic interrupt source.</span></span> <span data-ttu-id="f5bee-118">프로세서에 이 용량이 있는 경우 ThreadX에서 활용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-118">If the processor has this capability, it is utilized by ThreadX.</span></span> <span data-ttu-id="f5bee-119">그렇지 않고 대상 프로세서에서 정기적 인터럽트를 생성할 수 없는 경우 사용자의 하드웨어에서 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-119">Otherwise, if the target processor does not have the ability to generate a periodic interrupt, the user's hardware must provide it.</span></span> <span data-ttu-id="f5bee-120">타이머 인터럽트의 설정 및 구성은 일반적으로 ThreadX 배포의 ***tx_initialize_low_level*** 어셈블리 파일에 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-120">Setup and configuration of the timer interrupt is typically located in the ***tx_initialize_low_level*** assembly file in the ThreadX distribution.</span></span>

> [!NOTE]
> <span data-ttu-id="f5bee-121">*ThreadX는 정기적인 타이머 인터럽트 소스를 사용할 수 없더라도 작동합니다. 그렇지만 타이머 관련 서비스는 작동하지 않습니다.*</span><span class="sxs-lookup"><span data-stu-id="f5bee-121">*ThreadX is still functional even if no periodic timer interrupt source is available. However, none of the timer-related services are functional.*</span></span>

## <a name="product-distribution"></a><span data-ttu-id="f5bee-122">제품 배포</span><span class="sxs-lookup"><span data-stu-id="f5bee-122">Product Distribution</span></span>

<span data-ttu-id="f5bee-123">Azure RTOS ThreadX는 <https://github.com/azure-rtos/threadx/>에 있는 퍼블릭 소스 코드 리포지토리에서 가져올 수 있습니다</span><span class="sxs-lookup"><span data-stu-id="f5bee-123">Azure RTOS ThreadX can be obtained from our public source code repository at <https://github.com/azure-rtos/threadx/>.</span></span>

<span data-ttu-id="f5bee-124">다음은 리포지토리에 있는 몇 가지 중요한 파일 목록입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-124">The following is a list of several important files in the repository.</span></span>

| <span data-ttu-id="f5bee-125">파일 이름</span><span class="sxs-lookup"><span data-stu-id="f5bee-125">Filename</span></span> | <span data-ttu-id="f5bee-126">Description</span><span class="sxs-lookup"><span data-stu-id="f5bee-126">Description</span></span> |
|------------------- | ----------- |
| <span data-ttu-id="f5bee-127">**tx_api.h**</span><span class="sxs-lookup"><span data-stu-id="f5bee-127">**tx_api.h**</span></span>                      | <span data-ttu-id="f5bee-128">모든 시스템 등식, 데이터 구조 및 서비스 프로토타입이 포함되어 있는 C 헤더 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-128">C header file containing all system equates, data structures, and service prototypes.</span></span>                                                             |
| <span data-ttu-id="f5bee-129">**tx_port.h**</span><span class="sxs-lookup"><span data-stu-id="f5bee-129">**tx_port.h**</span></span>                     | <span data-ttu-id="f5bee-130">모든 개발 도구 및 대상 특정 데이터 정의 및 구조를 포함하는 C 헤더 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-130">C header file containing all development-tool and targetspecific data definitions and structures.</span></span>                                                 |
| <span data-ttu-id="f5bee-131">**demo_threadx.c**</span><span class="sxs-lookup"><span data-stu-id="f5bee-131">**demo_threadx.c**</span></span>                | <span data-ttu-id="f5bee-132">작은 데모 애플리케이션을 포함하는 C 파일입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-132">C file containing a small demo application.</span></span>                                                                                                       |
| <span data-ttu-id="f5bee-133">**tx.a(또는 tx.lib)**</span><span class="sxs-lookup"><span data-stu-id="f5bee-133">**tx.a (or tx.lib)**</span></span>              | <span data-ttu-id="f5bee-134">*표준* 패키지와 함께 배포되는 Threadx C 라이브러리의 이진 버전입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-134">Binary version of the ThreadX C library that is distributed with the *standard* package.</span></span>                                                          |
|                                   |                                                                                                                                                   |

>[!NOTE]
><span data-ttu-id="f5bee-135">모든 파일 이름은 소문자로 되어 있습니다. 이 명명 규칙을 사용하면 명령을 Linux(Unix) 개발 플랫폼으로 좀 더 쉽게 변환할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-135">*All file names are in lower-case. This naming convention makes it easier to convert the commands to Linux (Unix) development platforms.*</span></span>

## <a name="threadx-installation"></a><span data-ttu-id="f5bee-136">ThreadX 설치</span><span class="sxs-lookup"><span data-stu-id="f5bee-136">ThreadX Installation</span></span>

<span data-ttu-id="f5bee-137">ThreadX는 GitHub 리포지토리를 로컬 머신에 복제하여 설치됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-137">ThreadX is installed by cloning the GitHub repository to your local machine.</span></span> <span data-ttu-id="f5bee-138">다음은 PC에서 ThreadX 리포지토리의 복제본을 만들기 위한 일반적인 구문입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-138">The following is typical syntax for creating a clone of the ThreadX repository on your PC.</span></span>

```c
    git clone https://github.com/azure-rtos/threadx
```

<span data-ttu-id="f5bee-139">또는 GitHub 기본 페이지의 다운로드 단추를 사용하여 리포지토리의 복사본을 다운로드할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-139">Alternatively you can download a copy of the repository using the download button on the GitHub main page.</span></span>

<span data-ttu-id="f5bee-140">또한 온라인 리포지토리의 프런트 페이지에서 ThreadX 라이브러리를 빌드하기 위한 지침을 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-140">You will also find instructions for building the ThreadX library on the front page of the online repository.</span></span>

> [!NOTE]
> <span data-ttu-id="f5bee-141">*애플리케이션 소프트웨어는 ThreadX 라이브러리 파일(일반적으로 **tx.a** 또는 **tx.lib**) 및 C 포함 파일 *_tx_api.h_* 및 _*_tx_port.h_\*_ 에 액세스할 수 있어야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-141">*Application software needs access to the ThreadX library file (usually **tx.a** or **tx.lib**) and the C include files **_tx_api.h_* _ and _*_tx_port.h_*_.</span></span> <span data-ttu-id="f5bee-142">이 작업은 개발 도구에 대한 적절한 경로를 설정하거나 이러한 파일을 애플리케이션 개발 영역으로 복사하여 수행합니다._</span><span class="sxs-lookup"><span data-stu-id="f5bee-142">This is accomplished either by setting the appropriate path for the development tools or by copying these files into the application development area._</span></span>

## <a name="using-threadx"></a><span data-ttu-id="f5bee-143">ThreadX 사용</span><span class="sxs-lookup"><span data-stu-id="f5bee-143">Using ThreadX</span></span>

<span data-ttu-id="f5bee-144">ThreadX를 사용하려면 컴파일하는 동안 애플리케이션 코드가 ***tx_api.h** _를 포함하고 ThreadX 런타임 라이브러리 _*_tx.a_*_(또는 _*_tx.lib_\*\*)와 연결해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-144">To use ThreadX, the application code must include ***tx_api.h** _ during compilation and link with the ThreadX run-time library _*_tx.a_*_ (or _*_tx.lib_\*\*).</span></span>

<span data-ttu-id="f5bee-145">ThreadX 애플리케이션을 빌드하는 데는 4가지 단계가 필요합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-145">There are four steps required to build a ThreadX application.</span></span>

1. <span data-ttu-id="f5bee-146">ThreadX 서비스 또는 데이터 구조를 사용하는 모든 애플리케이션 파일에 ***tx_api.h*** 파일을 포함합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-146">Include the ***tx_api.h*** file in all application files that use ThreadX services or data structures.</span></span>

1. <span data-ttu-id="f5bee-147">표준 C \***main** _ 함수를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-147">Create the standard C \***main** _ function.</span></span> <span data-ttu-id="f5bee-148">이 함수는 결과적으로 _ \*_tx_kernel_enter_\*\*를 호출하여 Threadx를 시작해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-148">This function must eventually call _ *_tx_kernel_enter_*\* to start ThreadX.</span></span> <span data-ttu-id="f5bee-149">ThreadX가 포함되지 않은 애플리케이션별 초기화는 커널에 들어가기 전에 추가될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-149">Application-specific initialization that does not involve ThreadX may be added prior to entering the kernel.</span></span>

      > [!IMPORTANT]
      > <span data-ttu-id="f5bee-150">\*ThreadX entry 함수 \***tx_kernel_enter** _는 반환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-150">\*The ThreadX entry function \***tx_kernel_enter** _ does not return.</span></span> <span data-ttu-id="f5bee-151">따라서 그 뒤에는 처리 또는 함수 호출을 배치하지 않아야 합니다._</span><span class="sxs-lookup"><span data-stu-id="f5bee-151">So be sure not to place any processing or function calls after it._</span></span>

1. <span data-ttu-id="f5bee-152">***tx_application_define*** 함수를 만듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-152">Create the ***tx_application_define*** function.</span></span> <span data-ttu-id="f5bee-153">초기 시스템 리소스가 만들어지는 위치입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-153">This is where the initial system resources are created.</span></span> <span data-ttu-id="f5bee-154">시스템 리소스의 예로는 스레드, 큐, 메모리 풀, 이벤트 플래그 그룹, 뮤텍스 및 세마포가 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-154">Examples of system resources include threads, queues, memory pools, event flags groups, mutexes, and semaphores.</span></span>

1. <span data-ttu-id="f5bee-155">애플리케이션 소스를 컴파일하고 ThreadX 런타임 라이브러리 ***tx.lib*** 에 연결합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-155">Compile application source and link with the ThreadX run-time library ***tx.lib***.</span></span> <span data-ttu-id="f5bee-156">결과 이미지를 대상에 다운로드하여 실행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-156">The resulting image can be downloaded to the target and executed!</span></span>

## <a name="small-example-system"></a><span data-ttu-id="f5bee-157">간단한 예제 시스템</span><span class="sxs-lookup"><span data-stu-id="f5bee-157">Small Example System</span></span>

<span data-ttu-id="f5bee-158">그림 1의 작은 예제 시스템은 우선 순위가 3인 단일 스레드의 생성을 보여 줍니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-158">The small example system in Figure 1 shows the creation of a single thread with a priority of 3.</span></span> <span data-ttu-id="f5bee-159">스레드가 실행되고, 카운터가 증가한 후 1번의 클럭 틱을 대기합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-159">The thread executes, increments a counter, then sleeps for one clock tick.</span></span>
<span data-ttu-id="f5bee-160">이 프로세스는 무한히 계속됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-160">This process continues forever.</span></span>

```c
#include "tx_api.h"
unsigned long my_thread_counter = 0;
TX_THREAD my_thread;
main( )
{
    /* Enter the ThreadX kernel. */
    tx_kernel_enter( );
}
void tx_application_define(void *first_unused_memory)
{
    /* Create my_thread! */
    tx_thread_create(&my_thread, "My Thread",
    my_thread_entry, 0x1234, first_unused_memory, 1024,
    3, 3, TX_NO_TIME_SLICE, TX_AUTO_START);
}
void my_thread_entry(ULONG thread_input)
{
    /* Enter into a forever loop. */
    while(1)
    {
        /* Increment thread counter. */
        my_thread_counter++;
        /* Sleep for 1 tick. */
        tx_thread_sleep(1);
    }
}
```

<span data-ttu-id="f5bee-161">**그림 1. 애플리케이션 개발용 템플릿**</span><span class="sxs-lookup"><span data-stu-id="f5bee-161">**FIGURE 1. Template for Application Development**</span></span>

<span data-ttu-id="f5bee-162">간단한 예제이지만 실제 애플리케이션 개발에 적합한 템플릿을 제공합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-162">Although this is a simple example, it provides a good template for real application development.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="f5bee-163">문제 해결</span><span class="sxs-lookup"><span data-stu-id="f5bee-163">Troubleshooting</span></span>

<span data-ttu-id="f5bee-164">각 ThreadX 포트는 데모 애플리케이션과 함께 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-164">Each ThreadX port is delivered with a demonstration application.</span></span> <span data-ttu-id="f5bee-165">항상 실제 대상 하드웨어 또는 시뮬레이트된 환경에서 데모 시스템을 실행하는 것이 좋습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-165">It is always a good idea to first get the demonstration system running—either on actual target hardware or simulated environment.</span></span>

<span data-ttu-id="f5bee-166">데모 시스템이 제대로 실행되지 않을 경우 다음의 문제 해결 팁을 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5bee-166">If the demonstration system does not execute properly, the following are some troubleshooting tips.</span></span>

1. <span data-ttu-id="f5bee-167">데모가 어느 정도 실행되고 있는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-167">Determine how much of the demonstration is running.</span></span>
1. <span data-ttu-id="f5bee-168">스택 크기를 늘립니다(데모보다는 실제 애플리케이션 코드에서 더 중요함).</span><span class="sxs-lookup"><span data-stu-id="f5bee-168">Increase stack sizes (this is more important in actual application code than it is for the demonstration).</span></span>
1. <span data-ttu-id="f5bee-169">TX_ENABLE_STACK_CHECKING을 정의하여 ThreadX 라이브러리를 다시 빌드합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-169">Rebuild the ThreadX library with TX_ENABLE_STACK_CHECKING defined.</span></span> <span data-ttu-id="f5bee-170">이렇게 하면 기본 제공 ThreadX 스택 검사가 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-170">This enables the built-in ThreadX stack checking.</span></span>
1. <span data-ttu-id="f5bee-171">최근 변경 내용을 일시적으로 무시하여 문제가 사라지거나 변경되는지 확인합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-171">Temporarily bypass any recent changes to see if the problem disappears or changes.</span></span> <span data-ttu-id="f5bee-172">이러한 정보는 지원 엔지니어에게 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-172">Such information should prove useful to support engineers.</span></span>

<span data-ttu-id="f5bee-173">"[고객 지원 센터](about-this-guide.md#customer-support-center)"에 설명된 절차에 따라 문제 해결 단계에서 수집한 정보를 보냅니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-173">Follow the procedures outlined in "[Customer Support Center](about-this-guide.md#customer-support-center)" to send the information gathered from the troubleshooting steps.</span></span>

## <a name="configuration-options"></a><span data-ttu-id="f5bee-174">구성 옵션</span><span class="sxs-lookup"><span data-stu-id="f5bee-174">Configuration Options</span></span>

<span data-ttu-id="f5bee-175">ThreadX를 사용하여 ThreadX 라이브러리 및 애플리케이션을 빌드할 때 몇 가지 구성 옵션이 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-175">There are several configuration options when building the ThreadX library and the application using ThreadX.</span></span> <span data-ttu-id="f5bee-176">아래 옵션은 애플리케이션 소스, 명령줄 또는 ***tx_user.h*** 포함 파일 내에서 정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-176">The options below can be defined in the application source, on the command line, or within the ***tx_user.h*** include file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="f5bee-177">\*\*\*\*tx_user.h\*\* _에 정의된 옵션은 애플리케이션 및 ThreadX 라이브러리가 *TX_INCLUDE_USER_DEFINE_FILE* 이 정의된 상태로 빌드된 경우에만 적용됩니다.\*</span><span class="sxs-lookup"><span data-stu-id="f5bee-177">*Options defined in ***tx_user.h** _ are applied only if the application and ThreadX library are built with _ *TX_INCLUDE_USER_DEFINE_FILE** defined.*</span></span>

### <a name="smallest-configuration"></a><span data-ttu-id="f5bee-178">가장 작은 구성</span><span class="sxs-lookup"><span data-stu-id="f5bee-178">Smallest Configuration</span></span>

<span data-ttu-id="f5bee-179">가장 작은 코드 크기의 경우 다음 ThreadX 구성 옵션을 고려해야 합니다(다른 모든 옵션은 없음).</span><span class="sxs-lookup"><span data-stu-id="f5bee-179">For the smallest code size, the following ThreadX configuration options should be considered (in absence of all other options).</span></span>

```c
TX_DISABLE_ERROR_CHECKING
TX_DISABLE_PREEMPTION_THRESHOLD
TX_DISABLE_NOTIFY_CALLBACKS
TX_DISABLE_REDUNDANT_CLEARING
TX_DISABLE_STACK_FILLING
TX_NOT_INTERRUPTABLE
TX_TIMER_PROCESS_IN_ISR
```

### <a name="fastest-configuration"></a><span data-ttu-id="f5bee-180">가장 빠른 구성</span><span class="sxs-lookup"><span data-stu-id="f5bee-180">Fastest Configuration</span></span>

<span data-ttu-id="f5bee-181">가장 빠른 실행을 위해 이전의 가장 작은 구성에 사용되는 것과 동일한 구성 옵션이 사용되지만 다음 옵션을 고려할 수도 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-181">For the fastest execution, the same configuration options used for the Smallest Configuration previously, but with these options also considered.</span></span>

```c
TX_REACTIVATE_INLINE
TX_INLINE_THREAD_RESUME_SUSPEND
```

<span data-ttu-id="f5bee-182">[자세한 구성 옵션](#detailed-configuration-options)이 제공됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-182">[Detailed configuration options](#detailed-configuration-options) are described.</span></span>

### <a name="global-time-source"></a><span data-ttu-id="f5bee-183">글로벌 시간 소스</span><span class="sxs-lookup"><span data-stu-id="f5bee-183">Global Time Source</span></span>

<span data-ttu-id="f5bee-184">다른 Microsoft Azure RTOS 제품(FileX, NetX, GUIX, USBX 등)의 경우 ThreadX는 1초를 나타내는 ThreadX 타이머 틱 수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-184">For other Microsoft Azure RTOS products (FileX, NetX, GUIX, USBX, etc.), ThreadX defines the number of ThreadX timer ticks that represents one second.</span></span> <span data-ttu-id="f5bee-185">다른 제품을 사용할 때는 이 상수를 기준으로 다른 시간 요구 사항이 발생합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-185">Others derive their time requirements based on this constant.</span></span> <span data-ttu-id="f5bee-186">기본적으로 이 값은 100으로, 10ms의 정기적 인터럽트가 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-186">By default, the value is 100, assuming a 10ms periodic interrupt.</span></span> <span data-ttu-id="f5bee-187">사용자는 ***tx_port.h*** 에서 또는 IDE나 명령줄 내에서 원하는 값으로 TX_TIMER_TICKS_PER_SECOND를 정의하여 이 값을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-187">The user may override this value by defining TX_TIMER_TICKS_PER_SECOND with the desired value in ***tx_port.h*** or within the IDE or command line.</span></span>

### <a name="detailed-configuration-options"></a><span data-ttu-id="f5bee-188">자세한 구성 옵션</span><span class="sxs-lookup"><span data-stu-id="f5bee-188">Detailed Configuration Options</span></span>

<span data-ttu-id="f5bee-189">**TX_BYTE_POOL_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-189">**TX_BYTE_POOL_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-190">정의된 경우 이 옵션을 사용하여 바이트 풀에서 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-190">When defined, this option enables the gathering of performance information on byte pools.</span></span> <span data-ttu-id="f5bee-191">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-191">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-192">**TX_BYTE_POOL_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-192">**TX_BYTE_POOL_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-193">정의된 경우 이 옵션을 사용하여 바이트 풀에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-193">When defined, this option enables the gathering of performance information on byte pools.</span></span> <span data-ttu-id="f5bee-194">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-194">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-195">**TX_DISABLE_ERROR_CHECKING**</span><span class="sxs-lookup"><span data-stu-id="f5bee-195">**TX_DISABLE_ERROR_CHECKING**</span></span>

<span data-ttu-id="f5bee-196">기본 서비스 호출 오류 검사를 무시합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-196">Bypasses basic service call error checking.</span></span> <span data-ttu-id="f5bee-197">애플리케이션 소스에 정의된 경우 모든 기본 매개 변수 오류 검사가 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-197">When defined in the application source, all basic parameter error checking is disabled.</span></span> <span data-ttu-id="f5bee-198">이렇게 하면 성능이 30% 정도 향상되고 이미지 크기도 줄어들 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-198">This may improve performance by as much as 30% and may also reduce the image size.</span></span>

> [!NOTE]
> <span data-ttu-id="f5bee-199">애플리케이션이 외부 입력에서 파생된 입력 매개 변수를 비롯하여 모든 입력 매개 변수가 항상 유효할 것임을 절대적으로 보장할 수 있는 경우에만 오류 검사를 사용하지 않도록 설정하는 것이 안전합니다. 오류 검사를 사용하지 않도록 설정하고 API에 잘못된 입력을 제공하면 결과 동작은 정의되지 않으며 메모리 손상이나 시스템 충돌을 야기할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-199">*It is only safe to disable error checking if the application can absolutely guarantee all input parameters are always valid under all circumstances, including input parameters derived from external input. If invalid input is supplied to the API with error checking disabled, the resulting behavior is undefined and could result in memory corruption or system crash.*</span></span>

> [!NOTE]
> <span data-ttu-id="f5bee-200">*오류 검사를 사용하지 않도록 설정해도 영향을 받지 않는 ThreadX API 반환 값은 4장의 각 API 설명에 대한 "반환 값" 섹션에서 굵게 표시됩니다. TX_DISABLE_ERROR_CHECKING 옵션을 사용하여 오류 검사를 사용하지 않도록 설정한 경우 굵게 표시되지 않은 반환 값은 무효 처리됩니다.*</span><span class="sxs-lookup"><span data-stu-id="f5bee-200">*ThreadX API return values not affected by disabling error checking are listed in bold in the “Return Values” section of each API description in Chapter 4. The nonbold return values are void if error checking is disabled by using the TX_DISABLE_ERROR_CHECKING option.*</span></span>

<span data-ttu-id="f5bee-201">**TX_DISABLE_NOTIFY_CALLBACKS**</span><span class="sxs-lookup"><span data-stu-id="f5bee-201">**TX_DISABLE_NOTIFY_CALLBACKS**</span></span>

<span data-ttu-id="f5bee-202">정의되면 다양 한 ThreadX 개체에 대해 알림 콜백이 사용하지 않도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-202">When defined, disables the notify callbacks for various ThreadX objects.</span></span> <span data-ttu-id="f5bee-203">이 옵션을 사용하면 코드 크기가 약간 줄어들고 성능이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-203">Using this option slightly reduces code size and improves performance.</span></span> <span data-ttu-id="f5bee-204">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-204">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-205">**TX_DISABLE_PREEMPTION_THRESHOLD**</span><span class="sxs-lookup"><span data-stu-id="f5bee-205">**TX_DISABLE_PREEMPTION_THRESHOLD**</span></span>

<span data-ttu-id="f5bee-206">정의되면 선점 임계값 기능이 사용하지 않도록 설정되고 코드 크기가 약간 줄어들며 성능이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-206">When defined, disables the preemption-threshold feature and slightly reduces code size and improves performance.</span></span> <span data-ttu-id="f5bee-207">물론, 선점 임계값 기능은 더 이상 사용할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-207">Of course, the preemption-threshold capabilities are no longer available.</span></span> <span data-ttu-id="f5bee-208">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-208">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-209">**TX_DISABLE_REDUNDANT_CLEARING**</span><span class="sxs-lookup"><span data-stu-id="f5bee-209">**TX_DISABLE_REDUNDANT_CLEARING**</span></span>

<span data-ttu-id="f5bee-210">정의되면 ThreadX 글로벌 C 데이터 구조를 0으로 초기화하는 논리가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-210">When defined, removes the logic for initializing ThreadX global C data structures to zero.</span></span> <span data-ttu-id="f5bee-211">이 옵션은 컴파일러의 초기화 코드가 초기화되지 않은 모든 C 전역 데이터를 0으로 설정하는 경우에만 사용해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-211">This should only be used if the compiler's initialization code sets all un-initialized C global data to zero.</span></span> <span data-ttu-id="f5bee-212">이 옵션을 사용하면 초기화 중에 코드 크기가 약간 줄어들고 성능이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-212">Using this option slightly reduces code size and improves performance during initialization.</span></span> <span data-ttu-id="f5bee-213">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-213">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-214">**TX_DISABLE_STACK_FILLING**</span><span class="sxs-lookup"><span data-stu-id="f5bee-214">**TX_DISABLE_STACK_FILLING**</span></span>

<span data-ttu-id="f5bee-215">정의되면 생성될 때 각 스레드 스택의 각 바이트에 0xEF 값을 배치할 수 없습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-215">When defined, disables placing the 0xEF value in each byte of each thread's stack when created.</span></span> <span data-ttu-id="f5bee-216">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-216">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-217">**TX_ENABLE_EVENT_TRACE**</span><span class="sxs-lookup"><span data-stu-id="f5bee-217">**TX_ENABLE_EVENT_TRACE**</span></span>

<span data-ttu-id="f5bee-218">정의되면 ThreadX는 TraceX 추적 버퍼를 만들기 위한 이벤트 수집 코드를 사용하도록 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-218">When defined, ThreadX enables the event gathering code for creating a TraceX trace buffer.</span></span>

<span data-ttu-id="f5bee-219">**TX_ENABLE_STACK_CHECKING**</span><span class="sxs-lookup"><span data-stu-id="f5bee-219">**TX_ENABLE_STACK_CHECKING**</span></span>

<span data-ttu-id="f5bee-220">정의되면 사용된 스택 양에 대한 분석과 스택 영역 앞뒤에 있는 데이터 패턴 "펜스"의 검사를 포함하는 ThreadX 런타임 스택 검사가 사용하도록 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-220">When defined, enables ThreadX run-time stack checking, which includes analysis of how much stack has been used and examination of data pattern "fences" before and after the stack area.</span></span> <span data-ttu-id="f5bee-221">스택 오류가 검색되면 등록된 애플리케이션 스택 오류 처리기가 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-221">If a stack error is detected, the registered application stack error handler is called.</span></span> <span data-ttu-id="f5bee-222">이 옵션을 선택하면 오버헤드 및 코드 크기가 약간 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-222">This option does result in slightly increased overhead and code size.</span></span> <span data-ttu-id="f5bee-223">자세한 내용은 ***tx_thread_stack_error_notify*** API 함수를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5bee-223">Review the ***tx_thread_stack_error_notify*** API function for more information.</span></span> <span data-ttu-id="f5bee-224">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-224">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-225">**TX_EVENT_FLAGS_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-225">**TX_EVENT_FLAGS_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-226">정의되면 이벤트 플래그 그룹에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-226">When defined, enables the gathering of performance information on event flags groups.</span></span> <span data-ttu-id="f5bee-227">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-227">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-228">**TX_INLINE_THREAD_RESUME_SUSPEND**</span><span class="sxs-lookup"><span data-stu-id="f5bee-228">**TX_INLINE_THREAD_RESUME_SUSPEND**</span></span>

<span data-ttu-id="f5bee-229">정의되면 ThreadX는 인라인 코드를 통해 ***tx_thread_resume** _ 및 _ *_tx_thread_suspend_** API 호출을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-229">When defined, ThreadX improves the ***tx_thread_resume** _ and _ *_tx_thread_suspend_** API calls via in-line code.</span></span> <span data-ttu-id="f5bee-230">이렇게 하면 코드 크기는 늘어나지만 이러한 두 API 호출의 성능이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-230">This increases code size but enhances performance of these two API calls.</span></span>

<span data-ttu-id="f5bee-231">**TX_MAX_PRIORITIES**</span><span class="sxs-lookup"><span data-stu-id="f5bee-231">**TX_MAX_PRIORITIES**</span></span>

<span data-ttu-id="f5bee-232">ThreadX에 대한 우선 순위 수준을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-232">Defines the priority levels for ThreadX.</span></span> <span data-ttu-id="f5bee-233">유효한 값의 범위는 32에서 1024(경계값 포함) 사이이며 32씩 균등하게 *나눌 수 있어야 합니다*.</span><span class="sxs-lookup"><span data-stu-id="f5bee-233">Legal values range from 32 through 1024 (inclusive) and *must* be evenly divisible by 32.</span></span> <span data-ttu-id="f5bee-234">지원되는 우선 순위 수준 수를 늘리면 32개 우선 순위 그룹마다 RAM 사용량이 128바이트씩 늘어납니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-234">Increasing the number of priority levels supported increases the RAM usage by 128 bytes for every group of 32 priorities.</span></span> <span data-ttu-id="f5bee-235">그러나 성능에는 거의 영향을 주지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-235">However, there is only a negligible effect on performance.</span></span> <span data-ttu-id="f5bee-236">기본적으로 이 값은 32개 우선 순위 수준으로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-236">By default, this value is set to 32 priority levels.</span></span>

<span data-ttu-id="f5bee-237">**TX_MINIMUM_STACK**</span><span class="sxs-lookup"><span data-stu-id="f5bee-237">**TX_MINIMUM_STACK**</span></span>

<span data-ttu-id="f5bee-238">최소 스택 크기(바이트)를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-238">Defines the minimum stack size (in bytes).</span></span> <span data-ttu-id="f5bee-239">스레드를 만들 때 오류 검사에 사용됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-239">It is used for error checking when threads are created.</span></span> <span data-ttu-id="f5bee-240">기본값은 포트 전용이며 ***tx_port.h*** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-240">The default value is port-specific and is found in ***tx_port.h***.</span></span>

<span data-ttu-id="f5bee-241">**TX_MISRA_ENABLE**</span><span class="sxs-lookup"><span data-stu-id="f5bee-241">**TX_MISRA_ENABLE**</span></span>

<span data-ttu-id="f5bee-242">정의되면 ThreadX는 MISRA C 호환 규칙을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-242">When defined, ThreadX utilizes MISRA C compliant conventions.</span></span> <span data-ttu-id="f5bee-243">자세한 내용은 ***ThreadX_MISRA_Compliance.pdf*** 를 참조하세요.</span><span class="sxs-lookup"><span data-stu-id="f5bee-243">Refer to  ***ThreadX_MISRA_Compliance.pdf*** for details.</span></span>

<span data-ttu-id="f5bee-244">**TX_MUTEX_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-244">**TX_MUTEX_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-245">정의되면 뮤텍스에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-245">When defined, enables the gathering of performance information on mutexes.</span></span> <span data-ttu-id="f5bee-246">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-246">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-247">**TX_NO_TIMER**</span><span class="sxs-lookup"><span data-stu-id="f5bee-247">**TX_NO_TIMER**</span></span>

<span data-ttu-id="f5bee-248">정의되면 ThreadX 타이머 논리를 완전히 사용할 수 없게 됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-248">When defined, the ThreadX timer logic is completely disabled.</span></span> <span data-ttu-id="f5bee-249">이 옵션은 ThreadX 타이머 기능(스레드 절전, API 시간 제한, 시간 조각화 및 애플리케이션 타이머)이 사용되지 않는 경우에 유용합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-249">This is useful in cases where the ThreadX timer features (thread sleep, API timeouts, time-slicing, and application timers) are not utilized.</span></span> <span data-ttu-id="f5bee-250">**TX_NO_TIMER** 가 지정되면 **TX_TIMER_PROCESS_IN_ISR** 옵션도 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-250">If **TX_NO_TIMER** is specified, the option **TX_TIMER_PROCESS_IN_ISR** must also be defined.</span></span>

<span data-ttu-id="f5bee-251">**TX_NOT_INTERRUPTABLE**</span><span class="sxs-lookup"><span data-stu-id="f5bee-251">**TX_NOT_INTERRUPTABLE**</span></span>

<span data-ttu-id="f5bee-252">정의되면 ThreadX는 인터럽트 잠금 시간을 최소화하려고 시도하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-252">When defined, ThreadX does not attempt to minimize interrupt lockout time.</span></span> <span data-ttu-id="f5bee-253">이로 인해 실행 속도가 빨라지지만 인터럽트 잠금 시간이 약간 늘어납니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-253">This results in faster execution but does slightly increase interrupt lockout time.</span></span>

<span data-ttu-id="f5bee-254">**TX_QUEUE_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-254">**TX_QUEUE_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-255">정의되면 큐에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-255">When defined, enables the gathering of performance information on queues.</span></span> <span data-ttu-id="f5bee-256">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-256">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-257">**TX_REACTIVATE_INLINE**</span><span class="sxs-lookup"><span data-stu-id="f5bee-257">**TX_REACTIVATE_INLINE**</span></span>

<span data-ttu-id="f5bee-258">정의되면 함수 호출을 사용하는 대신 ThreadX 타이머 인라인이 다시 활성화됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-258">When defined, performs reactivation of ThreadX timers inline instead of using a function call.</span></span> <span data-ttu-id="f5bee-259">이렇게 하면 성능은 향상되지만 코드 크기는 약간 더 커집니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-259">This improves performance but slightly increases code size.</span></span> <span data-ttu-id="f5bee-260">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-260">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-261">**TX_SEMAPHORE_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-261">**TX_SEMAPHORE_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-262">정의되면 세마포에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-262">When defined, enables the gathering of performance information on semaphores.</span></span> <span data-ttu-id="f5bee-263">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-263">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-264">**TX_THREAD_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-264">**TX_THREAD_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-265">정의되면 스레드에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-265">When defined, enables the gathering of performance information on threads.</span></span> <span data-ttu-id="f5bee-266">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-266">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-267">**TX_TIMER_ENABLE_PERFORMANCE_INFO**</span><span class="sxs-lookup"><span data-stu-id="f5bee-267">**TX_TIMER_ENABLE_PERFORMANCE_INFO**</span></span>

<span data-ttu-id="f5bee-268">정의되면 타이머에 대한 성능 정보를 수집할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-268">When defined, enables the gathering of performance information on timers.</span></span> <span data-ttu-id="f5bee-269">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-269">By default, this option is not defined.</span></span>

<span data-ttu-id="f5bee-270">**TX_TIMER_PROCESS_IN_ISR**</span><span class="sxs-lookup"><span data-stu-id="f5bee-270">**TX_TIMER_PROCESS_IN_ISR**</span></span>

<span data-ttu-id="f5bee-271">정의되면 ThreadX에 대한 내부 시스템 타이머 스레드가 제거됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-271">When defined, eliminates the internal system timer thread for ThreadX.</span></span> <span data-ttu-id="f5bee-272">이로 인해 타이머 스택과 제어 블록이 더 이상 필요하지 않기 때문에 타이머 이벤트에 대한 성능이 향상되고 RAM 요구 사항은 더 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-272">This results in improved performance on timer events and smaller RAM requirements because the timer stack and control block are no longer needed.</span></span> <span data-ttu-id="f5bee-273">그러나 이 옵션을 사용하면 모든 타이머 만료 처리가 타이머 ISR 수준으로 이동됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-273">However, using this option moves all the timer expiration processing to the timer ISR level.</span></span> <span data-ttu-id="f5bee-274">이 옵션은 기본적으로 정의되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-274">By default, this option is not defined.</span></span>

> [!NOTE]
> <span data-ttu-id="f5bee-275">타이머에서 허용되는 서비스는 ISR에서 허용되지 않을 수 있으므로 이 옵션을 사용하는 경우 허용되지 않을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-275">*Services allowed from timers may not be allowed from ISRs and thus might not be allowed when using this option.*</span></span>

<span data-ttu-id="f5bee-276">**TX_TIMER_THREAD_PRIORITY**</span><span class="sxs-lookup"><span data-stu-id="f5bee-276">**TX_TIMER_THREAD_PRIORITY**</span></span>

<span data-ttu-id="f5bee-277">내부 ThreadX 시스템 타이머 스레드의 우선 순위를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-277">Defines the priority of the internal ThreadX system timer thread.</span></span> <span data-ttu-id="f5bee-278">기본값은 ThreadX에서 가장 높은 우선 순위를 나타내는 우선 순위 0입니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-278">The default value is priority 0—the highest priority in ThreadX.</span></span> <span data-ttu-id="f5bee-279">기본값은 ***tx_port.h*** 에 정의되어 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-279">The default value is defined in ***tx_port.h***.</span></span>

<span data-ttu-id="f5bee-280">**TX_TIMER_THREAD_STACK_SIZE**</span><span class="sxs-lookup"><span data-stu-id="f5bee-280">**TX_TIMER_THREAD_STACK_SIZE**</span></span>

<span data-ttu-id="f5bee-281">내부 ThreadX 시스템 타이머 스레드의 스택 크기(바이트)를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-281">Defines the stack size (in bytes) of the internal ThreadX system timer thread.</span></span> <span data-ttu-id="f5bee-282">이 스레드는 모든 서비스 호출 시간 제한뿐만 아니라 모든 스레드 대기 요청을 처리합니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-282">This thread processes all thread sleep requests as well as all service call timeouts.</span></span> <span data-ttu-id="f5bee-283">또한 모든 애플리케이션 타이머 콜백 루틴은 이 컨텍스트에서 호출됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-283">In addition, all application timer callback routines are invoked from this context.</span></span> <span data-ttu-id="f5bee-284">기본값은 포트 전용이며 ***tx_port.h*** 에서 찾을 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-284">The default value is port-specific and is found in ***tx_port.h***.</span></span>

## <a name="threadx-version-id"></a><span data-ttu-id="f5bee-285">ThreadX 버전 ID</span><span class="sxs-lookup"><span data-stu-id="f5bee-285">ThreadX Version ID</span></span>

<span data-ttu-id="f5bee-286">프로그래머는 \***tx_port.h** _ 파일을 조사하여 ThreadX 버전을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-286">The programmer can obtain the ThreadX version from examination of the \***tx_port.h** _ file.</span></span> <span data-ttu-id="f5bee-287">또한 이 파일에는 해당 포트의 버전 기록도 포함됩니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-287">In addition, this file also contains a version history of the corresponding port.</span></span> <span data-ttu-id="f5bee-288">애플리케이션 소프트웨어는 전역 문자열 _\*_tx_version_id\*\*를 검사하여 ThreadX 버전을 가져올 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="f5bee-288">Application software can obtain the ThreadX version by examining the global string _\*_tx_version_id\*\*.</span></span>