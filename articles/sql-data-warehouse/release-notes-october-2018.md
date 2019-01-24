---
title: Note sulla versione di Azure SQL Data Warehouse - Ottobre 2018 | Microsoft Docs
description: Note sulla versione di Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 12/04/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: 9160a5f4e3a452682787ff500199e43e7fad0c77
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213690"
---
# <a name="whats-new-in-azure-sql-data-warehouse-october-2018"></a>Novità di Azure SQL Data Warehouse Ottobre 2018
Azure SQL Data Warehouse riceve continuamente miglioramenti. Questo articolo illustra le nuove funzionalità e le modifiche introdotte nel mese di ottobre 2018.

## <a name="devops-for-data-warehousing"></a>DevOps per Data warehouse
La funzionalità altamente richiesta per SQL Data Warehouse (SQL DW) è ora in anteprima con il supporto per lo strumento SQL Server Data (SSDT) in Visual Studio! I team di sviluppatori possono ora collaborare in una codebase singola e con versione controllata e distribuire rapidamente le modifiche apportate a qualsiasi istanza in tutto il mondo. Interessa partecipare? Questa funzionalità è disponibile oggi come anteprima. È possibile registrarsi visitando [SQL Server Data Tools (SSDT) di SQL Data Warehouse Visual Studio - modulo di iscrizione di anteprima](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4-brmKy3TZOjoktwuHd7S1UODkwQ1lVMEw1NDBGRjNLRDNWOFlQRUpIRi4u). Data l'elevata richiesta, stiamo gestendo l'accettazione in anteprima per garantire la migliore esperienza ai nostri clienti. Una volta effettuata l'iscrizione, il nostro obiettivo è quello di confermare lo stato entro sette giorni lavorativi.

## <a name="row-level-security-generally-available"></a>Sicurezza a livello di riga disponibile a livello generale
Azure SQL Data Warehouse (SQL DW) supporta ora la sicurezza a livello di riga (RLS) con l'aggiunta di una potente funzionalità per proteggere i dati sensibili. Con l'introduzione della sicurezza a livello di riga, è possibile implementare i criteri di sicurezza per controllare l'accesso alle righe nelle tabelle, ad esempio chi può accedere a quali righe. La sicurezza a livello di riga abilita il controllo di accesso con granularità fine senza dover riprogettare il data warehouse. La sicurezza a livello di riga semplifica l'implementazione complessiva della sicurezza dal momento che la logica di restrizione dell'accesso si trova nel livello database e non lontano dai dati in un altro livello applicazione. Inoltre, la sicurezza a livello di riga elimina la necessità di introdurre le visualizzazioni per filtrare le righe per la gestione del controllo di accesso. Non sono previsti costi aggiuntivi per questa funzionalità di sicurezza di livello aziendale per tutti i clienti.

## <a name="advanced-advisors"></a>Advisor avanzati
L'ottimizzazione avanzata per Azure SQL Data Warehouse (SQL DW) è diventata più semplice con ulteriori raccomandazioni e metriche di data warehouse. In Azure Advisor sono disponibili raccomandazioni aggiuntive sulle prestazioni tramite Azure Advisor, tra cui:
1.  Cache adattiva: offre indicazioni su quando eseguire il ridimensionamento per ottimizzare l'utilizzo della cache.
2.  Distribuzione tabelle: determina quando replicare le tabelle per ridurre lo spostamento dei dati e incrementare le prestazioni dei carichi di lavoro. 
3.  Tempdb: offre informazioni su quando eseguire il ridimensionamento e configurare le classe di risorse per ridurre i conflitti di tempdb.

[Monitoraggio di Azure](https://azure.microsoft.com/blog/enhanced-capabilities-to-monitor-manage-and-integrate-sql-data-warehouse-in-the-azure-portal/) offre un'integrazione più approfondita delle metriche dei data warehouse, tra cui un grafico di monitoraggio avanzato personalizzabile per le metriche near real-time nel pannello della panoramica. Non è più necessario uscire dal pannello della panoramica dei data warehouse per accedere alle metriche di Monitoraggio di Azure durante il monitoraggio dell'utilizzo oppure durante la convalida e l'applicazione dei consigli relativi ai data warehouse. Sono inoltre disponibili nuove metriche, come tempdb e l'utilizzo della cache adattiva, complementari rispetto ai consigli sulle prestazioni.

## <a name="advanced-tuning-with-integrated-advisors"></a>Ottimizzazione avanzata con advisor integrati
L'ottimizzazione avanzata per Azure SQL Data Warehouse (SQL DW) è diventata più semplice con metriche e raccomandazioni di data warehouse aggiuntive e una riprogettazione del pannello della panoramica del portale che fornisce un'esperienza integrata con Azure Advisor e Monitoraggio di Azure.

## <a name="accelerated-database-recovery-adr"></a>Ripristino accelerato del database
Azure SQL Data Warehouse con ripristino accelerato del database (ADR) è ora disponibile in anteprima pubblica. Il ripristino accelerato del database è un nuovo motore di SQL Server che migliora notevolmente la disponibilità dei database, specialmente in presenza di transazioni a esecuzione prolungata, grazie alla completa riprogettazione del processo di ripristino corrente dall'inizio alla fine. I vantaggi principali del ripristino accelerato del database sono il recupero rapido e coerente del database e il rollback istantaneo della transazione.

## <a name="azure-monitor-diagnostics-logs"></a>Log di diagnostica di Monitoraggio di Azure
SQL Data Warehouse (SQL DW) offre ora informazioni dettagliate avanzate sui carichi di lavoro analitici tramite l'integrazione diretta con i log di diagnostica di Monitoraggio di Azure. Questa nuova funzionalità permette agli sviluppatori di analizzare il comportamento dei carichi di lavoro per un periodo di tempo esteso e possono prendere decisioni informate sull'ottimizzazione delle query o sulla gestione della capacità. A questo punto è stato introdotto un processo di registrazione esterno attraverso il [log di diagnostica di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json#logs) che fornisce informazioni dettagliate aggiuntive sul carico di lavoro di data warehouse. Con un solo clic, a questo punto si è in grado di configurare i log di diagnostica per la risoluzione dei problemi delle prestazioni delle query cronologiche usando [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). I log di diagnostica di Monitoraggio di Azure supportano periodi di conservazione personalizzabili tramite il salvataggio dei log in un account di archiviazione per finalità di controllo, consentono di eseguire lo streaming di log a hub eventi per ottenere informazioni dettagliate di telemetria near real-time e permettono di analizzare i log usando Azure Log Analytics con query su log. I log di diagnostica sono costituiti da visualizzazioni di telemetria dei data warehouse equivalenti alle viste a gestione dinamica più usate per la risoluzione dei problemi per SQL Data Warehouse. Per questa versione iniziale abbiamo abilitato le visualizzazioni per le viste a gestione dinamica seguenti:

- [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql)
- [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)
- [sys.dm_pdw_dms_workers](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql)
- [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
- [sys.dm_pdw_sql_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql)

## <a name="columnstore-memory-management"></a>Gestione della memoria columnstore
Man mano che il numero dei gruppi di righe columnstore compressi aumenta, aumenta la memoria necessaria per gestire i metadati interni dei segmenti di colonne per tali gruppi.  Di conseguenza, le prestazioni delle query e le query eseguite su alcune viste DMV (Dynamic Management View) columnstore possono peggiorare.  In questa versione sono stati apportati miglioramenti che consentono di ottimizzare le dimensioni dei metadati interni in questi casi. L'esperienza e le prestazioni delle query di questo tipo sono quindi migliorate. 

## <a name="azure-data-lake-storage-gen2-integration-ga"></a>Integrazione di Azure Data Lake Storage Gen2 (GA)
Azure SQL Data Warehouse (SQL DW) dispone ora dell'integrazione nativa con Azure Data Lake Storage Gen2. I clienti possono ora caricare dati tramite tabelle esterne da ABFS in SQL Data Warehouse. Questa funzionalità consente ai clienti di integrarsi con i data Lake in Data Lake Storage Gen2. 

## <a name="bug-fixes"></a>Correzioni di bug

| Title | DESCRIZIONE |
|:---|:---|
| **CETAS to Parquet nelle classi di risorse di piccole dimensioni nel Data warehouse di DW2000 e altro** | Questa correzione identifica correttamente un riferimento Null nel percorso di codice Create External Table As to Parquet. |
|**Il valore della colonna Identity potrebbe andare perso durante qualche operazione CTAS** | Il valore di una colonna Identity potrebbe non essere mantenuto durante un CTAS a un'altra tabella. Segnalati in un blog: [ https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/ ](https://blog.westmonroepartners.com/azure-sql-dw-identity-column-bugs/). |
| **Errore interno in alcuni casi quando una sessione viene terminata mentre è ancora in esecuzione una query** | Questa correzione attiva un'eccezione InvalidOperationException se una sessione viene terminata quando la query è ancora in esecuzione. |
| **(Distribuzione di novembre 2018) I clienti rilevano prestazioni non ottimali quando tentano di caricare più file di piccole dimensioni da ADLS (Gen1) usando Polybase.** | Collo di bottiglia delle prestazioni del sistema durante la convalida del token di sicurezza AAD. I problemi di prestazioni sono stati risolti abilitando la memorizzazione nella cache dei token di sicurezza. |


## <a name="next-steps"></a>Passaggi successivi
Dopo aver appreso alcune informazioni su SQL Data Warehouse, vedere come [creare un SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se non si ha familiarità con Azure, il [glossario di Azure][Azure glossary] può essere utile quando si incontrano termini nuovi. Oppure vedere alcune delle altre risorse disponibili per SQL Data Warehouse.  

* [Casi di successo dei clienti]
* [Blog]
* [Richieste di funzionalità]
* [Video]
* [Blog Customer Advisory Team]
* [Forum su Stack Overflow]
* [Twitter]


[Blog]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blog Customer Advisory Team]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Casi di successo dei clienti]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Richieste di funzionalità]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Forum su Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Video]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
