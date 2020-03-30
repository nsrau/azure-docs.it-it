---
title: Selezionare e distribuire il Centro sicurezza di Azure per l'agente IoT Documenti Microsoft
description: Informazioni su come selezionare e distribuire gli agenti di sicurezza di Azure Security Center per IoT nei dispositivi IoT.Learn about how select and deploy Azure Security Center for IoT security agents on IoT devices.
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
ms.openlocfilehash: d70f2f3ec87c8673013bcf7b6f70ebcbb8d06f08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75770017"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selezionare e distribuire un agente di sicurezza sul dispositivo IoT

Il Centro sicurezza di Azure per IoT offre architetture di riferimento per gli agenti di sicurezza che monitorano e raccolgono dati dai dispositivi IoT.Azure Security Center for IoT provides reference architectures for security agents that monitor and collect data from IoT devices.
Per altre informazioni, vedere Architettura di riferimento degli [agenti di sicurezza](security-agent-architecture.md).

Gli agenti sono sviluppati come progetti open source e sono disponibili in due gusti: <br> [In C](https://aka.ms/iot-security-github-c), e [in C.](https://aka.ms/iot-security-github-cs)

In questo articolo vengono illustrate le operazioni seguenti: 
> [!div class="checklist"]
> * Confrontare i sapori degli agenti di sicurezza
> * Individuazione delle piattaforme di agenti supportate
> * Scegli il sapore dell'agente giusto per la tua soluzione

## <a name="understand-security-agent-options"></a>Comprendere le opzioni degli agenti di sicurezza

Ogni configurazione dell'agente di sicurezza IoT di Azure offre lo stesso set di funzionalità e supporta opzioni di configurazione simili. 

L'agente di sicurezza basato su C ha un footprint di memoria inferiore ed è la scelta ideale per i dispositivi con meno risorse disponibili. 

|     | Agente di sicurezza basato su C | Agente di sicurezza basato su C |
| --- | ----------- | --------- |
| Open-source | Disponibile con [licenza MIT](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-cs) | Disponibile con [licenza MIT](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-c) |
| Linguaggio di sviluppo    | C | C# |
| Piattaforme Windows supportate? | No | Sì |
| Prerequisiti di Windows | --- | [Wmi](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| Piattaforme Linux supportate? | Sì, x64 e x86 | Sì, solo x64 |
| Prerequisiti di Linux | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Ingombro del disco | 10,5 MB | 90 MB |
| Footprint di memoria (in media) | 5,5 MB | 33 MB |
| [Autenticazione](concept-security-agent-authentication-methods.md) all'hub IoT | Sì | Sì |
| Raccolta dati [di sicurezza](how-to-agent-configuration.md#supported-security-events) | Sì | Sì |
| Aggregazione di eventi | Sì | Sì |
| Configurazione remota tramite [modulo di sicurezza gemello](concept-security-module.md) | Sì | Sì |
|

## <a name="security-agent-installation-guidelines"></a>Linee guida per l'installazione dell'agente di sicurezza

Per **Windows:** lo script Install SecurityAgent.ps1 deve essere eseguito da una finestra di PowerShell per amministratori. 

Per **Linux**: Il InstallSecurityAgent.sh deve essere eseguito come utente con privilegi avanzati. Si consiglia di anteporre "sudo" al comando di installazione.


## <a name="choose-an-agent-flavor"></a>Scegli un sapore agente 

Rispondere alle domande seguenti sui dispositivi IoT per selezionare l'agente corretto:

- Stai utilizzando _Windows Server_ o Windows _IoT Core_? 

    [Distribuire un agente di sicurezza basato su C, per Windows](how-to-deploy-windows-cs.md).

- Stai usando una distribuzione Linux con architettura x86? 

    [Distribuire un security agent basato su C per Linux.](how-to-deploy-linux-c.md)

- Stai usando una distribuzione Linux con l'architettura x64?

    Entrambi i sapori dell'agente possono essere utilizzati. <br>
    Distribuire un agente di [sicurezza basato su C per Linux](how-to-deploy-linux-c.md) e/o Distribuire un agente di sicurezza [basato su C, per Linux.](how-to-deploy-linux-cs.md)

Entrambe le tà degli agenti offrono lo stesso set di funzionalità e supportano opzioni di configurazione simili.
Per altre informazioni, vedere [Confronto tra agenti di sicurezza.](how-to-deploy-agent.md#understand-security-agent-options)

## <a name="supported-platforms"></a>Piattaforme supportate

L'elenco seguente include tutte le piattaforme attualmente supportate.

|Centro sicurezza di Azure per l'agente IoTAzure Security Center for IoT agent |Sistema operativo |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64, ARMv7|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di configurazione, vedere la guida alle procedure per la configurazione dell'agente. 
> [!div class="nextstepaction"]
> [Configurazione dell'agente come guidare](./how-to-agent-configuration.md)
