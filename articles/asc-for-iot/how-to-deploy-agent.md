---
title: Selezionare e distribuire il Centro sicurezza di Azure per l'agente Internet Microsoft Docs
description: Informazioni su come selezionare e distribuire il Centro sicurezza di Azure per gli agenti di sicurezza di Internet delle cose.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: ffc6ea447ae90649be0455abbed6245c078e518d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596351"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selezionare e distribuire un agente di sicurezza nel dispositivo Internet delle cose

Il Centro sicurezza di Azure per l'it fornisce architetture di riferimento per gli agenti di sicurezza che monitorano e raccolgono i dati dai dispositivi.
Per altre informazioni, vedere [l'articolo relativo all'architettura di riferimento degli agenti di sicurezza](security-agent-architecture.md).

Gli agenti vengono sviluppati come progetti open source e sono disponibili in due versioni: <br> [C](https://aka.ms/iot-security-github-c)e [C#](https://aka.ms/iot-security-github-cs).

In questo articolo viene spiegato come: 
> [!div class="checklist"]
> * Confrontare le versioni degli agenti di sicurezza
> * Individuare le piattaforme agente supportate
> * Scegliere la versione corretta dell'agente per la soluzione

## <a name="understand-security-agent-options"></a>Informazioni sulle opzioni dell'agente di sicurezza

Ogni Centro sicurezza di Azure per la versione dell'agente sicurezza di Internet offre lo stesso set di funzionalità e supporta opzioni di configurazione analoghe. 

L'agente di sicurezza basato su C ha un footprint di memoria inferiore ed è la scelta ideale per i dispositivi con meno risorse disponibili. 

|     | Agente sicurezza basato su C | C#agente di sicurezza basato su |
| --- | ----------- | --------- |
| Open Source | Disponibile con [licenza mit](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-cs) | Disponibile con [licenza mit](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-c) |
| Linguaggio di sviluppo    | C | C# |
| Piattaforme Windows supportate? | No | Sì |
| Prerequisiti di Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Piattaforme Linux supportate? | Sì, x64 e x86 | Sì, solo x64 |
| Prerequisiti per Linux | libunwind8, libcurl3, UUID-Runtime, auditd, audispd-plugins | libunwind8, libcurl3, UUID-Runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Impronta disco | 10,5 MB | 90 MB |
| Footprint di memoria (in media) | 5,5 MB | 33 MB |
| [Autenticazione](concept-security-agent-authentication-methods.md) nell'hub Internet | Sì | Sì |
| [Raccolta](how-to-agent-configuration.md#supported-security-events) dati di sicurezza | Sì | Yes |
| Aggregazione di eventi | Sì | Yes |
| Configurazione remota tramite il [modulo di sicurezza gemello](concept-security-module.md) | Sì | Sì |
|

## <a name="security-agent-installation-guidelines"></a>Linee guida per l'installazione degli agenti di sicurezza

Per **Windows**: È necessario eseguire lo script Install SecurityAgent. ps1 da una finestra di PowerShell Administrator. 

Per **Linux**: InstallSecurityAgent.sh deve essere eseguito come superuser. È consigliabile predisporre il comando di installazione con "sudo".


## <a name="choose-an-agent-flavor"></a>Scegliere una versione dell'agente 

Rispondere alle domande seguenti sui dispositivi Internet per selezionare l'agente corretto:

- Si sta usando _Windows Server_ o _Windows Internet Core_? 

    [Distribuire un C#agente di sicurezza basato su per Windows](how-to-deploy-windows-cs.md).

- Si usa una distribuzione Linux con l'architettura x86? 

    [Distribuire un agente di sicurezza basato su C per Linux](how-to-deploy-linux-c.md).

- Si usa una distribuzione Linux con l'architettura x64?

    È possibile usare una delle due versioni di Agent. <br>
    [Distribuire un agente di sicurezza basato su C per Linux](how-to-deploy-linux-c.md) e/o [distribuire C#un agente di sicurezza basato su per Linux](how-to-deploy-linux-cs.md).

Entrambe le versioni degli agenti offrono lo stesso set di funzionalità e supportano opzioni di configurazione simili.
Per altre informazioni, vedere [confronto degli agenti di sicurezza](how-to-deploy-agent.md#understand-security-agent-options) .

## <a name="supported-platforms"></a>Piattaforme supportate

Nell'elenco seguente sono incluse tutte le piattaforme attualmente supportate.

|Centro sicurezza di Azure per l'agente Internet |Sistema operativo |Architettura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core compilazione 17763 |x64|
|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni di configurazione, passare alla Guida alle procedure per la configurazione degli agenti. 
> [!div class="nextstepaction"]
> [Guida alla configurazione dell'agente](./how-to-agent-configuration.md)
