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
ms.openlocfilehash: 3331a0a9a8d3fb5d028d801d334daf2dbfa25235
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337331"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Librerie e SDK Plug and Play

Le librerie e gli SDK Plug and Play gli sviluppatori consentono agli sviluppatori di compilare soluzioni di Internet delle cose usando un'ampia gamma di linguaggi di programmazione su più piattaforme. La tabella seguente include collegamenti a esempi e guide introduttive utili per iniziare:

## <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDK per Internet delle cose Plug and Play

**SDK del dispositivo**

| Linguaggio | Pacchetto | Repository di codice | Esempi | Avvio rapido | Informazioni di riferimento |
|---|---|---|---|---|---|
| C-dispositivo | [1.3.9 vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Esempi](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Connettersi all'hub IoT](quickstart-connect-device-c.md) | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-dispositivo | [1.27.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Connettersi all'hub IoT](quickstart-connect-device-csharp.md) | [Riferimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java-dispositivo | [1.24.0 Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Connettersi all'hub IoT](quickstart-connect-device-java.md) | [Riferimento](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-dispositivo | [PIP 2.1.4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Connettersi all'hub IoT](quickstart-connect-device-python.md) | [Riferimento](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Nodo-dispositivo | [1.17.0 NPM](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Connettersi all'hub IoT](quickstart-connect-device-node.md) | [Riferimento](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

**SDK servizi (anteprima)**

| Linguaggio | Pacchetto | Repository di codice | Esempi | Avvio rapido | Informazioni di riferimento |
|---|---|---|---|---|---|
| .NET-servizio hub Internet-anteprima | [NuGet 1.27.1-Preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Esempi](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | na | [Riferimento](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet) |
| Anteprima servizio dell'hub Java-Internet | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Esempi](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | na | [Riferimento](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable) |
| Python-tutto Hub/anteprima servizio dispositivi gemelli digitali | [PIP 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Esempi](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interagire con l'API Digital gemelli dell'hub Internet](quickstart-service-python.md) | [Riferimento](https://docs.microsoft.com/python/api/azure-iot-hub/?view=azure-python) |
| Node-Internet Hub/anteprima servizio dispositivi gemelli digitali | [NPM 1.0.0-PNP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Esempi](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interagire con l'API Digital gemelli dell'hub Internet](quickstart-service-node.md) | [Riferimento](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest) |

## <a name="next-steps"></a>Passaggi successivi

Per provare gli SDK e le librerie, vedere la [Guida](concepts-developer-guide.md) per gli sviluppatori e le guide introduttive del [dispositivo](quickstart-connect-device-c.md) e dei [Servizi](quickstart-service-node.md).
