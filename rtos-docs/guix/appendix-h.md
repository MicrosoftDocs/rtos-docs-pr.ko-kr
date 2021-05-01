---
title: 부록 H - GUIX 빌드 시간 구성 플래그
description: GUIX 빌드 시간 구성 플래그에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 05/19/2020
ms.topic: article
ms.service: rtos
ms.openlocfilehash: 65095e326bc6809eba6e9472e2d74325351354ca
ms.sourcegitcommit: e3d42e1f2920ec9cb002634b542bc20754f9544e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104811232"
---
# <a name="appendix-h---guix-build-time-configuration-flags"></a><span data-ttu-id="6ee08-103">부록 H - GUIX 빌드 시간 구성 플래그</span><span class="sxs-lookup"><span data-stu-id="6ee08-103">Appendix H - GUIX Build-Time Configuration flags</span></span>

<span data-ttu-id="6ee08-104">GUIX는 여러 조건부 컴파일 옵션과 구성 값을 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-104">GUIX support several conditional compilation options and configuration values.</span></span> <span data-ttu-id="6ee08-105">gx_user.h 헤더 파일 또는 컴파일러 명령줄에서 값을 미리 정의하여 조건부와 구성 값의 기본 설정을 재정의할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-105">The default setting for these conditionals and configuration values can be overridden by pre-defining the value, either in your gx_user.h header file or on your compiler command line.</span></span>

<span data-ttu-id="6ee08-106">**GX_DISABLE_THREADX_BINDING**</span><span class="sxs-lookup"><span data-stu-id="6ee08-106">**GX_DISABLE_THREADX_BINDING**</span></span>
- <span data-ttu-id="6ee08-107">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-107">Default: Undefined</span></span>
- <span data-ttu-id="6ee08-108">설명: 이 조건부를 사용하여 기본 ThreadX RTOS 바인딩을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-108">Description: This conditional can be used to disable the default ThreadX RTOS binding.</span></span> <span data-ttu-id="6ee08-109">ThreadX가 아닌 RTOS를 사용하여 GUIX를 실행하려면 #define GX_DISABLE_THREADX_BINDING을 사용하고 사용자 고유의 RTOS 바인딩 서비스를 제공해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-109">If you want to run GUIX with an RTOS other than ThreadX, you should #define GX_DISABLE_THREADX_BINDING and provide your own RTOS binding services.</span></span>

<span data-ttu-id="6ee08-110">GX_SYSTEM_TIMER_MS</span><span class="sxs-lookup"><span data-stu-id="6ee08-110">GX_SYSTEM_TIMER_MS</span></span>
- <span data-ttu-id="6ee08-111">기본값: 20</span><span class="sxs-lookup"><span data-stu-id="6ee08-111">Default: 20</span></span>
- <span data-ttu-id="6ee08-112">설명: 이 값은 원하는 GUIX 타이머 간격이나 정밀도를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-112">Description: This value defines the desired GUIX timer interval or precision.</span></span>

<span data-ttu-id="6ee08-113">TX_TIMER_TICKS_PER_SECOND</span><span class="sxs-lookup"><span data-stu-id="6ee08-113">TX_TIMER_TICKS_PER_SECOND</span></span>
- <span data-ttu-id="6ee08-114">Default: 100</span><span class="sxs-lookup"><span data-stu-id="6ee08-114">Default: 100</span></span>
- <span data-ttu-id="6ee08-115">설명: 이 값은 TX 타이머 인터럽트 진동 수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-115">Description: This value defines the number of TX timer interrupt frequences.</span></span> <span data-ttu-id="6ee08-116">기본 ThreadX 간격 타이머가 10ms이므로 이 값은 기본적으로 100Hz 주파수로 설정됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-116">Since the default ThreadX interval timer is 10 ms, this value defaults to a 100-Hz frequency.</span></span>

<span data-ttu-id="6ee08-117">GX_SYSTEM_TIMER_TICKS</span><span class="sxs-lookup"><span data-stu-id="6ee08-117">GX_SYSTEM_TIMER_TICKS</span></span>
- <span data-ttu-id="6ee08-118">기본값: ((GX_SYSTEM_TIMER_MS \* TX_TIMER_TICKS_PER_SECOND)/1000)</span><span class="sxs-lookup"><span data-stu-id="6ee08-118">Default: ((GX_SYSTEM_TIMER_MS \* TX_TIMER_TICKS_PER_SECOND)/1000)</span></span>
- <span data-ttu-id="6ee08-119">설명: 이 값은 GUIX 타이머 틱당 기본 RTOS 타이머 틱 수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-119">Description: This value defines the number of underlying RTOS timer ticks per GUIX timer tick.</span></span> <span data-ttu-id="6ee08-120">기본값은 2입니다. 즉, GUIX 타이머 간격은 기본적으로 ThreadX 타이머 인터럽트 간격 2개 또는 20ms입니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-120">The default value is 2, meaning the GUIX timer interval is 2 ThreadX timer interrupt intervals, or 20 ms by default.</span></span>

<span data-ttu-id="6ee08-121">GX_DISABLE_MULTITHREAD_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-121">GX_DISABLE_MULTITHREAD_SUPPORT</span></span>
- <span data-ttu-id="6ee08-122">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-122">Default: Not defined</span></span>
- <span data-ttu-id="6ee08-123">설명: 이 컴파일 시간 조건부를 사용하여 GUIX API를 동시에 호출하는 여러 스레드에 대해 GUIX API 지원을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-123">Description: This compile-time conditional can be used to disable the GUIX API support for multiple threads invoking the GUIX API concurrently.</span></span> <span data-ttu-id="6ee08-124">하나의 애플리케이션 스레드만 GUIX API를 활용하는 경우 이 플래그를 정의하여 중요한 코드 섹션 보호와 관련된 시스템 오버헤드를 줄여야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-124">If only one application thread will ever utilize the GUIX API, you should define this flag to reduce the system overhead associated with protecting critical code sections.</span></span>

<span data-ttu-id="6ee08-125">GX_DISABLE_UTF8_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-125">GX_DISABLE_UTF8_SUPPORT</span></span>
- <span data-ttu-id="6ee08-126">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-126">Default: Not Defined.</span></span>
- <span data-ttu-id="6ee08-127">설명: 이 컴파일 시간 조건부를 사용하여 UTF8 형식 문자열 인코딩에 대한 GUIX 내부 지원을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-127">Description: This compile-time conditional can be used to remove the GUIX internal support for UTF8 format string encoding.</span></span> <span data-ttu-id="6ee08-128">애플리케이션에서 문자 값 M-0xff만 사용하는 경우 이 #define을 설정하면 UTF8 형식 문자열 인코딩 지원과 관련된 코드 크기와 오버헤드가 줄어듭니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-128">If you are using only character values M- 0xff in your application, turning on this #define will reduce the code size and overhead associated with supporting UTF8 format string encoding.</span></span>

<span data-ttu-id="6ee08-129">GX_DISABLE_ARC_DRAWING_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-129">GX_DISABLE_ARC_DRAWING_SUPPORT</span></span>
- <span data-ttu-id="6ee08-130">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-130">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-131">설명: 이 조건부를 사용하여 원호 그리기 함수 circle, arc, pie, ellipse에 대한 지원을 제거하면 GUIX 라이브러리 코드 크기와 GX_DISPLAY 구조체 크기를 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-131">Description: This conditional can be used to reduce the GUIX library code size and GX_DISPLAY structure size by removing support for the arc-drawing functions circle, arc, pie, and ellipse.</span></span> <span data-ttu-id="6ee08-132">이 함수는 기본 GUIX 위젯 세트에 필요하지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-132">These functions are not required by the default GUIX widget set.</span></span>

<span data-ttu-id="6ee08-133">GX_DISABLE_SOFTWARE_DECODER_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-133">GX_DISABLE_SOFTWARE_DECODER_SUPPORT</span></span>
- <span data-ttu-id="6ee08-134">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-134">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-135">설명: 이 조건부를 정의하여 GUIX 라이브러리 런타임 jpeg 및 png 소프트웨어 디코더 지원을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-135">Description: This conditional can be defined to remove the GUIX library runtime jpeg and png software decoder support.</span></span> <span data-ttu-id="6ee08-136">애플리케이션이 Studio에서 생성된 RAW 형식 pixelmap을 사용하지 않고 외부 파일 시스템에서 이미지 파일을 읽어오지 않아 애플리케이션에서 jpg 또는 png 파일의 런타임 디코드를 요구하지 않는 경우에는 이 #define을 설정하여 GUIX 라이브러리 공간을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-136">If your application does not require runtime decode of jpg or png files meaning your application does not use RAW format pixelmaps produced by Studio and does not read image files from an external filesystem, you can turn on this #define to reduce the GUIX library footprint.</span></span>

<span data-ttu-id="6ee08-137">GX_DISABLE_BINARY_RESOURCE_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-137">GX_DISABLE_BINARY_RESOURCE_SUPPORT</span></span>
- <span data-ttu-id="6ee08-138">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-138">Default: Not defined</span></span>
- <span data-ttu-id="6ee08-139">설명: 이 조건부를 사용하여 이진 리소스 데이터 로드에 대한 GUIX 라이브러리 지원을 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-139">Description: This conditional can be used to remove the GUIX library support for loading binary resource data.</span></span> <span data-ttu-id="6ee08-140">이진 리소스를 사용하면 GUIX 애플리케이션과 리소스 데이터의 런타임 바인딩을 수행할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-140">Binary resources can be used to do runtime binding of resource data with your GUIX application.</span></span> <span data-ttu-id="6ee08-141">C 소스 코드 형식 리소스 파일만 사용하는 경우 이 조건부를 정의하여 GUIX 라이브러리 공간을 줄일 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-141">If you are using only C source code format resource files, you can define this conditional to reduce your GUIX library footprint.</span></span>

<span data-ttu-id="6ee08-142">GX_DISABLE_BRUSH_ALPHA_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-142">GX_DISABLE_BRUSH_ALPHA_SUPPORT</span></span>
- <span data-ttu-id="6ee08-143">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-143">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-144">설명: 16bpp 이상의 색 농도로 실행하는 경우 GUIX는 필요에 따라 드로잉 컨텍스트 브러시에서 정의된 알파 값을 사용하여 비원호 그래픽, pixelmap, 글꼴을 그릴 수 있도록 지원합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-144">Description: When running at 16 bpp and higher color depths, GUIX optionally supports drawing non-arc graphics, pixelmaps, and fonts with an alpha value defined by the drawing context brush.</span></span> <span data-ttu-id="6ee08-145">이 그리기 모드를 지원하면 런타임 오버헤드와 라이브러리 공간이 약간 증가합니다. 알파 혼합 그리기 지원이 필요하지 않은 경우 이 플래그를 정의하여 제거할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-145">Supporting this drawing mode introduces a small runtime overhead and library footprint increase, which can be eliminated by defining this flag if you do not require alpha-blending drawing support.</span></span> <span data-ttu-id="6ee08-146">알파 채널, 앤티앨리어싱된 글꼴, 기타 앤티앨리어싱 그리기 모드를 사용하는 pixelmap은 이 조건부 설정에 관계없이 계속 지원됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-146">Note that pixelmaps with alpha channel, anti-aliased fonts, and other anti-aliasing drawing modes are still supported regardless of this conditional setting.</span></span>

<span data-ttu-id="6ee08-147">GX_DISABLE_THREADX_TIMER_SOURCE</span><span class="sxs-lookup"><span data-stu-id="6ee08-147">GX_DISABLE_THREADX_TIMER_SOURCE</span></span>
- <span data-ttu-id="6ee08-148">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-148">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-149">설명: 이 조건부를 사용하여 ThreadX 타이머 원본을 사용하지 않도록 설정할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-149">Description: This conditional can be used to disable the ThreadX timer source.</span></span> <span data-ttu-id="6ee08-150">다른 타이머 원본을 사용하려면 GX_DISABLE_THREADX_TIMER_SOURCE를 정의해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-150">You should define GX_DISABLE_THREADX_TIMER_SOURCE if you want to use a different timer source.</span></span>

<span data-ttu-id="6ee08-151">GX_REPEAT_BUTTON_INITIAL_TICS</span><span class="sxs-lookup"><span data-stu-id="6ee08-151">GX_REPEAT_BUTTON_INITIAL_TICS</span></span>
- <span data-ttu-id="6ee08-152">기본값은 10입니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-152">Default: 10.</span></span>
- <span data-ttu-id="6ee08-153">설명: 단추에 GX_STYLE_BUTTON_REPEAT 스타일이 있는 경우 이 값은 GX_EVENT_CLICKED 반복 이벤트 전송을 시작하기 전에 단추가 대기하는 기간을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-153">Description: If a button has style GX_STYLE_BUTTON_REPEAT, this value defines how long the button waits before beginning to send repeated GX_EVENT_CLICKED events.</span></span>

<span data-ttu-id="6ee08-154">GX_MAX_QUEUE_EVENTS</span><span class="sxs-lookup"><span data-stu-id="6ee08-154">GX_MAX_QUEUE_EVENTS</span></span>
- <span data-ttu-id="6ee08-155">기본값: 48</span><span class="sxs-lookup"><span data-stu-id="6ee08-155">Default: 48.</span></span>
- <span data-ttu-id="6ee08-156">설명: GUIX 이벤트 큐의 크기를 이벤트 구조 항목 단위로 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-156">Description: Defines the size of the GUIX event queue in units of event structure entries.</span></span> <span data-ttu-id="6ee08-157">이벤트 큐가 오버플로되면 큐에 푸시되는 이벤트는 무시되고 gx_system_event_send() 함수에서 GX_SYSTEM_ERROR가 반환됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-157">If the event queue overflows, events being pushed into the queue are discarded and GX_SYSTEM_ERROR is returned by the gx_system_event_send() function.</span></span>

<span data-ttu-id="6ee08-158">GX_MAX_DIRTY_AREAS</span><span class="sxs-lookup"><span data-stu-id="6ee08-158">GX_MAX_DIRTY_AREAS</span></span>
- <span data-ttu-id="6ee08-159">기본값: 64</span><span class="sxs-lookup"><span data-stu-id="6ee08-159">Default: 64.</span></span>
- <span data-ttu-id="6ee08-160">설명: 한 캔버스에서 유지 관리할 수 있는 고유한 더티 목록 항목의 최대 개수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-160">Description: Defines the maximum number of unique dirty list entries that can be maintained by one canvas.</span></span> <span data-ttu-id="6ee08-161">더티 목록이 오버플로되면 GUIX는 기본적으로 캔버스 루트 창을 더티로 표시합니다. 이는 개별 자식 위젯을 그리는 것보다 효율성이 낮습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-161">When the dirty list overflows, GUIX will default to marking the canvas root window as dirty, which is less efficient than drawing individual child widgets.</span></span>

<span data-ttu-id="6ee08-162">GX_MAX_CONTEXT_NESTING</span><span class="sxs-lookup"><span data-stu-id="6ee08-162">GX_MAX_CONTEXT_NESTING</span></span>
- <span data-ttu-id="6ee08-163">기본값: 8</span><span class="sxs-lookup"><span data-stu-id="6ee08-163">Default: 8.</span></span>
- <span data-ttu-id="6ee08-164">설명: 드로잉 컨텍스트 스택의 최대 중첩을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-164">Description: Defines the maximum nesting of the drawing context stack.</span></span> <span data-ttu-id="6ee08-165">UI 정의 내 부모/자식/자식/자식 위젯의 최대 중첩에 해당합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-165">This is equivalent to the maximum nesting of parent/child/child/child widgets within the UI definition.</span></span>

<span data-ttu-id="6ee08-166">GX_MAX_INPUT_CAPTURE_NESTING</span><span class="sxs-lookup"><span data-stu-id="6ee08-166">GX_MAX_INPUT_CAPTURE_NESTING</span></span>
- <span data-ttu-id="6ee08-167">기본값: 4</span><span class="sxs-lookup"><span data-stu-id="6ee08-167">Default: 4.</span></span>
- <span data-ttu-id="6ee08-168">설명: 사용자 입력(마우스 및 키보드)을 캡처하는 위젯 목록을 유지 관리하는 데 사용되는 스택의 크기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-168">Description: Defines the size of the stack used to maintain the list of widgets that have captures the user input (mouse and keyboard).</span></span>

<span data-ttu-id="6ee08-169">GX_SYSTEM_THREAD_PRIORITY</span><span class="sxs-lookup"><span data-stu-id="6ee08-169">GX_SYSTEM_THREAD_PRIORITY</span></span>
- <span data-ttu-id="6ee08-170">기본값: 16</span><span class="sxs-lookup"><span data-stu-id="6ee08-170">Default: 16.</span></span>
- <span data-ttu-id="6ee08-171">설명: gx_system_initialize() 중에 생성된 GUIX 스레드의 우선 순위를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-171">Description: Defines the priority of the GUIX thread created during gx_system_initialize().</span></span>

<span data-ttu-id="6ee08-172">GX_SYSTEM_THREAD_TIMESLICE</span><span class="sxs-lookup"><span data-stu-id="6ee08-172">GX_SYSTEM_THREAD_TIMESLICE</span></span>
- <span data-ttu-id="6ee08-173">기본값은 10입니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-173">Default: 10.</span></span>
- <span data-ttu-id="6ee08-174">설명: RTOS 타이머 틱을 기준으로 GUIX 스레드 시간 조각을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-174">Description: Defines the GUIX thread timeslice in terms of RTOS timer ticks.</span></span> <span data-ttu-id="6ee08-175">다른 스레드가 GUIX 스레드와 동일한 우선 순위로 정의된 경우 이 값은 경쟁 스레드에 CPU 제어를 부여하는 빈도를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-175">If other threads are defined with the same priority as the GUIX thread, this value determines how often those competing threads are granted CPU control.</span></span>

<span data-ttu-id="6ee08-176">GX_CURSOR_BLINK_INTERVAL</span><span class="sxs-lookup"><span data-stu-id="6ee08-176">GX_CURSOR_BLINK_INTERVAL</span></span>
- <span data-ttu-id="6ee08-177">기본값: 20.</span><span class="sxs-lookup"><span data-stu-id="6ee08-177">Default: 20.</span></span>
- <span data-ttu-id="6ee08-178">설명: 텍스트 입력 위젯의 입력 커서가 깜박이는 속도를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-178">Description: Defines the rate at which the input cursor blinks for text input widgets.</span></span> <span data-ttu-id="6ee08-179">이 값은 기본적으로 50ms로 정의되는 GUIX 타이머 틱을 기준으로 하므로 값 20은 입력 커서가 초당 한 번 깜박이는 것을 나타냅니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-179">This value is in terms of GUIX timer ticks, which by default is defines as 50 ms, so a value of 20 indicates that the input cursor blinks once per second.</span></span>

<span data-ttu-id="6ee08-180">GX_MULTI_LINE_INDEX_CACHE_SIZE</span><span class="sxs-lookup"><span data-stu-id="6ee08-180">GX_MULTI_LINE_INDEX_CACHE_SIZE</span></span>
- <span data-ttu-id="6ee08-181">기본값: 32</span><span class="sxs-lookup"><span data-stu-id="6ee08-181">Default: 32.</span></span>
- <span data-ttu-id="6ee08-182">설명: 여러 줄 텍스트 보기 및 여러 줄 텍스트 입력 위젯에서 유지 관리되는 목록 시작 인덱스 캐시의 크기를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-182">Description: Defines the size of the list-start index cache maintained by the multi-line text view and multi-line text input widgets.</span></span> <span data-ttu-id="6ee08-183">이 캐시를 사용하여 여러 줄 텍스트 위젯을 빠르게 세로로 스크롤할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-183">This cache is used to accomplish fast vertical scrolling of multi line text widgets.</span></span> <span data-ttu-id="6ee08-184">최상의 성능을 얻으려면 캐시 크기를 애플리케이션에서 정의된 가장 큰 여러 줄 텍스트 위젯의 표시 행 수보다 크게 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-184">For best performance, the cache size should be set greater than the number of visible rows of the largest multi line text widget defined by the application.</span></span> <span data-ttu-id="6ee08-185">예를 들어 텍스트 위젯의 최대 표시 행 수가 20개 행이면 애플리케이션에서 캐시 크기를 32(기본값)로 정의할 수 있습니다. 이렇게 하면 GUIX에서 모든 줄 시작 인덱스를 다시 계산하지 않고도 세로로 스크롤할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-185">For example, if the most visible rows for any text widget are 20 rows, the application might define a cache size of 32 (the default), which allows GUIX to scroll vertically without recalculating all line start indexes.</span></span>

<span data-ttu-id="6ee08-186">GX_MULTI_LINE_TEXT_BUTTON_MAX_LINES</span><span class="sxs-lookup"><span data-stu-id="6ee08-186">GX_MULTI_LINE_TEXT_BUTTON_MAX_LINES</span></span>
- <span data-ttu-id="6ee08-187">기본값: 4</span><span class="sxs-lookup"><span data-stu-id="6ee08-187">Default: 4.</span></span>
- <span data-ttu-id="6ee08-188">설명: 여러 줄 텍스트 단추 제어 블록은 단추에 표시되는 각 텍스트 줄에 대한 포인터를 유지 관리합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-188">Description: The multi-line text button control block maintains a pointer to each line of text to be displayed by the button.</span></span> <span data-ttu-id="6ee08-189">이 값은 최악의 경우 여러 줄 텍스트 단추에 필요한 텍스트 포인터 수를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-189">This value determines the number of text pointers needed by the worst case multi-line text button.</span></span>

<span data-ttu-id="6ee08-190">GX_POLYGON_MAX_EDGE_NUM</span><span class="sxs-lookup"><span data-stu-id="6ee08-190">GX_POLYGON_MAX_EDGE_NUM</span></span>
- <span data-ttu-id="6ee08-191">기본값은 10입니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-191">Default: 10.</span></span>
- <span data-ttu-id="6ee08-192">설명: 이 값은 GUIX에서 그릴 수 있는 가장 복잡한 다각형을 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-192">Description: This value determines the most complex polygon that can be drawn by GUIX.</span></span> <span data-ttu-id="6ee08-193">다각형 그리기 알고리즘에 따라 다각형 가장자리를 정의하는 데 필요한 선이 결정되며, 이 정의는 지원할 수 있는 최대 가장자리 수를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-193">The polygon drawing algorithm determines the lines needed to define the polygon edges, and this definition defines the maximum number of edges that can be supported.</span></span>

<span data-ttu-id="6ee08-194">GX_NUMERIC_SCROLL_WHEEL_STRING_BUFFER_SIZE</span><span class="sxs-lookup"><span data-stu-id="6ee08-194">GX_NUMERIC_SCROLL_WHEEL_STRING_BUFFER_SIZE</span></span>
- <span data-ttu-id="6ee08-195">기본값: 16</span><span class="sxs-lookup"><span data-stu-id="6ee08-195">Default: 16.</span></span>
- <span data-ttu-id="6ee08-196">설명: 숫자 스크롤 휠의 경우 스크롤 휠 위젯이 정수 값을 ASCII 문자열로 변환합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-196">Description: For a number scroll wheel, the scroll wheel widget converts integer values to ascii strings.</span></span> <span data-ttu-id="6ee08-197">이 값은 할당된 정수 값을 표시하는 데 필요한 문자열의 최대 길이를 결정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-197">This value determines the maximum length of the string required to display the assigned integer values.</span></span>

<span data-ttu-id="6ee08-198">GX_DEFAULT_CIRCULAR_GAUGE_ANIMATION_DELAY</span><span class="sxs-lookup"><span data-stu-id="6ee08-198">GX_DEFAULT_CIRCULAR_GAUGE_ANIMATION_DELAY</span></span>
- <span data-ttu-id="6ee08-199">기본값: 5</span><span class="sxs-lookup"><span data-stu-id="6ee08-199">Default: 5.</span></span>
- <span data-ttu-id="6ee08-200">설명: 마지막 각도 위치와 현재 각도 위치 사이의 바늘 이동에 애니메이션 효과를 적용하도록 구성된 원형 계기의 업데이트 간 GUIX 타이머 틱(50ms) 수를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-200">Description: Defines the number of GUIX timer ticks (50 ms) between updates of a circular gauge configured to animate the needle movement between last and current angular position.</span></span>

<span data-ttu-id="6ee08-201">GX_NUMERIC_PROMPT_BUFFER_SIZE</span><span class="sxs-lookup"><span data-stu-id="6ee08-201">GX_NUMERIC_PROMPT_BUFFER_SIZE</span></span>
- <span data-ttu-id="6ee08-202">기본값: 16</span><span class="sxs-lookup"><span data-stu-id="6ee08-202">Default: 16.</span></span>
- <span data-ttu-id="6ee08-203">설명: 숫자 프롬프트는 프롬프트에 할당된 정수 값을 ASCII 문자열로 변환하기 위해 버퍼를 할당합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-203">Description: A numeric prompt allocates a buffer to convert an integer value assigned to the prompt to an ascii string.</span></span> <span data-ttu-id="6ee08-204">이 정의는 문자 버퍼의 크기를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-204">This definition defines the size of this character buffer.</span></span>

<span data-ttu-id="6ee08-205">GX_ANIMATION_POOL_SIZE</span><span class="sxs-lookup"><span data-stu-id="6ee08-205">GX_ANIMATION_POOL_SIZE</span></span>
- <span data-ttu-id="6ee08-206">기본값: 6</span><span class="sxs-lookup"><span data-stu-id="6ee08-206">Default: 6.</span></span>
- <span data-ttu-id="6ee08-207">설명: GUIX는 gx_system_animation_get 및 gx_system_animation_free() API를 사용하여 애니메이션 정보 구조를 동적으로 할당하고 반환할 수 있는 애니메이션 풀을 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-207">Description: GUIX defines an animation pool from which animation information structures can be dynamically allocated and returned, using gx_system_animation_get and gx_system_animation_free() APIs.</span></span> <span data-ttu-id="6ee08-208">이 정의는 애니메이션 제어 블록 풀의 크기를 설정합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-208">This definition defines the size of this animation control block pool.</span></span>

<span data-ttu-id="6ee08-209">GX_MOUSE_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-209">GX_MOUSE_SUPPORT</span></span>
- <span data-ttu-id="6ee08-210">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-210">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-211">설명: 이 정의를 통해 마우스 입력을 지원할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-211">Description: This definition enables support for mouse input.</span></span> <span data-ttu-id="6ee08-212">소프트웨어 마우스를 사용하려면 디스플레이 드라이버에서 마우스 커서를 그리거나 추적해야 하므로 디스플레이 드라이버에 오버헤드가 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-212">Software mouse requires that the display driver draw and track the mouse cursor, which adds extra overhead to the display driver.</span></span> <span data-ttu-id="6ee08-213">이 정의는 터치 스크린이 아닌 마우스를 지원해야 하는 경우에만 설정해야 합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-213">This definition should only be defined when a mouse (not a touch screen) must be supported.</span></span>

<span data-ttu-id="6ee08-214">GX_HARDWARE_MOUSE_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-214">GX_HARDWARE_MOUSE_SUPPORT</span></span>
- <span data-ttu-id="6ee08-215">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-215">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-216">설명: 이 정의를 설정하면 GUIX 디스플레이 드라이버에서 하드웨어 마우스 커서 그리기 지원을 활용합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-216">Description: When this definition is defined, the GUIX display driver utilizes hardware mouse cursor drawing support.</span></span> <span data-ttu-id="6ee08-217">이렇게 하면 마우스 커서 아래의 캔버스 메모리를 캡처하는 데 필요한 메모리가 감소하고 마우스 오버레이 그래프 계층을 지원하는 하드웨어 대상의 시스템 성능이 향상됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-217">This reduces the memory required to capture the canvas memory beneath the mouse cursor and improves system performance for those hardware targets support a mouse overlay graphics layer.</span></span>

<span data-ttu-id="6ee08-218">GX_FONT_KERNING_SUPPORT</span><span class="sxs-lookup"><span data-stu-id="6ee08-218">GX_FONT_KERNING_SUPPORT</span></span>
- <span data-ttu-id="6ee08-219">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-219">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-220">설명: 이 정의를 설정하면 글꼴 커닝 지원을 사용할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-220">Description: This definition can be defined to enable font kerning support.</span></span> <span data-ttu-id="6ee08-221">글꼴 커닝은 특정 문자 모양 조합의 문자 모양 간격을 향상시킵니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-221">Font kerning improves glyph spacing for certain glyph combinations.</span></span> <span data-ttu-id="6ee08-222">이 지원으로 인해 런타임 문자열 그리기 함수에 약간의 오버헤드가 추가되고 글꼴 데이터 구조의 크기도 약간 증가합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-222">This support adds a small amount of overhead to the runtime string drawing functions, and also adds a small amount of size to the font data structures.</span></span>

<span data-ttu-id="6ee08-223">GX_WIDGET_USER_DATA</span><span class="sxs-lookup"><span data-stu-id="6ee08-223">GX_WIDGET_USER_DATA</span></span>
- <span data-ttu-id="6ee08-224">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-224">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-225">설명: 이 정의를 설정하면 사용자 정의 데이터 필드가 GX_WIDGET 제어 블록에 추가됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-225">Description: If defined, this adds a user-defined data field to the GX_WIDGET control block.</span></span> <span data-ttu-id="6ee08-226">GUIX Studio 내에서 속성 뷰를 사용하여 이 데이터 필드를 할당할 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-226">This data field can be assigned using the properties view within GUIX Studio.</span></span> <span data-ttu-id="6ee08-227">이 데이터 필드는 내부적으로 GUIX에서 무시되지만 애플리케이션 소프트웨어에서 다양한 용도로 사용될 수 있습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-227">This data field is ignored by GUIX internally, but can be used by application software for many purposes.</span></span>

<span data-ttu-id="6ee08-228">GUIX_5_4_0_COMPATIBILITY</span><span class="sxs-lookup"><span data-stu-id="6ee08-228">GUIX_5_4_0_COMPATIBILITY</span></span>
- <span data-ttu-id="6ee08-229">기본값: 정의되지 않음</span><span class="sxs-lookup"><span data-stu-id="6ee08-229">Default: Not defined.</span></span>
- <span data-ttu-id="6ee08-230">설명: 특정 GUI API는 사용할 수 없는 텍스트 색에 대한 지원을 추가하고 고정 소수점 일치 매개 변수를 사용하여 특정 수학 함수의 정확도를 높이기 위해 릴리스 5.4.0 이후 수정되었습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-230">Description: Certain GUI APIs were modified after release 5.4.0 to add support for disabled text colors and to improve the accuracy of certain math functions by using fixed-point match parameters.</span></span> <span data-ttu-id="6ee08-231">이 변경 내용으로 인해 5.4.0 이후의 GUIX 라이브러리 릴리스는 이전 릴리스와 호환되지 않습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-231">These changes make GUIX library releases after 5.4.0 incompatible with previous releases.</span></span> <span data-ttu-id="6ee08-232">그러나 이 #define을 설정하면 API가 5.4.0 및 아전 릴리스와 완전히 호환되도록 라이브러리를 빌드할 수 있으므로 최신 GUIX 라이브러리 릴리스로 컴파일하기 위해 기존 애플리케이션을 변경할 필요가 없습니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-232">However, by turning on this #define, the library can be built such that the APIs fully compatible with releases <= 5.4.0, meaning that no changes are needed in existing applications to compile with the latest GUIX library release.</span></span>

<span data-ttu-id="6ee08-233">GX_MAX_STRING_LENGTH</span><span class="sxs-lookup"><span data-stu-id="6ee08-233">GX_MAX_STRING_LENGTH</span></span>
- <span data-ttu-id="6ee08-234">기본값: 102400</span><span class="sxs-lookup"><span data-stu-id="6ee08-234">Default: 102400</span></span>
- <span data-ttu-id="6ee08-235">설명: 잘못된 문자열을 테스트하는 데 사용되는 문자열의 최대 길이를 정의합니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-235">Description: Defines the maximum length of a string, which is used to test invalid strings.</span></span> <span data-ttu-id="6ee08-236">최대 문자열 길이를 초과하는 입력 문자열은 잘못된 것으로 간주됩니다.</span><span class="sxs-lookup"><span data-stu-id="6ee08-236">If the input string is exceeding the maximum string length, it will be regard as invalid.</span></span>