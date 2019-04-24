---
title: Informazioni sugli SDK dell'hub IoT | Documentazione Microsoft
description: 'Guida per gli sviluppatori: informazioni e collegamenti ai vari Azure IoT SDK per dispositivi e servizi che è possibile usare per compilare app per dispositivo e app back-end.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: e51313bbed21459de9f717edd123887caed18f4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60400661"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Comprendere e usare gli SDK per l'hub IoT di Azure

Esistono due categorie di Software Development Kit (SDK) per l'uso con l'hub IoT:

* Gli **SDK per dispositivi hub IoT** consentono di creare app eseguite nei dispositivi IoT usando il client per dispositivi o il client per moduli. Queste app inviano la telemetria all'hub IoT e, facoltativamente, ricevono messaggi, processi, metodi o aggiornamenti dei dispositivi gemelli dall'hub IoT.  È anche possibile usare il client per moduli per creare [moduli](../iot-edge/iot-edge-modules.md) per il [runtime di Azure IoT Edge](../iot-edge/about-iot-edge.md).

* Gli **SDK per il servizio hub IoT** consentono di creare applicazioni back-end per gestire l'hub IoT e, facoltativamente, inviare messaggi, pianificare processi, richiamare metodi diretti o inviare gli aggiornamenti delle proprietà desiderati ai dispositivi o al moduli IoT.

Verrà inoltre fornito un set di SDK per l'uso del [servizio Device Provisioning](../iot-dps/about-iot-dps.md).
* Gli **SDK per i dispositivi di provisioning** consentono di creare app eseguibili nei dispositivi IoT per comunicare con il servizio Device Provisioning.

* Gli **SDK per il servizio di provisioning** consentono di creare applicazioni back-end per gestire le registrazioni nel servizio Device Provisioning.

Altre informazioni sui [ vantaggi offerti dallo sviluppo con gli SDK di Azure IoT](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]


### <a name="os-platform-and-hardware-compatibility"></a>Compatibilità delle piattaforme del sistema operativo e hardware

È possibile trovare le piattaforme supportate per gli SDK in [Supporto della piattaforma Azure IoT SDK](iot-hub-device-sdk-platform-support.md).

Per altre informazioni sulla compatibilità SDK con i dispositivi hardware specifici, vedere il [catalogo di dispositivi Azure Certified per IoT](https://catalog.azureiotsolutions.com/) o lo specifico repository.

## <a name="azure-iot-hub-device-sdks"></a>SDK per dispositivi hub IoT di Azure

Microsoft Azure IoT SDK per dispositivi contiene codice che facilita la compilazione delle applicazioni che si connettono e sono gestite dai servizi hub IoT di Azure.

Azure IoT Hub SDK per dispositivi per .NET: 

* Scaricare da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Lo spazio dei nomi è Microsoft.Azure.Devices.Clients, che contiene i client per dispositivi hub IoT (DeviceClient, ModuleClient).
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Informazioni di riferimento sui moduli](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

Azure IoT Hub SDK per dispositivi per C (ANSI C - C99):

* Installare da [apt-get, MBED, IDE Arduino o iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c)
* [Compilare l'SDK per dispositivi C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
* [Informazioni di riferimento sui moduli](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Porting dell'SDK per C in altre piattaforme](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Documentazione per sviluppatori](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) per informazioni su compilazione incrociata, introduzione alle diverse piattaforme e così via.
* [Informazioni sul consumo di risorse Azure IoT SDK per C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub SDK per dispositivi per Java: 

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [Informazioni di riferimento sui moduli](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

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

## <a name="azure-iot-hub-service-sdks"></a>SDK per il servizio hub IoT di Azure

I componenti Azure Iot SDK per servizi contengono codice che facilita la compilazione di applicazioni che interagiscono direttamente con l'hub IoT per gestire dispositivi e sicurezza.

Azure IoT Hub SDK per servizi per .NET:

* Scaricare da [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Lo spazio dei nomi è Microsoft.Azure.Devices, che contiene i client per il servizio hub IoT (RegistryManager, ServiceClients).
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

Azure IoT Hub SDK per servizi per Java: 

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
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

## <a name="microsoft-azure-provisioning-sdks"></a>SDK di provisioning di Microsoft Azure

**Gli SDK del provisioning di Microsoft Azure** consentono di eseguire il provisioning dei dispositivi all'hub IoT usando il [servizio di provisioning di dispositivi](../iot-dps/about-iot-dps.md).

SDK dei dispositivi di provisioning e di servizio di Azure per C#:

* Scaricare l'[SDK per dispositivi](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) e l'[SDK per il servizio](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) da NuGet.
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-csharp/)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

SDK dei dispositivi di provisioning e di servizio di Azure per C:

* Installare da [apt-get, MBED, IDE Arduino o iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)

SDK dei dispositivi di provisioning e di servizio di Azure per Java:

* Aggiungere al progetto [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)
* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

SDK dei dispositivi di provisioning e di servizio di Azure per Node.js:

* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [Informazioni di riferimento sulle API](https://docs.microsoft.com/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Scaricare l'[SDK per dispositivi](https://badge.fury.io/js/azure-iot-provisioning-device) e l'[SDK per il servizio](https://badge.fury.io/js/azure-iot-provisioning-service) da npm

SDK dei dispositivi di provisioning e di servizio di Azure per Python:

* [Codice sorgente](https://github.com/Azure/azure-iot-sdk-python)
* Scaricare l'[SDK per dispositivi](https://pypi.org/project/azure-iot-provisioning-device-client/) e l'[SDK per il servizio](https://pypi.org/project/azure-iothub-provisioningserviceclient/) da pip

## <a name="next-steps"></a>Passaggi successivi

La piattaforma di Azure IoT SDK fornisce anche un set di strumenti per facilitare lo sviluppo:
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics): uno strumento multipiattaforma da riga di comando che consente di diagnosticare i problemi relativi alla connessione all'hub IoT.
* [device-explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer): un'applicazione desktop di Windows per la connessione all'hub IoT.

Documentazione pertinente relativa allo sviluppo tramite gli SDK IoT di Azure:
* Informazioni su [come gestire le funzionalità di connettività e messaggistica affidabile](iot-hub-reliability-features-in-sdks.md) con gli SDK per l'hub IoT.
* Informazioni su come [sviluppare per piattaforme per dispositivi mobili](iot-hub-how-to-develop-for-mobile-devices.md) come iOS e Android.
* [Supporto della piattaforma di Azure IoT SDK](iot-hub-device-sdk-platform-support.md)


Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Endpoint hub IoT](iot-hub-devguide-endpoints.md)
* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md)
* [Quote e limitazione](iot-hub-devguide-quotas-throttling.md)
* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md)
* [Informazioni di riferimento sulle API REST dell'hub IoT](/rest/api/iothub/)
