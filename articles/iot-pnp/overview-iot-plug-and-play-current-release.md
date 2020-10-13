---
title: Versione corrente di Plug and Play IoT | Microsoft Docs
description: Informazioni su cosa include la versione di Plug and Play IoT corrente.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c7ebcdac3174f7eb497ae3e976386ab92212e1ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715566"
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

- SDK per dispositivi per C [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- SDK per dispositivi per C incorporati [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- SDK per dispositivi .NET [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- SDK per dispositivi Java [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- SDK per dispositivi Python [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- SDK per dispositivi Node.js [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - servizio hub IoT [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - servizio hub IoT [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - servizio hub IoT [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - servizio hub IoT/Gemelli digitali [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- Parser del modello DTDL [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

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
