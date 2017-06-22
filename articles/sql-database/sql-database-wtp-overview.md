---
title: App multi-tenant di esempio del database SQL di Azure | Microsoft Docs
description: Introduzione all&quot;app Wingtip Tickets (WTP) di esempio del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 265eab8104d8af7c510a88dffb9d70a2b3b37631
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Introduzione all'applicazione SaaS di esempio Wingtip Tickets Platform

L'applicazione SaaS Wingtip Tickets Platform (WTP) è un'app multi-tenant di esempio che dimostra i vantaggi peculiari di database SQL. L'app usa un database per ogni tenant, il modello di applicazione SaaS, per soddisfare le richieste di più tenant. L'app WTP è progettata per dimostrare le funzionalità del database SQL di Azure che supportano gli scenari SaaS, inclusi i modelli di progettazione e gestione SaaS. Per iniziare, è sufficiente [distribuire l'app WTP, operazione che richiede meno di cinque minuti](sql-database-saas-tutorial.md).

Dopo aver distribuito l'app WTP, esplorare la [raccolta di esercitazioni](#sql-database-saas-tutorials) che si basano sulla distribuzione iniziale. Ogni esercitazione è incentrata su attività tipiche implementate nelle applicazioni SaaS. Le attività vengono implementate seguendo i modelli SaaS che sfruttano le funzionalità predefinite di database SQL. I modelli descritti includono il provisioning di nuovi tenant, il ripristino dei database tenant, l'esecuzione di query distribuite su tutti i tenant e la distribuzione di modifiche allo schema in tutti i database tenant. Ogni esercitazione include script riutilizzabili, con spiegazioni dettagliate che semplificano notevolmente la comprensione e l'implementazione degli stessi modelli di gestione SaaS nelle proprie applicazioni.

Anche se l'applicazione WTP è completa e interessante come applicazione di esempio, è importante concentrarsi sui modelli SaaS fondamentali e le loro relazioni con il livello dati. In altre parole, concentrarsi sul livello dati senza dedicare troppo tempo all'analisi dell'app stessa. Comprendere l'implementazione di questi modelli SaaS fondamentali è cruciale per implementare gli stessi modelli nelle proprie applicazioni, tenendo naturalmente conto delle eventuali modifiche necessarie per soddisfare requisiti aziendali specifici.



## <a name="application-architecture"></a>Architettura dell'applicazione

L'app WTP usa il modello con un database per tenant e i pool elastici SQL per ottimizzare l'efficienza.
Usare un catalogo di tenant per il provisioning della gestione e della connettività.
App integrata, pool, monitoraggio e avvisi per il database (OMS).
Gestione di schema e dati di riferimento tra i tenant (processi di Database elastico).
Query tra tenant, analisi operativa (query elastica).
Uso di dati distribuiti geograficamente per una maggiore copertura.
Continuità aziendale, ripristino di singoli tenant (ripristino temporizzato), ripristino di emergenza su larga scala (ripristino geografico, replica geografica, ripristino di emergenza automatico), gestione self-service dei tenant (tramite API di gestione), ripristino temporizzato per i recupero da problemi causati internamente.

L'applicazione Wingtip principale usa un pool con tre tenant di esempio, oltre a un database di catalogo.

![Architettura dell'app WTP](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>Esercitazioni su SaaS con l'applicazione WTP e database SQL

Le esercitazioni seguenti sono basate sulla distribuzione iniziale dell'[applicazione di esempio SaaS Wingtip Tickets Platform (WTP)](sql-database-saas-tutorial.md):

| Area | Descrizione | Posizione degli script |
|:--|:--|:--|
|[Esercitazione sul provisioning e la catalogazione dei tenant](sql-database-saas-tutorial-provision-and-catalog.md)| Effettuare il provisioning di nuovi tenant e registrarli nel catalogo | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[Esercitazione sul monitoraggio e la gestione delle prestazioni](sql-database-saas-tutorial-performance-monitoring.md)| Monitorare e gestire le prestazioni dei database e del pool | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[Esercitazione sul ripristino di un tenant singolo](sql-database-saas-tutorial-restore-single-tenant.md)| Ripristinare database tenant | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[Esercitazione sulla gestione dello schema del tenant](sql-database-saas-tutorial-schema-management.md)| Eseguire query su tutti i tenant  | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[Esercitazione sull'esecuzione di analisi ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Creare un database di analisi ad-hoc ed eseguire query su tutti i tenant  | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[Esercitazione sulla gestione con Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Configurare ed esplorare Log Analytics | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[Esercitazione sull'esecuzione di analisi dei tenant](sql-database-saas-tutorial-tenant-analytics.md) | Configurare ed eseguire query di analisi sui tenant | [Script su GitHub](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="working-with-the-wtp-powershell-scripts"></a>Utilizzo degli script di PowerShell per l'applicazione WTP

I vantaggi dell'utilizzo dell'applicazione WTP derivano dalla possibilità di esaminare nei dettagli gli script forniti e studiare le modalità di implementazione dei diversi modelli SaaS.

Per visualizzare gli script e i moduli forniti esaminandoli in dettaglio con maggiore facilità per comprenderli al meglio, usare [Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise). Dato che la maggior parte degli script con prefisso *Demo-* contiene variabili che possono essere modificate prima dell'esecuzione, con PowerShell ISE è più semplice usare questi script.

Per ogni distribuzione dell'app WTP è disponibile un file **UserConfig.psm1** che contiene due parametri per l'impostazione del nome del gruppo di risorse e dell'utente definiti durante la distribuzione. Una volta completata la distribuzione, modificare il modulo **UserConfig.psm1** impostando i parametri _ResourceGroupName_ e _Name_. Questi valori vengono usati per la corretta esecuzione di altri script, quindi è consigliabile impostarli al termine della distribuzione.



### <a name="execute-scripts-by-pressing-f5"></a>Eseguire gli script premendo F5

Vari script usano *$PSScriptRoot* per consentire l'esplorazione delle cartelle e questa variabile viene valutata solo quando lo script viene eseguito premendo **F5**.  L'evidenziazione e l'esecuzione di una selezione (**F8**) può causare errori, quindi premere **F5** per l'esecuzione degli script WTP.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Eseguire gli script un'istruzione alla volta per esaminare l'implementazione

L'analisi degli script è fruttuosa in particolare eseguendoli un'istruzione alla volta in modo da esaminare le operazioni eseguite. Iniziare con gli script _Demo -_ di primo livello che offrono un flusso di lavoro di alto livello e di facile lettura, che mostra i passaggi necessari per completare ciascuna attività. Analizzare più approfonditamente le singole chiamate per scoprire i dettagli di implementazione per i diversi modelli SaaS.

Suggerimenti per l'utilizzo e il [debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise):

* Aprire e configurare gli script Demo- in PowerShell ISE.
* Eseguire o continuare l'esecuzione con **F5**. Non è consigliabile usare **F8** perché la variabile *$PSScriptRoot* non viene valutata durante l'esecuzione di selezioni di uno script.
* Aggiungere punti di interruzione facendo clic o selezionando una riga e premendo **F9**.
* Eseguire una funzione o una chiamata dello script in blocco con **F10** (comando Esegui istruzione/routine).
* Eseguire una funzione o una chiamata dello script un'istruzione alla volta con **F11**.
* Uscire dalla funzione o dalla chiamata dello script corrente con **MAIUSC+F11**.




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Esplorare lo schema del database ed eseguire query SQL tramite SSMS

Usare [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) per connettersi ai server e ai database WTP e per esplorarli.

L'app di esempio WTP inizialmente ha due server di database SQL a cui connettersi, il server *tenants1* e il server *catalog*:


1. Aprire *SSMS* e connettersi al server *tenants1-&lt;User&gt;.database.windows.net*.
2. Fare clic su **Connetti** > **Motore di database...**:

   ![server di catalogo](media/sql-database-wtp-overview/connect.png)

1. Le credenziali per la demo sono: Account di accesso = *developer*, Password = *P@ssword1*

   ![connessione](media\sql-database-wtp-overview\tenants1-connect.png)

1. Ripetere i passaggi 2 e 3 e connettersi al server *catalog-&lt;Utente&gt;.database.windows.net*.

Dopo avere stabilito la connessione, entrambi i server dovrebbero essere visibili. È possibile che vengano visualizzati più o meno database a seconda del numero di tenant di cui è stato effettuato il provisioning:

![esplora oggetti](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Passaggi successivi

[Distribuire l'applicazione di esempio SaaS Wingtip Tickets](sql-database-saas-tutorial.md)
