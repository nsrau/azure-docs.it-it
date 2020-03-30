---
title: Supporto per la piattaforma di Azure IoT SDK per dispositivi | Microsoft Docs
description: Gli SDK per dispositivi open source sono disponibili in GitHub in C, .NET (C, Java, Node.js e Python) per connettere i dispositivi al servizio DPS (Hub and Device Provisioning Service) di Azure IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: robinsh
ms.openlocfilehash: 496b890cc49b6b6b9f15213a48472447f801b1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76045125"
---
# <a name="azure-iot-device-sdks-platform-support"></a>Supporto della piattaforma SDKs per dispositivi IoT di AzureAzure IoT Device SDKs Platform Support

Microsoft si impegna a espandere continuamente l'universo dei dispositivi in grado di Hub IoT di Azure.Microsoft sstrives to continuamente expand the universe of Azure IoT Hub devices. Microsoft pubblica SDK per dispositivi open source su GitHub per connettere i dispositivi all'hub IoT di Azure e al servizio di provisioning dei dispositivi. Gli SDK del dispositivo sono disponibili per C, .NET (C) , Java, Node.js e Python. Microsoft verifica ogni SDK per verificare che venga eseguito sulle configurazioni supportate descritte nella sezione Microsoft SDK e supporto della [piattaforma per dispositivi.](#microsoft-sdks-and-device-platform-support)

Oltre agli SDK dei dispositivi, Microsoft offre diverse altre strade per consentire a clienti e sviluppatori di connettere i propri dispositivi ad Azure IoT:In addition to the device SDKs, Microsoft provides several other avenues to empower customers and developers to connect their devices to Azure IoT:

* Microsoft collabora con diverse aziende partner per aiutarli a pubblicare kit di sviluppo, basati su Azure IoT C SDK, per le proprie piattaforme hardware.

* Microsoft works with Microsoft trusted partners to provide an ever-expanding set of devices that have been tested and certified for Azure IoT. Per un elenco corrente di questi dispositivi, vedere il catalogo dei [dispositivi certificato di Azure per IoT.](https://catalog.azureiotsolutions.com/)

* Microsoft fornisce un livello di astrazione della piattaforma (PAL) nell'SDK di Azure IoT Hub Device C che consente agli sviluppatori di trasferire facilmente l'SDK alla propria piattaforma. Per ulteriori informazioni, vedere le linee guida per la [conversione dell'SDK di C](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

In questo argomento vengono fornite informazioni sui Microsoft SDK e sulle configurazioni della piattaforma supportate, nonché su ognuna delle altre opzioni elencate in precedenza.

## <a name="microsoft-sdks-and-device-platform-support"></a>Microsoft SDK e supporto della piattaforma per dispositivi

Microsoft pubblica SDK open source su GitHub per i linguaggi seguenti: C, .NET (C) , Node.js, Java e Python. Gli SDK e le relative dipendenze sono elencati in questa sezione. Gli SDK sono supportati su qualsiasi piattaforma del dispositivo che soddisfi queste dipendenze.

Per ognuno degli SDK elencati, Microsoft:

* Compila ed esegue continuamente test end-to-end sul ramo master dell'SDK pertinente in GitHub su diverse piattaforme più popolari.  Per fornire la copertura dei test tra diverse versioni del compilatore, in genere viene eseguito il test rispetto alla versione LTS più recente e alla versione più diffusa.

* Fornisce indicazioni per l'installazione o pacchetti di installazione, se applicabile.

* Supporta completamente gli SDK su GitHub con codice open source, un percorso per i contributi dei clienti e il coinvolgimento del team di prodotto con i problemi di GitHub.

### <a name="c-sdk"></a>SDK per C

[L'SDK del dispositivo C dell'hub IoT](https://github.com/Azure/azure-iot-sdk-c) di Azure viene testato e supporta le configurazioni seguenti.

| OS                  | Libreria TLS                  | Requisiti aggiuntivi                                                                     |
|---------------------|------------------------------|---------------------------------------------------------------------------------------------|
| Linux               | OpenSSL, WolfSSL o BearSSL | Prese di Berkeley</br></br>Interfaccia poSIX (Portable Operating System Interface)                       |
| iOS 12.2            | OpenSSL                      | XCode emulato in OSX 10.13.4                                                               |
| Famiglia Windows 10   | SChannel                     |                                                                                             |
| Mbed OS 5.4         | TLS Mbed 2                   | [Kit di sviluppo MXChip IoT](https://microsoft.github.io/azure-iot-developer-kit/)                  |
| Sistema operativo della sfera di AzureAzure Sphere OS     | LupoSSL                      | [Azure Sphere MT3620](https://azure.microsoft.com/services/azure-sphere/get-started/) |
| Arduino             | OrsoSSL                      | [ESP32 o ESP8266](https://github.com/Azure/azure-iot-arduino#simple-sample-instructions) 

### <a name="python-sdk"></a>Python SDK

[L'SDK del dispositivo Azure IoT Hub Python](https://github.com/Azure/azure-iot-sdk-python) viene testato e supporta le configurazioni seguenti.

| OS                  | Compilatore                          |
|---------------------|-----------------------------------|
| Linux               | Python 2.7., 3.5 o versioni successive |
| MacOS High Sierra   | Python 2.7., 3.5 o versioni successive |
| Famiglia Windows 10   | Python 2.7., 3.5 o versioni successive |

Solo Python versione 3.5.3 o successiva supporta le API asincrone, è consigliabile usare la versione 3.7 o successiva.

### <a name="net-sdk"></a>.NET SDK

L'SDK del [dispositivo .NET dell'hub IoT](https://github.com/Azure/azure-iot-sdk-csharp) di Azure viene testato e supporta le configurazioni seguenti.

| OS                                   | Standard                                                   |
|--------------------------------------|------------------------------------------------------------|
| Linux                                | .NET Core 2.1                                              |
| SKU di Windows 10 Desktop e Server   | .NET Core 2.1, .NET Framework 4.5.1 o .NET Framework 4.7 |

.NET SDK può essere utilizzato anche con Windows IoT Core con [l'agente di dispositivo](https://github.com/ms-iot/azure-client-tools/blob/master/docs/device-agent/device-agent.md) di Azure o un NTService personalizzato che può utilizzare RPC per comunicare con le applicazioni [UWP.](https://docs.microsoft.com/samples/microsoft/windows-iotcore-samples/ntservice-rpc/)

### <a name="nodejs-sdk"></a>Node.js SDK

L'SDK del [dispositivo Azure IoT Hub Node.js](https://github.com/Azure/azure-iot-sdk-node) viene testato e supporta le configurazioni seguenti.

| OS                  | Versione del nodo    |
|---------------------|-----------------|
| Linux               | LTS e corrente |
| Famiglia Windows 10   | LTS e corrente |

### <a name="java-sdk"></a>SDK per Java

Azure [IoT Hub Java Device SDK](https://github.com/Azure/azure-iot-sdk-java) viene testato e supporta le configurazioni seguenti.

| OS                     | Versione Java |
|------------------------|--------------|
| API Android 28         | Java 8       |
| Linux x64             | Java 8       |
| Famiglia Windows 10 x64  | Java 8       |

## <a name="partner-supported-development-kits"></a>Kit di sviluppo supportati da i partner

Microsoft collabora con vari partner per fornire kit di sviluppo per diverse architetture di microprocessori. Questi partner hanno portato l'SDK di Azure IoT C alla piattaforma. I partner creano e gestiscono il livello di astrazione della piattaforma (PAL) dell'SDK. Microsoft collabora con questi partner per fornire supporto esteso.

| Partner             | Dispositivi                            | Collegamento                     | Supporto |
|---------------------|------------------------------------|--------------------------|---------|
| Espressif           | ESP32 <br/> ESP8266                              | [Esp-azure](https://github.com/espressif/esp-azure)                | [GitHub](https://github.com/espressif/esp-azure)  
| Qualcomm            | Modem Iot Qualcomm MDM9206 LTE     | [Qualcomm LTE per IoT SDK](https://developer.qualcomm.com/software/lte-iot-sdk) | [Forum](https://developer.qualcomm.com/forums/software/lte-iot-sdk)   |
| ST Microelectronics | Serie STM32L4 <br/> Serie STM32F4 <br/>  Serie STM32F7 <br/>  STM32L4 Kit Discovery per nodo IoT    | [X-CUBE-AZURE](https://www.st.com/en/embedded-software/x-cube-azure.html) <br/>  <br/> [P-NUCLEO-AZURE](https://www.st.com/content/st_com/en/products/evaluation-tools/solution-evaluation-tools/communication-and-connectivity-solution-eval-boards/p-nucleo-azure1.html) <br/> [FP-CLD-AZURE](https://www.st.com/content/st_com/en/products/embedded-software/mcus-embedded-software/stm32-embedded-software/stm32-ode-function-pack-sw/fp-cld-azure1.html)            | [Supporto](https://www.st.com/content/st_com/en/support/support-home.html)
| Texas Instruments   | CC3220SF LaunchPad </br> Lanciatore CC3220S </br> CC3235SF LaunchPad </br> Lanciatore CC3235S </br> MSP432E4 LaunchPad | [Plug-in Azure IoT per SimpleLink](https://github.com/TexasInstruments/azure-iot-pal-simplelink) | [Forum di TI E2E](https://e2e.ti.com) <br/> [Forum di E2E TI per CC3220](https://e2e.ti.com/support/wireless_connectivity/simplelink_wifi_cc31xx_cc32xx/) <br/> [Forum di E2E TI per MSP432E4](https://e2e.ti.com/support/microcontrollers/msp430/) |

## <a name="porting-the-microsoft-azure-iot-c-sdk"></a>Conversione di Microsoft Azure IoT C SDK

Se la piattaforma del dispositivo non è trattata da una delle sezioni precedenti, è possibile prendere in considerazione il porting di Azure IoT C SDK. Il porting dell'SDK C comporta principalmente l'implementazione del livello di astrazione della piattaforma (PAL) dell'SDK. Il PAL definisce primitive che forniscono l'associazione tra il dispositivo e le funzioni di livello superiore nell'SDK. Per ulteriori informazioni, vedere [Porting Guidance](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md).

## <a name="microsoft-partners-and-certified-azure-iot-devices"></a>Partner Microsoft e dispositivi IoT di Azure certificati

Microsoft collabora con un certo numero di partner per espandere continuamente l'universo IoT di Azure con i dispositivi testati e certificati di Azure IoT.

* Per esplorare i dispositivi certificati IoT di Azure, vedere [Catalogo dispositivi Certificati IoT di Microsoft Azure.](https://catalog.azureiotsolutions.com/)

* Per altre informazioni sull'ecosistema Azure Certified for IoT, vedere [Join the Certified for IoT ecosystem](https://catalog.azureiotsolutions.com/register).

## <a name="connecting-to-iot-hub-without-an-sdk"></a>Connessione all'hub IoT senza un SDKConnecting to IoT Hub without an SDK

Se non riesci a usare uno degli SDK del dispositivo hub IoT, puoi connetterti direttamente all'hub IoT usando [le API REST dell'hub IoT](https://docs.microsoft.com/rest/api/iothub/) da qualsiasi applicazione in grado di inviare e ricevere richieste e risposte HTTPS.

## <a name="support-and-other-resources"></a>Supporto e altre risorse

Se si verificano problemi durante l'uso degli SDK per dispositivi IoT di Azure, esistono diversi modi per richiedere supporto. Puoi provare uno dei seguenti canali:

**Segnalazione** di bug: i bug negli SDK del dispositivo possono essere segnalati nella pagina dei problemi del progetto GitHub pertinente. Le correzioni passano rapidamente dal progetto agli aggiornamenti del prodotto.

* [Problemi relativi a Azure IoT Hub C SDKAzure IoT Hub C SDK issues](https://github.com/Azure/azure-iot-sdk-c/issues)

* [Problemi relativi all'SDK di Azure IoT Hub .NET (C)](https://github.com/Azure/azure-iot-sdk-csharp/issues)

* [Problemi di Azure IoT Hub Java SDKAzure IoT Hub Java SDK issues](https://github.com/Azure/azure-iot-sdk-java/issues)

* [Problemi relativi all'SDK di Azure IoT Hub Node.jsAzure IoT Hub Node.js SDK issues](https://github.com/Azure/azure-iot-sdk-node/issues)

* [Problemi di Azure IoT Hub Python SDKAzure IoT Hub Python SDK issues](https://github.com/Azure/azure-iot-sdk-python/issues)

**Team di supporto tecnico clienti Microsoft:** gli utenti che dispongono di un piano di [supporto](https://azure.microsoft.com/support/plans/) possono coinvolgere il team del Supporto tecnico clienti Microsoft creando una nuova richiesta di supporto direttamente dal portale di [Azure.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

**Richieste di funzionalità:** le richieste di funzionalità IoT di Azure vengono registrate tramite la [pagina Voce utente](https://feedback.azure.com/forums/321918-azure-iot)del prodotto.

## <a name="next-steps"></a>Passaggi successivi

* [SDK di servizi e dispositivi](iot-hub-devguide-sdks.md)
* [Indicazioni sulla portabilità](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
