---
title: Informazioni sugli SDK dell'hub IoT | Documentazione Microsoft
description: 'Guida per gli sviluppatori: informazioni e collegamenti ai vari Azure IoT SDK per dispositivi e servizi che è possibile usare per compilare app per dispositivo e app back-end.'
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/12/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 71d02e3f8bf0757d7ea0622e76b95acda1fefc95
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Comprendere e usare gli SDK per l'hub IoT di Azure

Esistono due categorie di Software Development Kit (SDK) per l'uso con l'hub IoT:

* Gli **SDK per dispositivi** consentono di compilare le app eseguite su dispositivi IoT. Queste app inviano la telemetria all'hub IoT e, facoltativamente, ricevono messaggi, processi, metodi o aggiornamenti dei dispositivi gemelli dall'hub IoT.

* Gli **SDK per servizi** consentono di gestire l'hub IoT e, facoltativamente, inviare messaggi, pianificare processi, richiamare metodi diretti o inviare gli aggiornamenti delle proprietà desiderati ai dispositivi IoT.

[Questo articolo][lnk-benefits-blog] contiene informazioni sui vantaggi offerti dallo sviluppo con Azure IoT SDK.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>SDK dispositivo IoT Azure

GLI SDK per dispositivi IoT di Microsoft Azure contengono codice che facilita la compilazione dei dispositivi e delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

Azure IoT Hub SDK per dispositivi per .NET: 
* Installare da [Nuget][lnk-nuget-csharp-device]
* [Codice sorgente][lnk-dotnet-sdk]
* [Informazioni di riferimento sulle API][lnk-dotnet-ref]

Azure IoT Hub SDK per dispositivi per C: scritto in ANSI C (C99) per la portabilità e la compatibilità multipiattaforma
* Installare da [apt-get, MBED, IDE Arduino o Nuget][lnk-c-package]
* [Codice sorgente][lnk-c-sdk]
* [Informazioni di riferimento sulle API][lnk-c-ref]

Azure IoT Hub SDK per dispositivi per Java: 
* Aggiungere al progetto [Maven][lnk-maven-device]
* [Codice sorgente][lnk-java-sdk]
* [Informazioni di riferimento sulle API][lnk-java-ref]

Azure IoT Hub SDK per dispositivi per Node.js: 
* Installare da [npm][lnk-npm-device]
* [Codice sorgente][lnk-node-sdk]
* [Informazioni di riferimento sulle API][lnk-node-ref]

Azure IoT Hub SDK per dispositivi per Python: 
* Installare da [pip][lnk-pip-device]
* [Codice sorgente][lnk-python-sdk]

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilità delle piattaforme del sistema operativo e hardware

Per altre informazioni sulla compatibilità SDK con i dispositivi hardware specifici, vedere il [catalogo di dispositivi Azure Certified per IoT][lnk-certified] o lo specifico repository.

## <a name="azure-iot-service-sdks"></a>Azure IoT SDK per servizi

I componenti Azure Iot SDK per servizi contengono codice che facilita la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire dispositivi e sicurezza.

Azure IoT Hub SDK per servizi per .NET:
* Scaricare da [Nuget][lnk-nuget-csharp-service]
* [Codice sorgente][lnk-dotnet-sdk]
* [Informazioni di riferimento sulle API][lnk-dotnet-service-ref]

Azure IoT Hub SDK per servizi per Java: 
* Aggiungere al progetto [Maven][lnk-maven-service]
* [Codice sorgente][lnk-java-sdk]
* [Informazioni di riferimento sulle API][lnk-java-service-ref]

Azure IoT Hub SDK per servizi per Node.js: 
* Scaricare da [npm][lnk-npm-service]
* [Codice sorgente][lnk-node-sdk]
* [Informazioni di riferimento sulle API][lnk-node-service-ref]

Azure IoT Hub SDK per servizi per Python: 
* Scaricare da [pip][lnk-pip-service]
* [Codice sorgente][lnk-python-sdk]

Azure IoT Hub SDK per servizi per C: 
* Scaricare da [apt-get, MBED, IDE Arduino o Nuget][lnk-c-package]
* [Codice sorgente][lnk-c-sdk]

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.


## <a name="next-steps"></a>Passaggi successivi

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint dell'hub IoT][lnk-devguide-endpoints]
* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi][lnk-devguide-query]
* [Quote e limitazioni][lnk-devguide-quotas]
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt]
* [Informazioni di riferimento sulle API REST dell'hub IoT][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
