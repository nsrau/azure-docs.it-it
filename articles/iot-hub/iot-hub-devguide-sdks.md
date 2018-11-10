---
title: Informazioni sugli SDK dell'hub IoT | Documentazione Microsoft
description: 'Guida per gli sviluppatori: informazioni e collegamenti ai vari Azure IoT SDK per dispositivi e servizi che è possibile usare per compilare app per dispositivo e app back-end.'
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 9daca1876a291cb1f726e7c0eb4840c57f5c54c8
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741318"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Comprendere e usare gli SDK per l'hub IoT di Azure

Esistono tre categorie di Software Development Kit (SDK) per l'uso con l'hub IoT:

* Gli **SDK per dispositivi** consentono di compilare le app eseguite su dispositivi IoT usando il client per dispositivi o il client per moduli. Queste app inviano la telemetria all'hub IoT e, facoltativamente, ricevono messaggi, processi, metodi o aggiornamenti dei dispositivi gemelli dall'hub IoT.  È anche possibile usare il client per moduli per creare [moduli](../iot-edge/iot-edge-modules.md) per il [runtime di Azure IoT Edge](../iot-edge/about-iot-edge.md).

* Gli **SDK per servizi** consentono di gestire l'hub IoT e, facoltativamente, inviare messaggi, pianificare processi, richiamare metodi diretti o inviare gli aggiornamenti delle proprietà desiderati ai dispositivi o al moduli IoT.

* **Gli SDK di provisioning di dispositivi** consentono di eseguire il provisioning dei dispositivi all'hub IoT usando il [servizio provisioning di dispositivi](../iot-dps/about-iot-dps.md).

Altre informazioni sui [ vantaggi offerti dallo sviluppo con gli SDK di Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>SDK dispositivo IoT Azure

GLI SDK per dispositivi IoT di Microsoft Azure contengono codice che facilita la compilazione dei dispositivi e delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

Azure IoT Hub SDK per dispositivi per .NET: 

* Installare dal [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Informazioni di riferimento sui moduli](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

L'SDK dell'hub IoT di per dispositivi per C, scritto in ANSI C (C99) per la portabilità e la compatibilità multipiattaforma:

* Installare da [apt-get, MBED, IDE Arduino o Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Informazioni di riferimento sui moduli](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

Azure IoT Hub SDK per dispositivi per Java: 

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Informazioni di riferimento sui moduli](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

Azure IoT Hub SDK per dispositivi per Node.js: 

* Installare da [npm](https://www.npmjs.com/package/azure-iot-device)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-node)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Informazioni di riferimento sui moduli](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub SDK per dispositivi per Python: 

* Installare da [pip](https://pypi.python.org/pypi/azure-iothub-device-client/)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-python)
* Riferimento all'API: vedere [riferimento all'API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

Azure IoT Hub SDK per dispositivi per iOS: 

* Installare da [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Esempi](https://github.com/Azure-Samples/azure-iot-samples-ios)
* Riferimento all'API: vedere [riferimento all'API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>Compatibilità delle piattaforme del sistema operativo e hardware

È possibile trovare le piattaforme supportate per gli SDK in [Supporto della piattaforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md).

Per altre informazioni sulla compatibilità SDK con i dispositivi hardware specifici, vedere il [catalogo di dispositivi Azure Certified per IoT](https://catalog.azureiotsuite.com/) o lo specifico repository.

## <a name="azure-iot-service-sdks"></a>Azure IoT SDK per servizi

I componenti Azure Iot SDK per servizi contengono codice che facilita la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire dispositivi e sicurezza.

Azure IoT Hub SDK per servizi per .NET:

* Scaricare da [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub SDK per servizi per Java: 

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub SDK per servizi per Node.js: 

* Scaricare da [npm](https://www.npmjs.com/package/azure-iothub)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-node)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub SDK per servizi per Python: 

* Scaricare da [pip](https://pypi.python.org/pypi/azure-iothub-service-client/)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-python)

Azure IoT Hub SDK per servizi per C: 

* Scaricare da [apt-get, MBED, IDE Arduino o Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub SDK per servizi per iOS: 

* Installare da [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Esempi](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> Vedere i file Leggimi nei repository GitHub per informazioni sull'uso di gestori di pacchetti specifici per piattaforma e linguaggio e installare file binari e dipendenze nel computer di sviluppo.

## <a name="device-provisioning-sdks"></a>SDK del provisioning di dispositivi

**Gli SDK del provisioning di Microsoft Azure** consentono di eseguire il provisioning dei dispositivi all'hub IoT usando il [servizio di provisioning di dispositivi](../iot-dps/about-iot-dps.md).

SDK dei dispositivi di provisioning e di servizio di Azure per C#:

* [SDK del client dei dispositivi di provisioning](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [SDK del client del servizio di provisioning](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

SDK dei dispositivi di provisioning e di servizio di Azure per Java:

* [SDK del client dei dispositivi di provisioning](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [SDK del client del servizio di provisioning](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

SDK dei dispositivi di provisioning e di servizio di Azure per Node.js:

* [SDK del client dei dispositivi di provisioning](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [SDK del client del servizio di provisioning](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

SDK dei dispositivi di provisioning e di servizio di Azure per Python:

* [SDK del client dei dispositivi di provisioning](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [SDK del client del servizio di provisioning](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

SDK dei dispositivi di provisioning e di servizio di Azure per C:

* [SDK del client dei dispositivi di provisioning](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [SDK del client del servizio di provisioning](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>Passaggi successivi

La piattaforma di Azure IoT SDK fornisce anche un set di strumenti per facilitare lo sviluppo:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uno strumento multipiattaforma da riga di comando che consente di diagnosticare i problemi relativi alla connessione all'hub IoT.
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): un'applicazione desktop di Windows per la connessione all'hub IoT.

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint hub IoT](iot-hub-devguide-endpoints.md)
* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md)
* [Quote e limitazione](iot-hub-devguide-quotas-throttling.md)
* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md)
* [Informazioni di riferimento sulle API REST dell'hub IoT](/rest/api/iothub/)
* [Supporto della piattaforma di Azure IoT SDK](iot-hub-device-sdk-platform-support.md)
