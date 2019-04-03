---
title: Selezionare e distribuire il Centro sicurezza di Azure per l'agente di IoT Preview | Microsoft Docs
description: Informazioni su come selezionare e distribuire il Centro sicurezza di Azure per IoT gli agenti protezione nei dispositivi IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2019
ms.author: mlottner
ms.openlocfilehash: 8f5a6187b0a651da9dd8de1cb5670a8faffded1a
ms.sourcegitcommit: d83fa82d6fec451c0cb957a76cfba8d072b72f4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58862421"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selezionare e distribuire un agente protezione nel dispositivo IoT

> [!IMPORTANT]
> Il Centro sicurezza di Azure per IoT è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il Centro sicurezza di Azure (ASC) per IoT fornisce le architetture di riferimento per gli agenti di protezione che monitorano e raccolgono i dati dai dispositivi IoT.
Visualizzare [architettura di riferimento di sicurezza dell'agente](security-agent-architecture.md) per altre informazioni.

Gli agenti vengono sviluppati come progetti open source e sono disponibili in due versioni: <br> [C](https://aka.ms/iot-security-github-c), e [ C# ](https://aka.ms/iot-security-github-cs).

In questo articolo viene spiegato come: 
> [!div class="checklist"]
> * Confrontare versioni di agente di protezione
> * Individuare le piattaforme supportate dell'agente
> * Scegliere la versione corretta dell'agente per la soluzione

## <a name="understand-security-agent-options"></a>Informazioni sulle opzioni di sicurezza dell'agente

Ogni Centro sicurezza di AZURE per la versione dell'agente di sicurezza IoT offre lo stesso set di funzionalità e supporta le opzioni di configurazione simile. 

L'agente protezione basata su C ha un impatto minore sulla memoria e rappresenta la scelta ideale per i dispositivi con un numero inferiore di risorse disponibile. 

|     | Agente di protezione basato sul linguaggio C | C#-agente di protezione basato su |
| --- | ----------- | --------- |
| Aprire origine | Disponibile nel [licenza MIT](https://en.wikipedia.org/wiki/MIT_License) in [Github](https://aka.ms/iot-security-github-cs) | Disponibile nel [licenza MIT](https://en.wikipedia.org/wiki/MIT_License) in [Github](https://aka.ms/iot-security-github-c) |
| Linguaggio di sviluppo    | C | C# |
| Piattaforme supportate di Windows? | No  | Sì |
| Prerequisiti di Windows | --- | [WMI](https://docs.microsoft.com/en-us/windows/desktop/wmisdk/) |
| Piattaforme supportate di Linux? | Sì, x64 e x86 | Sì, solo x64 |
| Prerequisiti di Linux | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plug-in | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plug-in, sudo, netstat, iptables |
| Footprint su disco | 10.5 MB | 90MB |
| Footprint di memoria (in Media) | 5.5 MB | 33MB |
| [Autenticazione](concept-security-agent-authentication-methods.md) all'Hub IoT | Sì | Sì |
| I dati di sicurezza [raccolta](how-to-agent-configuration.md#supported-security-events) | Sì | Sì |
| Aggregazione di eventi | Sì | Sì |
| Configurazione remota tramite [dispositivo gemello del modulo security](concept-security-module.md) | Sì | Sì |


## <a name="choose-an-agent-flavor"></a>Scegliere una versione dell'agente 

Rispondere alle domande seguenti sui dispositivi IoT per selezionare l'agente corretto:

- Usi _Windows Server_ oppure _Windows IoT Core_? 

    [Distribuire un C#-agente di protezione per Windows basato su](how-to-deploy-windows-cs.md).

- Si usa una distribuzione Linux con x86 architettura? 

    [Distribuire un agente protezione basata su C per Linux](how-to-deploy-linux-c.md).

- Si usa una distribuzione Linux con x64 architettura?

    È possibile usare una versione dell'agente. <br>
    [Distribuire un agente protezione basata su C per Linux](how-to-deploy-linux-c.md) e/o [Distribuisci un C#-basato su agente protezione per Linux](how-to-deploy-linux-cs.md).

Entrambi i tipi agente offrono lo stesso set di funzionalità e supportano le opzioni di configurazione simile.
Visualizzare [confronto tra l'agente protezione](how-to-deploy-agent.md#understand-security-agent-options) per altre informazioni.

## <a name="supported-platforms"></a>Piattaforme supportate

Nell'elenco seguente include tutte le piattaforme attualmente supportate.

|Centro sicurezza di AZURE per l'agente di IoT |Sistema operativo |Architettura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core compilazione 17763 |x64|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di configurazione, continuare alla Guida alle procedure per la configurazione dell'agente. 
> [!div class="nextstepaction"]
> [Configurazione dell'agente Guida alle procedure](./how-to-agent-configuration.md)
