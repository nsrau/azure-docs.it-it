---
title: Plug and Play Bridge | Microsoft Docs
description: Informazioni sul Bridge Plug and Play e su come usarlo per connettere i dispositivi esistenti collegati a un gateway Windows o Linux come dispositivi Plug and Play.
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580472"
---
# <a name="iot-plug-and-play-bridge"></a>Bridge Plug and Play

Il Plug and Play Bridge è un'applicazione open source per connettere i dispositivi esistenti collegati al gateway Windows o Linux come dispositivi Plug and Play. Dopo l'installazione e la configurazione dell'applicazione nel computer Windows o Linux, è possibile usarla per connettere i dispositivi collegati a un hub Internet. È possibile usare il Bridge per eseguire il mapping delle interfacce Plug and Play ai dati di telemetria inviati dai dispositivi collegati, usare le proprietà del dispositivo e richiamare i comandi.

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="Sul lato sinistro sono presenti un paio di sensori esistenti collegati (sia cablati che wireless) a un PC Windows o Linux che contiene il Plug and Play Bridge. Il Plug and Play Bridge si connette quindi a un hub Internet delle cose sul lato destro":::

Internet delle cose Plug and Play Bridge può essere distribuito come eseguibile autonomo su qualsiasi dispositivo, PC industriale, server o gateway che esegue Windows 10 o Linux. Può anche essere compilato nel codice dell'applicazione. Un semplice file JSON di configurazione indica ai Plug and Play Bridge i dispositivi/periferiche collegati che devono essere esposti ad Azure.

## <a name="supported-protocols-and-sensors"></a>Protocolli e sensori supportati

Per impostazione predefinita, i tipi di periferiche Plug and Play Bridge supportano i seguenti tipi di periferiche, con collegamenti alla documentazione dell'adapter:

|Periferica|Windows|Linux|
|---------|---------|---------|
|[Bluetooth LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |Sì|No|
|[Fotocamere](https://aka.ms/iot-pnp-bridge-camera)               |Sì|No|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |Sì|Sì|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |Sì|Sì|
|[Serie](https://aka.ms/iot-pnp-bridge-serial)                |Sì|Sì|
|[Periferiche USB di Windows](https://aka.ms/iot-pnp-bridge-usb)  |Sì|Non applicabile|

>[!Important]
>Gli sviluppatori possono estendere il Plug and Play Bridge per supportare altri protocolli del dispositivo tramite le istruzioni nella documentazione per gli sviluppatori di Internet delle cose **[plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)**.

## <a name="prerequisites"></a>Prerequisiti

### <a name="os-platform"></a>Piattaforma del sistema operativo

Sono supportate le piattaforme e le versioni del sistema operativo seguenti:

|Piattaforma  |Versioni supportate  |
|---------|---------|
|Windows 10 |     Sono supportati tutti gli SKU di Windows. Ad esempio: Internet delle cose Enterprise, server, desktop, Internet delle cose. *Per la funzionalità di monitoraggio dell'integrità della fotocamera è consigliabile usare 20H1 o una compilazione successiva. Tutte le altre funzionalità sono disponibili in tutte le build di Windows 10.*  |
|Linux     |Testato e supportato in Ubuntu 18,04, la funzionalità su altre distribuzioni non è stata testata.         |
||

### <a name="hardware"></a>Hardware

- Qualsiasi piattaforma hardware in grado di supportare gli SKU e le versioni precedenti del sistema operativo.
- I sensori e le periferiche Serial, USB, Bluetooth e della fotocamera sono supportati in modalità nativa. Il Bridge Plug and Play può essere esteso per supportare qualsiasi periferica o sensore personalizzato ([vedere la sezione periferiche precedente](#iot-plug-and-play-bridge)).

### <a name="development-environment"></a>Ambiente di sviluppo

Per compilare, estendere e sviluppare il Plug and Play Bridge, è necessario:  

- Un ambiente di sviluppo che supporta la compilazione di C++, ad esempio [Visual Studio (community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): assicurarsi di includere il carico di lavoro sviluppo di applicazioni desktop con c++ quando si installa Visual Studio.
- [CMake](https://cmake.org/download/) : quando si installa CMake, selezionare l'opzione `Add CMake to the system PATH` .
- Se si esegue la compilazione in Windows, sarà anche necessario scaricare Windows 17763 SDK: [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [SDK del dispositivo C dell'hub Azure](https://github.com/Azure/azure-iot-sdk-c). Gli script di compilazione inclusi in questo repository clonano automaticamente l'SDK di Azure Internet necessario per l'utente.

### <a name="azure-iot-products-and-tools"></a>Prodotti e strumenti di Azure.

- **Hub** di Azure: per connettere il dispositivo a è necessario un [Hub](https://docs.microsoft.com/azure/iot-hub/) di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare. Se non si dispone di un hub Internet delle cose, [seguire queste istruzioni per crearne uno](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli).

> [!Note]
> Plug and Play IoT è attualmente disponibile negli hub IoT creati nelle aree Stati Uniti centrali, Europa settentrionale e Giappone orientale. Il supporto di Internet di Plug and Play IoT non è incluso negli hub IoT di livello base. Per interagire con il dispositivo Plug and Play, è possibile usare lo strumento Azure Internet Explorer. [Scaricare e installare la versione di Azure IoT Explorer più recente](./howto-use-iot-explorer.md) per il sistema operativo in uso.

## <a name="iot-plug-and-play-bridge-architecture"></a>Architettura Plug and Play Bridge

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="Sul lato sinistro sono presenti un paio di sensori esistenti collegati (sia cablati che wireless) a un PC Windows o Linux che contiene il Plug and Play Bridge. Il Plug and Play Bridge si connette quindi a un hub Internet delle cose sul lato destro":::

## <a name="download-iot-plug-and-play-bridge"></a>Scarica Plug and Play Bridge

È possibile scaricare una versione predefinita del Bridge con gli adapter supportati in Internet delle cose [plug and Play le versioni Bridge](https://aka.ms/iot-pnp-bridge-releases) ed espandere l'elenco degli asset per la versione più recente. Scaricare la versione più recente dell'applicazione per il sistema operativo in uso.

È anche possibile scaricare e visualizzare il codice sorgente del [plug and Play Bridge su GitHub](https://aka.ms/bridge).

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di una panoramica dell'architettura di Internet delle cose Plug and Play Bridge, i passaggi successivi sono ulteriori informazioni su:

- [Come usare il Bridge Plug and Play](./howto-use-iot-pnp-bridge.md)
- [Vedere le informazioni di riferimento per gli sviluppatori di GitHub per Internet Plug and Play Bridge](https://aka.ms/iot-pnp-bridge-dev-doc)
- [Plug and Play Bridge su GitHub](https://aka.ms/iotplugandplaybridge)
