---
title: Informazioni sugli SDK dell'hub IoT | Documentazione Microsoft
description: "Guida per gli sviluppatori: informazioni e collegamenti ai vari Azure IoT SDK per dispositivi e servizi che è possibile usare per compilare app per dispositivo e app back-end."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96dad8917f4483c3d7c74ea0462b96d89286f549
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>Comprendere e usare gli SDK di Azure IoT

Esistono tre categorie di Software Development Kit (SDK) per l'uso con l'hub IoT:

* Gli **SDK per dispositivi** consentono di compilare le app eseguite su dispositivi IoT. Queste app inviano la telemetria all'hub IoT e, facoltativamente, ricevono messaggi dall'hub IoT.

* Gli **SDK per servizi** consentono di gestire l'hub IoT e, facoltativamente, inviare messaggi ai dispositivi IoT.

* **Azure IoT Edge** consente di compilare i gateway per i dispositivi che non usano uno dei protocolli supportati. I gateway possono anche elaborare i messaggi sul perimetro.

Vengono forniti SDK per supportare più linguaggi di programmazione.

## <a name="azure-iot-device-sdks"></a>SDK dispositivo IoT Azure

GLI SDK per dispositivi IoT di Microsoft Azure contengono codice che facilita la compilazione dei dispositivi e delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

I seguenti Azure IoT SDK per dispositivi sono disponibili per il download da GitHub:

* [Azure IoT SDK per dispositivi per .NET][lnk-dotnet-device-sdk]
* [Azure IoT SDK per dispositivi per Java][lnk-java-device-sdk]
* [Azure IoT SDK per dispositivi per Node.js][lnk-node-device-sdk]
* [Azure IoT SDK per dispositivi per Python][lnk-python-device-sdk]
* [Azure IoT SDK per dispositivi per C][lnk-c-device-sdk] scritto in ANSI C (C99) per la portabilità e la compatibilità multipiattaforma. Sono presenti due librerie client di dispositivi per C, l'**iothub_client** di basso livello e il **serializzatore**.

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilità delle piattaforme del sistema operativo e hardware

Per altre informazioni sulla compatibilità SDK con i dispositivi hardware specifici, vedere il [catalogo di dispositivi Azure Certified per IoT][lnk-certified].

## <a name="azure-iot-service-sdks"></a>Azure IoT SDK per servizi

I componenti Azure Iot SDK per servizi contengono codice che facilita la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire dispositivi e sicurezza.

I seguenti Azure IoT SDK per servizi sono disponibili per il download da GitHub:

* [Azure IoT SDK per servizi per .NET][lnk-dotnet-service-sdk]
* [Azure IoT SDK per servizi per Java][lnk-java-service-sdk]
* [Azure IoT SDK per servizi per Node.js][lnk-node-service-sdk]
* [Azure IoT SDK per dispositivi per Python][lnk-python-service-sdk]
* [Azure IoT SDK per servizi per C][lnk-c-service-sdk]

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge contiene l'infrastruttura e i moduli per creare soluzioni gateway IoT. È possibile estendere IoT Edge per creare gateway personalizzati in base agli scenari end-to-end.

È possibile scaricare [Azure IoT Edge][lnk-iot-edge] da GitHub.

## <a name="online-api-reference-documentation"></a>Documentazione di riferimento API online

L'elenco seguente contiene un elenco di collegamenti alla documentazione di riferimento all'API online per librerie di dispositivi, servizi e gateway di Azure IoT:

* [Internet of Things (IoT) .NET][lnk-dotnet-ref]
* [Azure IoT SDK per dispositivi per Java][lnk-java-ref]
* [Azure IoT SDK per servizi per Java][lnk-java-service-ref]
* [Azure IoT SDK per dispositivi per Node.js][lnk-node-ref]
* [Azure IoT SDK per servizi per Node.js][lnk-node-service-ref]
* [Azure IoT SDK per dispositivi per C][lnk-c-ref]
* [REST hub IoT][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-devguide-endpoints]
* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi][lnk-devguide-query]
* [Quote e limitazioni][lnk-devguide-quotas]
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
