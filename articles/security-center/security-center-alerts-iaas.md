---
title: Rilevamento delle minacce per VM e server nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta gli avvisi relativi alla macchina virtuale e al server disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: b101fd30ddbdbf0f8fdf6e02394cb10b9af5f4b0
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666364"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Rilevamento delle minacce per VM e server nel centro sicurezza di Azure

In questo argomento vengono illustrati i diversi tipi di metodi di rilevamento e gli avvisi disponibili per le macchine virtuali e i server con i sistemi operativi seguenti. Per un elenco delle versioni supportate, vedere [piattaforme e funzionalità supportate dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Il Centro sicurezza di Azure si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows. Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione di tutti questi servizi in un formato di facile utilizzo.

* **Microsoft Defender ATP** <a name="windows-atp"></a> : il Centro sicurezza estende le proprie piattaforme di protezione del carico di lavoro cloud grazie all'integrazione con Microsoft Defender Advanced Threat Protection (ATP). In questo modo vengono fornite le funzionalità complete di rilevamento e risposta dell'endpoint.

    > [!NOTE]
    > Il sensore Microsoft Defender ATP viene abilitato automaticamente nei server Windows che usano il Centro sicurezza.

    Quando Microsoft Defender ATP rileva una minaccia, viene attivato un avviso. L'avviso viene visualizzato nel dashboard del Centro sicurezza. Dal dashboard è possibile passare alla console di Microsoft Defender ATP ed eseguire un'indagine dettagliata per individuare l'ambito dell'attacco. Per ulteriori informazioni su Microsoft Defender ATP, vedere [onboarding servers to the Microsoft Defender ATP Service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Analisi** <a name="windows-dump"></a> dei dump di arresto anomalo del sistema: in caso di arresto anomalo del software, un dump di arresto anomalo acquisisce una parte della memoria al momento dell'arresto

    Un arresto anomalo potrebbe essere stato causato da malware o contenere malware. Per evitare di essere rilevati dai prodotti per la sicurezza, diverse forme di malware utilizzano un attacco senza file, che evita la scrittura su disco o la crittografia dei componenti software scritti su disco. Questo tipo di attacco è difficile da rilevare usando approcci tradizionali basati su disco.

    Tuttavia, tramite l'analisi della memoria, è possibile rilevare questo tipo di attacco. Analizzando la memoria nel dump di arresto anomalo del sistema, il Centro sicurezza può rilevare le tecniche usate dagli attacchi. Ad esempio, l'attacco potrebbe tentare di sfruttare le vulnerabilità del software, accedere ai dati riservati e mantenersi nascosto in un computer compromesso. Il Centro sicurezza funziona con un effetto minimo sulle prestazioni degli host.

    Per un elenco degli avvisi di analisi dei dump di arresto anomalo del sistema, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-crashdump).

* **Rilevamento** <a name="windows-fileless"></a> di attacchi non file: gli attacchi di file che hanno come destinazione gli endpoint sono comuni. Per evitare il rilevamento, gli attacchi con file inseriscono payload dannosi nella memoria. I payload degli utenti malintenzionati vengono mantenuti nella memoria dei processi compromessi ed eseguono un'ampia gamma di attività dannose.

    Con il rilevamento di attacchi senza file, le tecniche di analisi della memoria automatizzata identificano i toolkit, le tecniche e i comportamenti di attacco senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae informazioni dettagliate direttamente dalla memoria dei processi critici per la sicurezza.

    Rileva l'evidenza di exploit, inserimento di codice ed esecuzione di payload dannosi. Il rilevamento di attacchi non file genera avvisi di sicurezza dettagliati per accelerare la valutazione degli avvisi, la correlazione e il tempo di risposta downstream. Questo approccio integra le soluzioni EDR basate su eventi, garantendo una maggiore copertura del rilevamento.

    Per un elenco degli avvisi di rilevamento degli attacchi senza file, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-filelessattackdetect).

> [!NOTE]
> È possibile simulare gli avvisi di Windows scaricando il [PlayBook del Centro sicurezza di Azure: avvisi di sicurezza](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

## Linux <a name="linux-machines"></a>

Il Centro sicurezza raccoglie i record di controllo dai computer Linux usando **auditd**, uno dei framework di controllo di Linux più comuni. il controllo risiede nel kernel principale. 

* <a name="linux-auditd"></a> **Avvisi controllati da Linux e integrazione con Microsoft Monitoring Agent (MMA)** : il sistema controllato è costituito da un sottosistema a livello di kernel, responsabile del monitoraggio delle chiamate di sistema. I filtri vengono filtrati in base a un set di regole specificato e i messaggi vengono scritti in un socket. Il Centro sicurezza integra le funzionalità del pacchetto controllato all'interno del Microsoft Monitoring Agent (MMA). Questa integrazione consente la raccolta di eventi controllati in tutte le distribuzioni di Linux supportate, senza prerequisiti.  

    i record controllati vengono raccolti, arricchiti e aggregati in eventi usando l'agente MMA di Linux. Il Centro sicurezza aggiunge continuamente nuove analisi che usano i segnali di Linux per rilevare comportamenti dannosi nei computer Linux locali e nel cloud. Analogamente alle funzionalità di Windows, queste analisi si estendono su processi sospetti, tentativi di accesso ambigui, caricamento del modulo kernel e altre attività. Queste attività possono indicare che un computer è sotto attacco o è stato violato.  

    Di seguito sono riportati alcuni esempi di analisi che si estendono su diverse fasi del ciclo di vita degli attacchi.

    Per un elenco degli avvisi di Linux, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-linux).

> [!NOTE]
> È possibile simulare gli avvisi Linux scaricando il [PlayBook del Centro sicurezza di Azure: rilevamento di Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Passaggi successivi

Per esempi e altre informazioni sul rilevamento del Centro sicurezza, vedere:

* [Come il Centro sicurezza di Azure automatizza il rilevamento di attacchi informatici](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Come il Centro sicurezza di Azure rileva le vulnerabilità usando gli strumenti di amministrazione](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Usare il Centro sicurezza di Azure per rilevare gli attacchi con computer Linux compromessi](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Il Centro sicurezza di Azure può rilevare le vulnerabilità emergenti in Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)