---
title: Versione corrente di Plug and Play IoT | Microsoft Docs
description: Informazioni su cosa include la versione di Plug and Play IoT corrente.
author: dominicbetts
ms.author: dobett
ms.date: 09/19/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 4cfc9ca14a4b344423a27d9683114de5275fb20b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580790"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>Cosa include la versione di Plug and Play IoT corrente

In questo articolo vengono riepilogati gli strumenti, gli SDK e le API che supportano la versione di Plug and Play IoT corrente. I numeri di versione visualizzati riflettono il numero di versione nel momento in cui il prodotto Plug and Play IoT è diventato disponibile a livello generale. I numeri di versione possono incrementare in seguito al rilascio.

## <a name="modeling-language"></a>Linguaggio di modellazione

[DTDL (Digital Twins Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Per altre informazioni su come funzionano i dispositivi Plug and Play IoT con DTDL, vedere [Convenzioni di Plug and Play IoT](concepts-convention.md).

## <a name="tools-and-utilities"></a>Strumenti e utilità

- Azure IoT Explorer 0.12.0.

    Per altre informazioni, vedere [Installare e usare Azure IoT Explorer](howto-use-iot-explorer.md).

- Estensione Visual Studio Code 1.0.0.

    Per altre informazioni, vedere [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md).

- Estensione di Visual Studio 2019 1.0.0.

    Per altre informazioni, vedere [Installare e usare gli strumenti di creazione di DTDL](howto-use-dtdl-authoring-tools.md).

- Estensione IoT per l'interfaccia della riga di comando di Azure 0.10.0.

    Per altre informazioni, vedere [Installare e usare l'estensione Azure IoT per l'interfaccia della riga di comando di Azure](howto-use-iot-pnp-cli.md).

    > [!TIP]
    > L'estensione Azure IoT include i comandi che consentono di certificare i dispositivi. Vedere `az iot product -h`.



## <a name="libraries-and-sdks"></a>Librerie e SDK

Per altre informazioni sulle librerie e sugli SDK, vedere [Microsoft SDK per Plug and Play IoT](libraries-sdks.md).

- SDK per dispositivi per C [VcPkg 2020-07-19](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)
- SDK per dispositivi per .NET [NuGet Microsoft.Azure.Devices.Client 1.30.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
- SDK per dispositivi per Java [Maven iot-device-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- SDK per dispositivi per Python [Pip azure-iot-device v2.2.0](https://pypi.org/project/azure-iot-device/)
- SDK per dispositivi per Node.js [npm azure-iot-device 1.17.1](https://www.npmjs.com/package/azure-iot-device)
- .NET - Servizio hub IoT [NuGet Microsoft.Azure.Devices 1.22.0](https://www.nuget.org/packages/Microsoft.Azure.Devices/)
- Java - Servizio hub IoT [Maven iot-service-client 1.25.0](https://search.maven.org/artifact/com.microsoft.azure.sdk.iot/iot-service-client)
- Node.js - Servizio hub IoT [npm azure-iothub 1.12.5](https://www.npmjs.com/package/azure-iothub)
- Python - Servizio hub IoT/Gemelli digitali [Pip azure-iot-hub 2.2.2](https://pypi.org/project/azure-iot-hub/)
- Parser del modello DTDL [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

### <a name="preview"></a>Anteprima

- Azure SDK for Embedded [1.0.0-preview.5](https://github.com/Azure/azure-sdk-for-c/releases/tag/1.0.0-preview.5)
- Middleware IoT Azure RTOS [v6.0_beta3](https://github.com/azure-rtos/azure-iot-preview/releases/tag/v6.0_beta3)

## <a name="rest-apis"></a>API REST

API REST [2020-09-30](https://docs.microsoft.com/rest/api/iothub).

Per altre informazioni, vedere la [Guida per sviluppatori di Plug and Play IoT](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>Hub IoT

Il servizio Plug and Play IoT è supportato dall'hub IoT in tutte le aree. Il servizio Plug and Play IoT è supportato solo dagli hub IoT di livello Standard o gratuito.

## <a name="announcements"></a>Annunci

Per gli annunci su Plug and Play IoT attuali e precedenti, vedere i post di blog seguenti:

- [Aggiornamento dell'anteprima pubblica (pubblicato il 29 agosto 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Preparare e certificare i dispositivi per Plug and Play IoT (pubblicato il 26 agosto 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [Plug and Play IoT è ora disponibile in anteprima (pubblicato il 22 agosto 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Creare con Azure IoT Central e Plug and Play IoT (pubblicato il 7 maggio 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

