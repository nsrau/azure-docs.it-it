---
title: Tempo di inserimento dei dati in Azure Log Analytics | Microsoft Docs
description: Illustra i diversi fattori che influiscono sulla latenza nella raccolta dei dati in Azure Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2018
ms.author: bwren
ms.openlocfilehash: f40c8ed7eb6bfae958b3b57c4b7d525963ab9741
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955255"
---
# <a name="data-ingestion-time-in-log-analytics"></a>Tempo di inserimento dei dati in Log Analytics
Azure Log Analytics è un servizio dati su vasta scala in Monitoraggio di Azure che serve migliaia di clienti che ogni mese inviano terabyte di dati a un ritmo crescente. Spesso sono state poste domande sul tempo necessario affinché i dati diventino disponibili in Log Analytics dopo la raccolta. Questo articolo illustra i diversi fattori che influiscono su questa latenza.

## <a name="typical-latency"></a>Latenza tipica
La latenza si riferisce al tempo in cui i dati vengono creati nel sistema monitorato e al tempo necessario affinché diventino disponibili per l'analisi in Log Analytics. La latenza tipica per inserire dati in Log Analytics va da 2 a 5 minuti. La latenza specifica per dati particolari varia in base a una serie di fattori illustrati di seguito.


## <a name="factors-affecting-latency"></a>Fattori che influiscono sulla latenza
Il tempo totale di inserimento per un determinato set di dati può essere suddiviso nelle seguenti aree di alto livello. 

- Tempo dell'agente: il tempo necessario per individuare un evento, raccoglierlo e inviarlo al punto di inserimento di Log Analytics come record di log. Nella maggior parte dei casi, questo processo viene gestito da un agente.
- Tempo della pipeline: il tempo necessario alla pipeline di inserimento per elaborare il record di log. Include l'analisi delle proprietà dell'evento e l'aggiunta potenziale di informazioni calcolate.
- Tempo di indicizzazione: il tempo impiegato per inserire un record di log nell'archivio per Big Data di Log Analytics.

Di seguito sono riportati i dettagli sui diversi tipi di latenza introdotti in questo processo.

### <a name="agent-collection-latency"></a>Latenza di raccolta degli agenti
Gli agenti e le soluzioni di gestione usano diverse strategie per raccogliere i dati da una macchina virtuale, che possono influire sulla latenza. Ecco alcuni esempi specifici:

- Gli eventi Windows, gli eventi SysLog e le metriche delle prestazioni vengono raccolti immediatamente. Il polling dei contatori delle prestazioni Linux viene eseguito a intervalli di 30 secondi.
- I log di IIS e i log personalizzati vengono raccolti quando viene modificato il relativo timestamp. Per i log di IIS, questa frequenza dipende anche dalla [pianificazione di rollover configurata in IIS](log-analytics-data-sources-iis-logs.md). 
- La soluzione Replica Active Directory esegue la valutazione ogni cinque giorni, mentre la soluzione Valutazione Active Directory esegue una valutazione settimanale dell'infrastruttura Active Directory. L'agente raccoglierà questi log solo al termine della valutazione.

### <a name="agent-upload-frequency"></a>Frequenza di caricamento dell'agente
Per assicurarsi che l'agente di Log Analytics sia leggero, l'agente memorizza nel buffer i log e li carica periodicamente in Log Analytics. La frequenza di caricamento varia tra 30 secondi e 2 minuti a seconda del tipo di dati. La maggior parte dei dati viene caricata in meno di 1 minuto. Le condizioni della rete possono influire negativamente sulla latenza di questi dati per raggiungere il punto di inserimento di Log Analytics.

### <a name="azure-logs-and-metrics"></a>Log e metriche di Azure 
I dati dei log attività impiegano circa 5 minuti per diventare disponibili in Log Analytics. I dati dei log e delle metriche di diagnostica possono impiegare da 1 a 5 minuti per diventare disponibili in base al servizio di Azure. Per inviare i dati al punto di inserimento di Log Analytics saranno necessari ulteriori 30-60 secondi per i log e 3 minuti per le metriche.

### <a name="management-solutions-collection"></a>Raccolta delle soluzioni di gestione
Alcune soluzioni non raccolgono i dati da un agente e possono usare un metodo di raccolta che introduce una latenza aggiuntiva. Alcune soluzioni raccolgono dati a intervalli regolari senza tentare di eseguire la raccolta quasi in tempo reale. Ecco alcuni esempi specifici:

- La soluzione Office 365 esegue il polling dei log attività usando l'API Office 365 Management Activity, che attualmente non fornisce garanzie di latenza quasi in tempo reale.
- I dati delle soluzioni di Windows Analytics, ad esempio Conformità aggiornamenti, vengono raccolti dalla soluzione con frequenza giornaliera.

Per determinare la frequenza di raccolta specifica, consultare la documentazione relativa a ciascuna soluzione.

### <a name="pipeline-process-time"></a>Tempo di elaborazione della pipeline
Dopo l'inserimento nella pipeline di Log Analytics, i record di log vengono scritti nella risorsa di archiviazione temporanea per garantire l'isolamento del tenant e assicurarsi che i dati non vengano persi. Questo processo richiede in genere altri 5-15 secondi. Alcune soluzioni di gestione implementano algoritmi più pesanti per aggregare i dati e derivare informazioni dettagliate mentre i dati sono in streaming. Ad esempio, Monitoraggio prestazioni rete aggrega i dati in ingresso a intervalli di 3 minuti, aggiungendo di fatto una latenza di 3 minuti. Un altro processo che aggiunge latenza è il processo che gestisce i log personalizzati. In alcuni casi, questo processo potrebbe aggiungere alcuni minuti di latenza ai log che vengono raccolti dai file dall'agente.

### <a name="new-custom-data-types-provisioning"></a>Provisioning di nuovi tipi di dati personalizzati
Quando viene creato un nuovo tipo di dati personalizzati da un [log personalizzato](../log-analytics/log-analytics-data-sources-custom-logs.md) o dall'[API dell'agente di raccolta dati](../log-analytics/log-analytics-data-collector-api.md), il sistema crea un contenitore di archiviazione dedicato. Questo sovraccarico è occasionale poiché si verifica solo alla prima occorrenza di questo tipo di dati.

### <a name="surge-protection"></a>Protezione in caso di picchi di dati
La principale priorità di Log Analytics è quella di garantire che nessun dato del cliente venga perso e pertanto il sistema dispone di una protezione predefinita in caso di picchi di dati. La protezione include buffer per assicurare che il sistema continui a funzionare anche in situazioni di carico elevato. In condizioni di carico normale, questi controlli aggiungono meno di un minuto ma, in caso di errori e condizioni eccezionali, possono aggiungere un tempo maggiore assicurando tuttavia la sicurezza dei dati.

### <a name="indexing-time"></a>Tempo di indicizzazione
Ogni piattaforma per Big Data prevede un equilibrio predefinito tra fornire funzionalità di analisi e di ricerca avanzata e fornire l'accesso immediato ai dati. Azure Log Analytics consente di eseguire query avanzate su miliardi di record e ottenere risultati in pochi secondi. Questo risultato è possibile in quanto l'infrastruttura trasforma i dati in modo significativo durante l'inserimento e li memorizza in strutture compatte uniche. Il sistema memorizza i dati finché non è disponibile una quantità sufficiente per creare queste strutture. Questa operazione deve essere completata prima che il record di log venga visualizzato nei risultati della ricerca.

Attualmente questo processo richiede circa 5 minuti in caso di un volume ridotto di dati, ma un tempo inferiore a velocità di trasferimento dati più elevate. Questo processo sembra illogico, ma consente l'ottimizzazione della latenza per carichi di lavoro in produzione con volumi elevati.



## <a name="checking-ingestion-time"></a>Controllo del tempo di inserimento dei dati
È possibile usare la tabella **Heartbeat** per ottenere una stima della latenza per i dati dagli agenti. Poiché l'heartbeat viene inviato una volta al minuto, la differenza tra il tempo corrente e l'ultimo record di heartbeat sarà in teoria il più vicino possibile al minuto.

Usare la query seguente per elencare i computer con la latenza più alta.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | top 50 by IngestionTime asc

 
Usare la query seguente in ambienti di grandi dimensioni per riepilogare la latenza per diverse percentuali dei computer totali.

    Heartbeat 
    | summarize IngestionTime = now() - max(TimeGenerated) by Computer 
    | summarize percentiles(IngestionTime, 50,95,99)



## <a name="next-steps"></a>Passaggi successivi
* Leggere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) per Log Analytics.

