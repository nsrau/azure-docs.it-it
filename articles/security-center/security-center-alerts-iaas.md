---
title: Rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta gli avvisi relativi alla macchina virtuale e al server disponibili nel centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: f23865fc0a1943a5157e4ff8eb8de10a71ef0883
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295782"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Rilevamento delle minacce per le macchine virtuali & Server nel centro sicurezza di Azure

In questo argomento vengono illustrati i diversi tipi di metodi di rilevamento e gli avvisi disponibili per le macchine virtuali e i server con i sistemi operativi seguenti. Per un elenco delle versioni supportate, vedere [piattaforme e funzionalità supportate dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Il Centro sicurezza si integra con i servizi di Azure per monitorare e proteggere i computer basati su Windows.  Il Centro sicurezza presenta gli avvisi e i suggerimenti per la correzione di tutti questi servizi in un formato di facile utilizzo.

### Microsoft Server Defender ATP<a nanme="windows-atp"></a>

Il Centro sicurezza di Azure estende le piattaforme di protezione dei carichi di lavoro cloud integrando con Windows Defender Advanced Threat Protection (ATP). In questo modo vengono fornite le funzionalità complete di rilevamento e risposta dell'endpoint.

> [!NOTE]
> Il sensore Windows Server Defender ATP viene abilitato automaticamente nei server Windows caricati nel centro sicurezza di Azure.

Quando Windows Server Defender ATP rileva una minaccia, viene attivato un avviso. L'avviso viene visualizzato nel dashboard del Centro sicurezza. Dal dashboard è possibile passare alla console di Windows Defender ATP per eseguire un'indagine dettagliata per individuare l'ambito dell'attacco. Per ulteriori informazioni su Windows Server Defender ATP, vedere [onboarding servers to the Windows Defender ATP Service](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analisi del dump di arresto anomalo<a nanme="windows-dump"></a>

Quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una porzione della memoria al momento dell'arresto.

Un arresto anomalo può essere causato da malware o contenere malware. Per evitare di essere rilevati dai prodotti per la sicurezza, diverse forme di malware utilizzano un attacco senza file, che evita la scrittura su disco o la crittografia dei componenti software scritti su disco. Questo tipo di attacco è difficile da rilevare usando approcci tradizionali basati su disco.

Tuttavia, questo tipo di attacco può essere rilevato tramite l'analisi della memoria. Analizzando la memoria nel dump di arresto anomalo del sistema, il Centro sicurezza è in grado di rilevare le tecniche usate dall'attacco per sfruttare le vulnerabilità del software, accedere ai dati riservati e mantenersi nascosto in un computer compromesso. Questa operazione viene eseguita dal centro sicurezza back-end con un effetto minimo sulle prestazioni degli host.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Inserimento di codice individuato**|Un attacco di code injection consiste nell'inserimento di moduli eseguibili in processi o thread in esecuzione. Questa tecnica viene usata da malware per accedere ai dati, mentre si nasconde correttamente per impedire che venga individuata e rimossa. <br/>Questo avviso indica che nel dump di arresto anomalo del sistema è presente un modulo inserito. Per distinguere i moduli inseriti dannosi e non dannosi, il Centro sicurezza controlla se il modulo inserito è conforme a un profilo di comportamento sospetto.|
|**Rilevato segmento di codice sospetto**|Indica che un segmento di codice è stato allocato utilizzando metodi non standard, ad esempio l'inserimento riflettente e il svuotamento del processo. L'avviso fornisce caratteristiche aggiuntive del segmento di codice che sono state elaborate per fornire il contesto per le funzionalità e i comportamenti del segmento di codice restituito.|
|**Shellcode individuato**|Uno shellcode è il payload che viene eseguito dopo che il malware ha sfruttato una vulnerabilità del software.<br/>Questo avviso indica che l'analisi dei dump di arresto anomalo del sistema ha rilevato codice eseguibile che presenta un comportamento comunemente eseguito da payload dannosi. Anche se il software non dannoso può eseguire questo comportamento, non è tipico delle normali procedure di sviluppo del software.|

### Rilevamento di attacchi non file<a nanme="windows-fileless"></a>

In Azure, si verificano periodicamente attacchi non di file destinati agli endpoint dei clienti.

Per evitare il rilevamento, gli attacchi con file inseriscono payload dannosi nella memoria. I payload degli utenti malintenzionati vengono mantenuti nella memoria dei processi compromessi ed eseguono un'ampia gamma di attività dannose.

Con il rilevamento di attacchi senza file, le tecniche di analisi della memoria automatizzata identificano i toolkit, le tecniche e i comportamenti di attacco senza file. Questa soluzione analizza periodicamente il computer in fase di esecuzione ed estrae informazioni dettagliate direttamente dalla memoria dei processi critici per la sicurezza.

Rileva l'evidenza di exploit, inserimento di codice ed esecuzione di payload dannosi. Il rilevamento di attacchi non file genera avvisi di sicurezza dettagliati per accelerare la valutazione degli avvisi, la correlazione e il tempo di risposta downstream. Questo approccio integra le soluzioni EDR basate su eventi, garantendo una maggiore copertura del rilevamento.

> [!NOTE]
> È possibile simulare gli avvisi di Windows scaricando il [PlayBook del Centro sicurezza di Azure](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Avvisi di sicurezza e seguire le linee guida fornite

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**È stata rilevata una tecnica di attacco non file**|La memoria del processo specificato di seguito contiene un toolkit di attacco informatico: Meterpreter. I toolkit di attacco senza file in genere non hanno una presenza sul file system, rendendo difficile il rilevamento da parte di un antivirus tradizionale.|

### <a name="further-reading"></a>Altre informazioni

Per esempi e altre informazioni sul rilevamento del Centro sicurezza:

* [Come il Centro sicurezza di Azure automatizza il rilevamento di attacchi informatici](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Come il Centro sicurezza di Azure rileva le vulnerabilità usando gli strumenti di amministrazione](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Il Centro sicurezza raccoglie i record di controllo dai computer Linux usando auditd, uno dei framework di controllo di Linux più comuni. il controllo ha il vantaggio di essere stato da molto tempo e vivere nel kernel principale. 

### Integrazione di Microsoft Monitoring Agent e avvisi controllati da Linux (MMA)<a name="linux-auditd"></a>

Il sistema controllato è costituito da un sottosistema a livello di kernel, che è responsabile del monitoraggio delle chiamate di sistema, del filtro in base a un determinato set di regole e della scrittura dei messaggi in un socket. Il Centro sicurezza integra le funzionalità del pacchetto controllato all'interno del Microsoft Monitoring Agent (MMA). Questa integrazione consente la raccolta di eventi controllati in tutte le distribuzioni di Linux supportate senza prerequisiti.  

i record controllati vengono raccolti, arricchiti e aggregati in eventi usando l'agente MMA di Linux. Il Centro sicurezza sta lavorando costantemente sull'aggiunta di nuove analisi, che sfruttano i segnali di Linux per rilevare comportamenti dannosi nei computer Linux locali e cloud. Analogamente alle funzionalità di Windows, queste analisi si estendono nei processi sospetti, i tentativi di accesso ambigui, il caricamento del modulo kernel e altre attività, che indicano che un computer è sotto attacco o è stato violato.  

Di seguito sono riportati alcuni esempi di analisi, che dimostrano il modo in cui si estendono diverse fasi del ciclo di vita degli attacchi.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Il processo ha visto l'accesso al file delle chiavi autorizzate SSH in modo insolito**|È stato eseguito l'accesso a un file di chiavi autorizzate SSH in un metodo simile a una campagna malware nota. Questo accesso potrebbe indicare che un utente malintenzionato sta tentando di ottenere l'accesso permanente a un computer|
|**Tentativo di persistenza rilevato**|L'analisi dei dati host ha rilevato che è stato installato uno script di avvio per la modalità utente singolo. <br/>Poiché è raro che sia necessario eseguire qualsiasi processo legittimo in tale modalità, questo può indicare che un utente malintenzionato ha aggiunto un processo dannoso a ogni livello di esecuzione per garantire la persistenza.|
|**Manipolazione delle attività pianificate rilevate**|L'analisi dei dati host ha rilevato la possibile modifica delle attività pianificate. Spesso gli utenti malintenzionati aggiungono attività pianificate ai computer compromessi per ottenere la persistenza.|
|**Modifica del timestamp del file sospetto**|L'analisi dei dati host ha rilevato una modifica del timestamp sospetta. Spesso gli utenti malintenzionati copiano i timestamp da file legittimi esistenti a nuovi strumenti per evitare il rilevamento di questi file appena eliminati|
|**Un nuovo utente è stato aggiunto al gruppo sudoers**|L'analisi dei dati host ha rilevato che un utente è stato aggiunto al gruppo sudoers, che consente ai membri di eseguire comandi con privilegi elevati.|
|**Exploit probabile della vulnerabilità DynoRoot nel client DHCP**|L'analisi dei dati host ha rilevato l'esecuzione di un comando insolito con processo padre dello script dhclient.|
|**Rilevato modulo kernel sospetto**|L'analisi dei dati host ha rilevato un file oggetto condiviso caricato come un modulo kernel. Questa operazione potrebbe essere legittima o indicare che uno dei computer è stato compromesso.|
|**Processo associato al data mining di valuta digitale rilevato**|L'analisi dei dati dell'host ha rilevato l'esecuzione di un processo normalmente associato a Digital Currency mining|
|**Potenziale portabilità dell'invio a un indirizzo IP esterno**|L'analisi dei dati dell'host ha rilevato l'avvio del Port inoltring a un indirizzo IP esterno.|

> [!NOTE]
> È possibile simulare gli avvisi di Windows [scaricando il PlayBook del Centro sicurezza di Azure: Avvisi](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) di sicurezza e seguire le linee guida fornite.


Per altre informazioni, vedere questi articoli:  

* [Usare il Centro sicurezza di Azure per rilevare gli attacchi con computer Linux compromessi](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Il Centro sicurezza di Azure può rilevare le vulnerabilità emergenti in Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 