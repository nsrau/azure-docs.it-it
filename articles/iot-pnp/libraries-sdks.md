---
title: Librerie Plug and Play IoT e SDK
description: Informazioni sul dispositivo e sulle librerie di servizi disponibili per lo sviluppo di soluzioni abilitate per IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064338"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Librerie Plug and Play IoT e SDK

Le librerie IoT Plug and Play e SDK consentono agli sviluppatori di creare soluzioni IoT utilizzando una varietà di linguaggi di programmazione su più piattaforme. Nella tabella seguente sono inclusi collegamenti a esempi e guide introduttive che consentono di iniziare:

## <a name="microsoft-supported-libraries-and-sdks"></a>Librerie e SDK supportati da Microsoft

| Piattaforma | Libreria/Pacchetto | Codice sorgente | Esempio | Guida introduttiva | Riferimento |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Device SDK su apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Campioni client Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-c-linux.md) | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [SDK del dispositivo in Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Campioni client Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-c-windows.md) | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [SDK del dispositivo su EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Campioni client Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Device SDK nell'IDE di Arduino](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Campioni client Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [SDK del dispositivo su CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Campioni client Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Riferimento](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Campioni Digital Twin](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-csharp.md) | [Riferimento](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Campioni Digital Twin](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-java.md) | [Riferimento](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Campioni Digital Twin](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Connettersi all'hub IoT](./quickstart-connect-pnp-device-node.md) | [Riferimento](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Supporto dell'hub IoT

Le funzionalità dei dispositivi IoT Plug and Play sono supportate solo da [hub IoT](../iot-hub/iot-hub-scaling.md)di livello standard e gratuito.

## <a name="next-steps"></a>Passaggi successivi

Oltre agli SDK e alle librerie del dispositivo, è possibile usare le API REST per interagire con i repository di modelli.