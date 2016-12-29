---
title: Guida per gli sviluppatori - SDK dell&quot;hub IoT | Documentazione Microsoft
description: Guida per gli sviluppatori dell&quot;hub IoT di Azure - Informazioni e collegamenti ai vari Azure IoT SDK per dispositivi e servizi.
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58dc546ee2db9a0925ecf0fc074795b531323e03


---
# <a name="azure-iot-sdks"></a>Azure IoT SDK
## <a name="azure-iot-device-sdk"></a>Azure IoT SDK per dispositivi
GLI SDK per dispositivi IoT di Microsoft Azure contengono codice che facilita la compilazione dei dispositivi e delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

I seguenti Azure IoT SDK per dispositivi sono disponibili per il download da GitHub:

* [Azure IoT SDK per dispositivi per C][lnk-c-device-sdk] scritto in ANSI C (C99) per la portabilità e la compatibilità multipiattaforma.
* [Azure IoT SDK per dispositivi per .NET][lnk-dotnet-device-sdk]
* [Azure IoT SDK per dispositivi per Java][lnk-java-device-sdk]
* [Azure IoT SDK per dispositivi per Node.js][lnk-node-device-sdk]
* [Microsoft Azure IoT SDK per dispositivi per Python 2.7][lnk-python-device-sdk]

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.
> 
> 

## <a name="os-platforms-and-hardware-compatibility"></a>Compatibilità delle piattaforme del sistema operativo e hardware
Per altre informazioni sulla compatibilità SDK con i dispositivi hardware specifici, vedere il [catalogo di dispositivi Azure Certified per IoT][lnk-certified].

## <a name="azure-iot-service-sdk"></a>Azure IoT SDK per servizi
Gli Azure IoT SDK per servizi contengono codice che consente la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire i dispositivi e la sicurezza.

I seguenti Azure IoT SDK per servizi sono disponibili per il download da GitHub:

* [Azure IoT SDK per servizi per .NET][lnk-dotnet-service-sdk]
* [Azure IoT SDK per servizi per Node.js][lnk-node-service-sdk]
* [Azure IoT SDK per servizi per Java][lnk-java-service-sdk]

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.
> 
> 

## <a name="azure-iot-gateway-sdk"></a>SDK per gateway IoT di Azure
Questo SDK per gateway IoT di Azure contiene l'infrastruttura e i moduli per creare soluzioni gateway IoT. È possibile estendere l'SDK per la creazione di gateway su misura per qualsiasi scenario end-to-end.

[Azure IoT Gateway SDK][lnk-gateway-sdk] può essere scaricato da GitHub.

## <a name="online-api-reference-documentation"></a>Documentazione di riferimento API online
Di seguito è riportato un elenco di collegamenti alla documentazione di riferimento all'API online per librerie di dispositivi, servizi e gateway di Azure IoT:

* [Internet of Things (IoT) .NET][lnk-dotnet-ref]
* [REST hub IoT][lnk-rest-ref]
* [Azure IoT SDK per dispositivi per C][lnk-c-ref]
* [Azure IoT SDK per dispositivi per Java][lnk-java-ref]
* [Azure IoT SDK per servizi per Java][lnk-java-service-ref]
* [Azure IoT SDK per dispositivi per Node.js][lnk-node-ref]
* [Azure IoT SDK per servizi per Node.js][lnk-node-service-ref]
* [Azure IoT Gateway SDK][lnk-gateway-ref]

## <a name="next-steps"></a>Passaggi successivi
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-devguide-endpoints]
* [Linguaggio di query dell'hub IoT per dispositivi gemelli e processi][lnk-devguide-query]
* [Quote e limitazioni][lnk-devguide-quotas]
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


