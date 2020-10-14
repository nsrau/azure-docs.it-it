---
title: Librerie e SDK Plug and Play
description: Informazioni sulle librerie del dispositivo e del servizio disponibili per lo sviluppo di soluzioni Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6ea9440c153e26e36aa17b55c4cb712dd08d4508
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042678"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK per Internet delle cose Plug and Play

Le librerie e gli SDK Plug and Play gli sviluppatori consentono agli sviluppatori di compilare soluzioni di Internet delle cose usando un'ampia gamma di linguaggi di programmazione su più piattaforme. La tabella seguente include collegamenti a esempi e guide introduttive utili per iniziare:

## <a name="device-sdks"></a>SDK del dispositivo

| Linguaggio | Pacchetto | Repository di codice | Esempi | Avvio rapido | Informazioni di riferimento |
|---|---|---|---|---|---|
| C-dispositivo | [1.3.9 vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Esempi](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Connettersi all'hub IoT](quickstart-connect-device-c.md) | [Riferimento](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-dispositivo | [1.31.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Esempi](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Connettersi all'hub IoT](quickstart-connect-device-csharp.md) | [Riferimento](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Java-dispositivo | [1.26.0 Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Connettersi all'hub IoT](quickstart-connect-device-java.md) | [Riferimento](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python-dispositivo | [2.3.0 pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Connettersi all'hub IoT](quickstart-connect-device-python.md) | [Riferimento](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Nodo-dispositivo | [1.17.2 NPM](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Connettersi all'hub IoT](quickstart-connect-device-node.md) | [Riferimento](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| Dispositivo C incorporato | N/D | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Esempi](howto-use-embedded-c.md#samples) | [Come usare il linguaggio C incorporato](howto-use-embedded-c.md) | N/D

## <a name="service-sdks"></a>SDK per servizi

| Piattaforma  | Pacchetto | Repository di codice | Esempi | Avvio rapido | Informazioni di riferimento |
|---|---|---|---|---|---|
| .NET-servizio hub Internet | [1.27.1 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Esempi](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/D | [Riferimento](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Servizio Hub Java-Internet | [1.26.0 Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/D | [Riferimento](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| Servizio hub nodo-Internet | [1.13.0 NPM](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/D | [Riferimento](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Python-servizio dispositivi gemelli digitali | [PIP 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Esempi](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interagire con l'API Digital gemelli dell'hub Internet](quickstart-service-python.md) | N/D |
| Node-Digital Gemells Service | [1.13.0 NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interagire con l'API Digital gemelli dell'hub Internet](quickstart-service-node.md) | N/D |

## <a name="next-steps"></a>Passaggi successivi

Per provare gli SDK e le librerie, vedere la  [Guida](concepts-developer-guide-device-csharp.md) per gli sviluppatori e le guide introduttive del [dispositivo](quickstart-connect-device-c.md) e dei [Servizi](quickstart-service-node.md).