---
title: Per le minacce rilevamento per le macchine virtuali e i server nel Centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta avvisi della macchina virtuale e server disponibili nel Centro sicurezza di Azure.
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
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571582"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Rilevamento delle minacce per macchine virtuali e i server nel Centro sicurezza di Azure

In questo argomento presenta i diversi tipi di metodi di rilevamento e gli avvisi disponibili per le macchine virtuali e i server con i sistemi operativi seguenti. Per un elenco delle versioni supportate, vedere [funzionalità supportate dal Centro sicurezza di Azure e piattaforme](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Il Centro sicurezza si integra con servizi di Azure per monitorare e proteggere i computer basati su Windows.  Il Centro sicurezza Visualizza gli avvisi e i suggerimenti di correzione da tutti questi servizi in un formato di facile utilizzo.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Centro sicurezza di Azure estende le piattaforme di protezione del carico di lavoro Cloud grazie all'integrazione con Windows Defender Advanced Threat Protection (ATP). Ciò offre funzionalità complete di Endpoint di rilevamento e risposta (EDR).

> [!NOTE]
> Sensore di Server di Windows Defender ATP viene abilitato automaticamente in Windows Server che siano state caricate al Centro sicurezza di Azure.

Quando il Server di Windows Defender ATP rileva una minaccia, attiva un avviso. L'avviso viene visualizzato nel dashboard del Centro sicurezza. Dal dashboard, è possibile trasformare tramite pivot nella console di Windows Defender ATP per eseguire un'analisi approfondita per scoprire l'ambito dell'attacco. Per altre informazioni sui Server di Windows Defender ATP, vedere [server eseguire l'Onboarding nel servizio di Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Analisi di Dump di arresto anomalo del sistema <a nanme="windows-dump"></a>

Quando il software si arresta in modo anomalo, un dump di arresto anomalo acquisisce una porzione della memoria al momento dell'arresto.

Un arresto anomalo del sistema potrebbe essere stato causato da malware o contengono malware. Per evitare di essere rilevate dai prodotti di sicurezza, varie forme di malware utilizzano un attacco fileless, che consente di evitare la scrittura su disco o la crittografia dei componenti software scritti sul disco. Questo tipo di attacco è difficile da rilevare con gli approcci tradizionali basate su disco.

Tuttavia, questo tipo di attacco può essere rilevato tramite l'analisi della memoria. Analizzando la memoria nel dump di arresto anomalo del sistema, il Centro sicurezza può rilevare le tecniche di che attacco Usa per sfruttare le vulnerabilità del software, accedere ai dati riservati e rimanere permanentemente all'interno di un computer. In tal caso, il back-end di Centro sicurezza con impatto minimo sulle prestazioni per gli host.

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Attacco di code injection individuato**|Un attacco di code injection consiste nell'inserimento di moduli eseguibili in processi o thread in esecuzione. Questa tecnica viene usata dal malware per accedere ai dati, mentre correttamente se si nasconde in modo da evitare che viene trovato e rimosso. <br/>Questo avviso indica che nel dump di arresto anomalo del sistema è presente un modulo inserito. Per distinguere tra moduli inseriti dannosi e legittimi, il Centro sicurezza verifica se il modulo inserito corrisponde a un profilo di comportamento sospetto.|
|**Segmento di codice sospetto individuato**|Indica che un segmento di codice è stato allocato con metodi non standard, ad esempio reflective injection e hollowing del processo. L'avviso fornisce le caratteristiche aggiuntive del segmento di codice che sono state elaborate per fornire un contesto per le funzionalità e i comportamenti del segmento di codice segnalato.|
|**Individuato attacco ShellCode**|Uno shellcode è il payload che viene eseguito dopo che il malware ha sfruttato una vulnerabilità del software.<br/>Questo avviso indica che l'analisi di dump di arresto anomalo del sistema ha rilevato codice eseguibile che presenta un comportamento comunemente adottato dai payload dannosi. Sebbene non malware può anche eseguire questo comportamento, non è tipico di procedure consigliate per lo sviluppo di software normale.|

### Rilevamento di attacchi fileless <a nanme="windows-fileless"></a>

In Azure, noteremo regolarmente fileless attacchi gli endpoint dei clienti.

Per evitare il rilevamento, attacchi fileless inserire payload dannosi in memoria. I payload di autore dell'attacco persistono all'interno della memoria dei processi compromessi ed eseguono un'ampia gamma di attività dannose.

Con il rilevamento di attacchi fileless, tecniche di analisi forense memoria automatizzati identificano i comportamenti, tecniche e attacchi fileless Toolkit. Questa soluzione periodicamente analizza la macchina in fase di esecuzione ed estrae insights direttamente dalla memoria dei processi critici per la sicurezza.

Rileva la prova di exploit, inserimento di codice ed esecuzione del payload dannosi. Rilevamento di attacchi fileless genera avvisi di sicurezza dettagliate per accelerare la valutazione degli avvisi, la correlazione e il tempo di risposta a valle. Questo approccio si integra con soluzioni EDR basato su eventi che fornisce maggiore copertura del rilevamento.

> [!NOTE]
> È possibile simulare gli avvisi di Windows tramite download [Azure Security Center Playbook](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Avvisi di sicurezza e seguire le linee guida fornite

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Tecnica di attacco fileless rilevato**|La memoria del processo specificato di seguito contiene un toolkit di attacco fileless: Meterpreter. Toolkit di attacco fileless in genere non è una presenza nel file system, rendendo difficile il rilevamento antivirus tradizionali.|

### <a name="further-reading"></a>Altre informazioni

Per esempi e altre informazioni sul rilevamento del Centro sicurezza:

* [Il Centro sicurezza di Azure consente di automatizzare il rilevamento di attacchi informatici](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Il Centro sicurezza di Azure rileva vulnerabilità usando strumenti di amministrazione](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Raccoglie il Centro sicurezza controlla i record da computer Linux usando **auditd**, uno di Linux più comuni Framework di controllo. auditd ha il vantaggio di essere stata intorno per molto tempo e che vivono nel kernel del progetto principale. 

### Integrazione di Microsoft Monitoring Agent (MMA) e gli avvisi di Linux auditd <a name="linux-auditd"></a>

Il sistema auditd è costituito da un sottosistema a livello di kernel, che è responsabile per le chiamate di sistema di monitoraggio, filtrarli da un determinato set di regole e scrivendo messaggi per loro un socket. Il Centro sicurezza si integra le funzionalità dal pacchetto auditd all'interno di Microsoft Monitoring Agent (MMA). Questa integrazione consente la raccolta di eventi auditd in tutte le distribuzioni di Linux supportate senza tutti i prerequisiti.  

i record di controllo vengono raccolti, arricchiti e aggregati in eventi tramite l'agente MMA Linux. Il Centro sicurezza è rivolto sull'aggiunta di nuovi analitica, che sfrutta Linux segnali per rilevare comportamenti dannosi nei cloud locali e nel computer Linux. Analogamente alle funzionalità di Windows, questi analitica estendersi su processi sospetti, tentativi di accesso sospette, caricamento moduli kernel e altre attività, che indicano un computer è sotto attacco oppure non è stata soddisfatta.  

Ecco alcuni esempi di analitica, che illustrano come si estendono su diverse fasi del ciclo di vita di attacco.

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Processo visualizzata l'accesso al file di chiavi autorizzate SSH in modo anomalo**|Un file di chiavi autorizzate SSH è stato eseguito l'accesso in un metodo simile alle campagne malware conosciuto. Questo tipo di accesso potrebbe indicare che un utente malintenzionato sta tentando di ottenere l'accesso permanente a un computer|
|**Tentativo di salvataggio permanente rilevato**|Analisi di dati host ha rilevato che uno script di avvio per la modalità utente singolo sia stato installato. <br/>Poiché è raro che tutti i processi legittimi sarebbe richiesto per eseguire in tale modalità, questo può indicare che un utente malintenzionato ha aggiunto un processo dannoso a ogni livello di esecuzione per garantire la persistenza.|
|**Manipolazione di attività pianificate rilevato**|Analisi dei dati host ha rilevato possibili manipolazione delle attività pianificate. Gli utenti malintenzionati aggiungono spesso le attività pianificate per le macchine che hai compromesse per ottenere la persistenza.|
|**Modifica del timestamp file sospetto**|Analisi dei dati host ha rilevato una modifica del timestamp sospette. Gli utenti malintenzionati spesso copiare timestamp dai file legittimi esistenti ai nuovi strumenti per evitare il rilevamento di questi file appena rilasciati|
|**È stato aggiunto un nuovo utente al gruppo sudoers**|Analisi dei dati host ha rilevato che un utente è stato aggiunto al gruppo sudoers, che consente ai membri di eseguire i comandi con privilegi elevati.|
|**Probabilmente exploit di vulnerabilità DynoRoot nel client dhcp**|Analisi dei dati host rilevati l'esecuzione di un comando insolito con il processo padre di dhclient script.|
|**Rilevato modulo kernel sospetta**|Analisi dei dati di host ha rilevato un file oggetto condiviso viene caricato come modulo del kernel. Potrebbe trattarsi di attività legittimo o un valore che indica che una delle macchine sia stata compromessa.|
|**Processo associato al data mining valuta digitale rilevato**|Analisi dei dati host rilevati l'esecuzione di un processo in genere associato a data mining valuta digitale|
|**Potenziali il port forwarding per indirizzo IP esterno**|Analisi dei dati host ha rilevato l'apertura di porte di inoltro a un indirizzo IP esterno.|

> [!NOTE]
> È possibile simulare gli avvisi di Windows scaricando [Azure Security Center Playbook: Avvisi di sicurezza](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) e seguire le linee guida specificate.


Per altre informazioni, vedere questi articoli:  

* [Centro sicurezza di Azure usare per rilevare l'attacco dei computer Linux compromesso](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Centro sicurezza di Azure consente di rilevare le vulnerabilità emergenti in Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 