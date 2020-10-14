---
title: Usare l'Plug and Play delle cose su dispositivi vincolati | Microsoft Docs
description: Informazioni su come implementare il Plug and Play di Internet delle cose sui dispositivi vincolati usando l'SDK per Embedded C o Azure RTO.
author: elhorton
ms.author: elhorton
ms.date: 09/23/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d61ca10612a0935f8483745d164835d7498280c0
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042814"
---
# <a name="implement-iot-plug-and-play-on-constrained-devices"></a>Implementare l'Plug and Play delle cose sui dispositivi vincolati

Se si sta sviluppando per i *dispositivi vincolati*, è possibile usare la plug and Play di Internet delle cose con [Azure SDK per le librerie client di Azure Internet Embedded](https://aka.ms/embeddedcsdk) o con [Azure RTO](/azure/rtos/overview-rtos). Questo articolo include collegamenti e risorse per questi scenari vincolati.

## <a name="use-the-sdk-for-embedded-c"></a>Usare l'SDK per Embedded C

L'SDK per Embedded C offre una soluzione leggera per connettere i dispositivi vincolati ai servizi di Azure Internet, incluso l'uso delle [convenzioni plug and Play](concepts-convention.md). I collegamenti seguenti includono esempi per i dispositivi reali e per scopi didattici e di debug.

### <a name="use-a-real-device"></a>Usare un dispositivo reale

Per un'esercitazione end-to-end completa che usa l'SDK per la funzionalità Embedded C, il servizio Device provisioning e gli Plug and Play su un dispositivo reale, vedere [reimpiegate PIC-IoT WX Development Board per connettersi ad Azure tramite il servizio Device provisioning in hub](https://github.com/Azure-Samples/Microchip-PIC-IoT-Wx)Internet.

### <a name="introductory-samples"></a>Esempi introduttivi

L'SDK per il repository C incorporato contiene [diversi esempi](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/samples/iot#iot-hub-plug-and-play-sample) che illustrano come usare l'plug and Play Internet:

> [!NOTE]
> Questi esempi sono visualizzati in Windows e Linux a scopo didattico e di debug. In uno scenario di produzione, gli esempi sono destinati solo ai dispositivi vincolati.

- [Esempio di cronotermostato con SDK per Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_sample.c)

- [Esempio di controller di temperatura con SDK per Embedded C](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/paho_iot_hub_pnp_component_sample.c)

## <a name="using-azure-rtos"></a>Uso di Azure RTO

Azure RTO include un livello leggero che aggiunge connettività nativa ai servizi cloud di Azure. Questo livello offre un meccanismo semplice per connettere i dispositivi vincolati ad Azure, usando le funzionalità avanzate di Azure RTO. Per ulteriori informazioni, vedere la pagina relativa alla [Microsoft Azure RTO](/azure/rtos/overview-rtos).

### <a name="toolchains"></a>Toolchain

Gli esempi di Azure RTO sono forniti con diversi tipi di combinazioni di IDE e di tipo di oggetto, ad esempio:

- IAR: IDE [Embedded Workbench](https://www.iar.com/iar-embedded-workbench/) di IAR
- GCC/CMake: si basa sul sistema di compilazione Open Source [CMake](https://cmake.org/) e sulla gestione delle risorse [embedded di GNU ARM](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm).
- MCUExpresso: [IDE MCUXpresso](https://www.nxp.com/design/software/development-software/mcuxpresso-software-and-tools-/mcuxpresso-integrated-development-environment-ide:MCUXpresso-IDE) di NXP
- STM32Cube: [IDE STM32Cube](https://www.st.com/en/development-tools/stm32cubeide.html) di STMicroelectronic
- MPLAB: IDE di [MPLAB X](https://www.microchip.com/mplab/mplab-x-ide) del microchip

### <a name="samples"></a>Esempi

Per esempi che illustrano come iniziare a usare dispositivi diversi con Azure RTO e le informazioni Plug and Play, vedere la tabella seguente:

Produttore | Dispositivo | Esempi |
| --- | --- | --- |
| Microchip | [ATSAME54-XPRO](https://www.microchip.com/developmenttools/productdetails/atsame54-xpro) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/Microchip/ATSAME54-XPRO) • [IAR](https://aka.ms/azrtos-sample/e54-iar) • [MPLAB](https://aka.ms/azrtos-sample/e54-mplab)
| MXCHIP | [AZ3166](https://aka.ms/iot-devkit) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)
| NXP | [MIMXRT1060-EVK](https://www.nxp.com/design/development-boards/i-mx-evaluation-and-development-boards/mimxrt1060-evk-i-mx-rt1060-evaluation-kit:MIMXRT1060-EVK) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/NXP/MIMXRT1060-EVK) • [IAR](https://aka.ms/azrtos-sample/rt1060-iar) • [MCUXpresso](https://aka.ms/azrtos-sample/rt1060-mcuxpresso)
| STMicroelectronics | [32F746GDISCOVERY](https://www.st.com/en/evaluation-tools/32f746gdiscovery.html) | [IAR](https://aka.ms/azrtos-sample/f746g-iar) • [STM32Cube](https://aka.ms/azrtos-sample/f746g-cubeide)
| STMicroelectronics | [B-L475E-IOT01](https://www.st.com/en/evaluation-tools/b-l475e-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)
| STMicroelectronics | [B-L4S5I-IOT01](https://www.st.com/en/evaluation-tools/b-l4s5i-iot01a.html) | [GCC/CMake](https://github.com/azure-rtos/getting-started/tree/master/STMicroelectronics/STM32L4_L4%2B) • [IAR](https://aka.ms/azrtos-sample/l4s5-iar) • [STM32Cube](https://aka.ms/azrtos-sample/l4s5-cubeide)

## <a name="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le opzioni per l'implementazione di Plug and Play su dispositivi vincolati, un passaggio successivo suggerito consiste nell'acquisire familiarità con le [convenzioni plug and Play](concepts-convention.md).