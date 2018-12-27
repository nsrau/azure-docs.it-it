---
title: Supporto per la piattaforma di Azure IoT SDK per dispositivi | Microsoft Docs
description: 'Concetti: elenco di piattaforme supportate da Azure IoT SDK per dispositivi'
author: yzhong94
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: yizhon
ms.openlocfilehash: ebe039c37eb42b0044fa0acf9361edc7e8cbb828
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879389"
---
# <a name="azure-iot-sdks-platform-support"></a>Supporto della piattaforma Azure IoT SDK

[Azure IoT SDK](iot-hub-devguide-sdks.md) è un set di librerie per interagire con l'hub IoT e il servizio di provisioning di dispositivi con ampio supporto linguistico e per la piattaforma. Gli SDK eseguiti sulle piattaforme più comuni e gli sviluppatori possono trasferire C SDK alla piattaforma specifica seguendo la [Indicazioni sulla portabilità](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md). 

Microsoft supporta una vasta gamma di sistemi operativi/piattaforme/framework e può essere estesa usando Azure IoT SDK per C. Alcuni sono supportati ufficialmente dal team, raggruppati in livelli che rappresentano il grado di supporto previsto per gli utenti. *Piattaforme interamente supportate* indica le piattaforme per cui Microsoft:

    * Esegue la compilazione in modo continuo ed esegue il test in modalità end-to-end rispetto al master e alle versioni LTS supportate.  Per garantire la copertura dei test tra versioni diverse, viene testata in genere con la versione LTS più recente e la versione più diffusa.  Altre versioni della stessa piattaforma possono essere supportate tramite la compatibilità della versione di piattaforma.
    * Offre la guida all'installazione oppure i pacchetti di installazione, se applicabile.
    * Offre supporto completo su GitHub.

Inoltre, un elenco di partner ha trasferito SDK per C a più piattaforme e gestiscono il livello di astrazione della piattaforma (PAL). [Azure Certified per il catalogo IoT Device](https://catalog.azureiotsolutions.com/) include inoltre un elenco di piattaforme del sistema operativo in cui sono stati testati vari SDK. Gli SDK vengono compilati regolarmente su queste piattaforme, con test e supporto limitati:

* MBED2
* Arduino
* Windows CE 2013 (deprecato a ottobre 2018)
* .NET standard 1.3 con .NET Core 2.1 e .NET Framework 4.7
* Xamarin iOS, Android, piattaforma UWP

## <a name="supported-platforms"></a>Piattaforme supportate

Sono presenti diverse piattaforme supportate.

### <a name="c-sdk"></a>SDK per C

| OS                  | Arch | Compilatore             | Libreria TLS       |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | gcc-5.4.0            | openssl  - 1.0.2g |
| Ubuntu 18.04 LTS    | X64  | gcc-7.3              | WolfSSL – 1.13    |
| Ubuntu 18.04 LTS    | X64  | Clang 6.0.X          | OpenSSL – 1.1.0g  |
| OSX 10.13.4         | x64  | XCode 9.4.1          | Native OSX        |
| Windows Server 2016 | x64  | Visual Studio 14.0.X | SChannel          |
| Windows Server 2016 | x86  | Visual Studio 14.0.X | SChannel          |
| Debian 9 Stretch    | x64  | gcc-7.3              | Openssl – 1.1.0f  |

### <a name="python-sdk"></a>Python SDK

| OS                  | Arch | Compilatore   | Libreria TLS |
|---------------------|------|------------|-------------|
| Windows Server 2016 | x86  | Python 2.7 | openssl     |
| Windows Server 2016 | x64  | Python 2.7 | openssl     |
| Windows Server 2016 | x86  | Python 3.5 | openssl     |
| Windows Server 2016 | x64  | Python 3.5 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 2.7 | openssl     |
| Ubuntu 18.04 LTS    | x86  | Python 3.4 | openssl     |
| MacOS High Sierra   | x64  | Python 2.7 | openssl     |

### <a name="net-sdk"></a>.NET SDK

| OS                  | Arch | Framework            | Standard          |
|---------------------|------|----------------------|-------------------|
| Ubuntu 16.04 LTS    | X64  | .NET Core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET Core 2.1        | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.7   | .NET standard 2.0 |
| Windows Server 2016 | X64  | .NET Framework 4.5.1 | N/D               |

### <a name="nodejs-sdk"></a>Node.js SDK

| OS                                           | Arch | Versione del nodo |
|----------------------------------------------|------|--------------|
| Ubuntu 16.04 LTS (con immagine docker del nodo 6) | X64  | Nodo 6       |
| Windows Server 2016                          | X64  | Nodo 6       |

### <a name="java-sdk"></a>SDK per Java

| OS                  | Arch | Versione Java |
|---------------------|------|--------------|
| Ubuntu 16.04 LTS    | X64  | Java 8       |
| Windows Server 2016 | X64  | Java 8       |
| API Android 28 | X64  | Java 8       |

## <a name="partner-supported-platforms"></a>Piattaforme supportate del partner

I clienti possono estendere il supporto della piattaforma eseguendo il porting di Azure IoT C SDK, in particolare creando il livello di astrazione della piattaforma dell'SDK. Microsoft collabora con i partner per offrire supporto esteso. Elenco di partner che ha eseguito il porting di C SDK su più piattaforme e gestione del livello di astrazione della piattaforma.

| Partner             | Dispositivi                            | Collegamento                     | Supporto |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem Iot Qualcomm MDM9206 LTE     | [Qualcomm LTE per IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Serie STM32L4 <br/> Serie STM32F4 <br/>  Serie STM32F7 <br/>  STM32L4 Kit Discovery per nodo IoT    | [X-CUBE-CLOUD](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-cloud.html) <br/> [X-CUBE-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32cube-expansion-packages/x-cube-azure.html) <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Supporto](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF Launchpad <br/> CC3220S Launchpad <br/> MSP432E4 Launchpad      | [Plug-in Azure IoT per SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum di TI E2E](https://e2e.ti.com) <br/> [Forum di E2E TI per CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Forum di E2E TI per MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="next-steps"></a>Passaggi successivi

* [SDK di servizi e dispositivi](iot-hub-devguide-sdks.md)
* [Indicazioni sulla portabilità](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
