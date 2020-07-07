---
title: Librerie e SDK Plug and Play
description: Informazioni sulle librerie del dispositivo e del servizio disponibili per lo sviluppo di soluzioni Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80064338"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Librerie e SDK Plug and Play

Le librerie e gli SDK Plug and Play gli sviluppatori consentono agli sviluppatori di compilare soluzioni di Internet delle cose usando un'ampia gamma di linguaggi di programmazione su più piattaforme. La tabella seguente include collegamenti a esempi e guide introduttive utili per iniziare:

## <a name="microsoft-supported-libraries-and-sdks"></a>Librerie e SDK supportati da Microsoft

| Piattaforma | Libreria/pacchetto | Codice sorgente | Esempio | Avvio rapido | Riferimento |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Device SDK in apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Esempi di client gemelli digitali](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-c-linux.md) | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [SDK per dispositivi in vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Esempi di client gemelli digitali](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-c-windows.md) | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [SDK per dispositivi in EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Esempi di client gemelli digitali](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [SDK per dispositivi nell'IDE di Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Esempi di client gemelli digitali](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [SDK per dispositivi in CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Esempi di client gemelli digitali](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Esempi di dispositivi gemelli digitali](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-csharp.md) | [Riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Esempi di dispositivi gemelli digitali](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-java.md) | [Riferimento](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Esempi di dispositivi gemelli digitali](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-node.md) | [Riferimento](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Supporto dell'hub IoT

Le funzionalità per i dispositivi Plug and Play sono supportate solo dagli [hub Internet di livello gratuito e standard](../iot-hub/iot-hub-scaling.md).

## <a name="next-steps"></a>Passaggi successivi

Oltre alle librerie e agli SDK per dispositivi, è possibile usare le API REST per interagire con i repository del modello.