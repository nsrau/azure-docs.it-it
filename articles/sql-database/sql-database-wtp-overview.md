---
title: Esempio di app multi-tenant di database SQL di Azure - SaaS Wingtip | Microsoft Docs
description: Uso dell'esempio SaaS Wingtip, un'applicazione multi-tenant di esempio basata sul database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 68a9d97a881f3a7628a08b66091c3feb4c4dbbfe
ms.contentlocale: it-it
ms.lasthandoff: 09/13/2017

---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>Introduzione a un esempio di app SaaS multi-tenant di database SQL

L'applicazione *SaaS Wingtip* è un'app multi-tenant di esempio che dimostra i vantaggi peculiari del database SQL. L'app usa un database per ogni tenant, il modello di applicazione SaaS, per soddisfare le richieste di più tenant. L'app è progettata per presentare le funzionalità del database SQL di Azure che supportano gli scenari SaaS, inclusi diversi modelli di progettazione e gestione SaaS. Per iniziare, è sufficiente distribuire l'app SaaS Wingtip, operazione che richiede meno di cinque minuti.

Il codice sorgente e gli script di gestione dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Per eseguire gli script, [scaricare la cartella Learning Modules](#download-and-unblock-the-wingtip-saas-scripts) nel computer locale.

## <a name="sql-database-wingtip-saas-tutorials"></a>Esercitazioni su SaaS con l'applicazione Wingtip e database SQL

Dopo avere distribuito l'app, esplorare le esercitazioni seguenti che si basano sulla distribuzione iniziale. Queste esercitazioni illustrano i comuni modelli SaaS che sfruttano le funzionalità predefinite del database SQL, di SQL Data Warehouse e di altri servizi di Azure. Le esercitazioni includono script di PowerShell, con spiegazioni dettagliate che semplificano notevolmente la comprensione e l'implementazione degli stessi modelli di gestione SaaS nelle proprie applicazioni.


| Esercitazione | Descrizione |
|:--|:--|
|[Distribuire ed esplorare l'applicazione SaaS Wingtip](sql-database-saas-tutorial.md)| **INIZIARE DA QUI.** Distribuire ed esplorare l'applicazione SaaS Wingtip nella sottoscrizione di Azure. |
|[Effettuare il provisioning di tenant e registrarli nel catalogo](sql-database-saas-tutorial-provision-and-catalog.md)| Informazioni su come l'applicazione si connette ai tenant usando un database di catalogo e su come il catalogo esegue il mapping dei tenant ai dati. |
|[Monitorare e gestire le prestazioni](sql-database-saas-tutorial-performance-monitoring.md)| Informazioni su come usare le funzionalità di monitoraggio del database SQL e su come impostare avvisi quando vengono superate le soglie per le prestazioni. |
|[Eseguire il monitoraggio con Log Analytics (OMS)](sql-database-saas-tutorial-log-analytics.md) | Informazioni su come usare [Log Analytics](../log-analytics/log-analytics-overview.md) per monitorare grandi quantità di risorse in più pool. |
|[Ripristinare un tenant singolo](sql-database-saas-tutorial-restore-single-tenant.md)| Informazioni su come eseguire un ripristino temporizzato di un database tenant. È inclusa anche la procedura per ripristinare un database parallelo, lasciando il database esistente online. |
|[Gestire uno schema di tenant](sql-database-saas-tutorial-schema-management.md)| Informazioni su come aggiornare lo schema e i dati di riferimento in tutti i tenant SaaS Wingtip. |
|[Eseguire analisi ad hoc](sql-database-saas-tutorial-adhoc-analytics.md) | Creare un database di analisi ad-hoc ed eseguire query distribuite in tempo reale su tutti i tenant.  |
|[Eseguire l'analisi di tenant](sql-database-saas-tutorial-tenant-analytics.md) | Estrarre i dati del tenant in un database di analisi o in un data warehouse per l'esecuzione offline di query di analisi. |



## <a name="application-architecture"></a>Architettura dell'applicazione

L'app SaaS Wingtip usa il modello con un database per tenant e i pool elastici SQL per ottimizzare l'efficienza. Per il provisioning e il mapping dei tenant ai dati, viene usato un database di catalogo. L'applicazione SaaS Wingtip principale usa un pool con tre tenant di esempio, oltre al database del catalogo. Completando molte delle esercitazioni sull'app SaaS Wingtip, si aggiungono componenti alla distribuzione iniziale, introducendo database di analisi, gestione dello schema tra database e così via.


![Architettura SaaS Wingtip](media/sql-database-wtp-overview/app-architecture.png)


Mentre si eseguono le esercitazioni e si utilizza l'app, è importante concentrarsi sugli schemi SaaS perché sono correlati al livello dati. In altre parole, concentrarsi sul livello dati senza dedicare troppo tempo all'analisi dell'app stessa. Comprendere l'implementazione di questi modelli SaaS è cruciale per implementare gli stessi modelli nelle proprie applicazioni, tenendo naturalmente conto delle eventuali modifiche necessarie per soddisfare requisiti aziendali specifici.

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Scaricare e sbloccare gli script dell'app SaaS Wingtip

I contenuti eseguibili (script, DLL) possono venire bloccati da Windows quando i file ZIP vengono scaricati da un'origine esterna ed estratti. Quando si estraggono gli script da un file ZIP, ***seguire questa procedura per sbloccare il file ZIP prima di estrarlo***. In questo modo sarà possibile eseguire gli script.

1. Passare al [repository GitHub SaaS Wingtip](https://github.com/Microsoft/WingtipSaaS).
1. Fare clic su **Clona o scarica**.
1. Fare clic su **Scarica ZIP** e salvare il file.
1. Fare clic con il pulsante destro del mouse sul file **WingtipSaaS-master.zip** e scegliere **Proprietà**.
1. Nella scheda **Generale** selezionare **Annulla blocco**.
1. Fare clic su **OK**.
1. Estrarre i file.

Gli script si trovano nella cartella *..\\WingtipSaaS-master\\Learning Modules*.


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>Utilizzo degli script di PowerShell per l'applicazione SaaS Wingtip

Per ottenere il massimo dall'esempio, è necessario esplorare gli script forniti. Usare i punti di interruzione e scorrere gli script, esaminando in dettaglio come vengono implementati i diversi schemi SaaS. Per scorrere facilmente gli script e i moduli forniti e comprenderli a fondo, è consigliabile usare [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise).

### <a name="update-the-configuration-file-for-your-deployment"></a>Aggiornare il file di configurazione per la distribuzione

Modificare il file **UserConfig.psm1** con il valore per il gruppo di risorse e per l'utente usato durante la distribuzione:

1. Aprire *PowerShell ISE* e caricare ...\\Learning Modules\\*UserConfig.psm1* 
1. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
1. Salvare le modifiche.

Impostando qui questi valori specifici della distribuzione, non sarà necessario aggiornarli in ogni script.

### <a name="execute-scripts-by-pressing-f5"></a>Eseguire gli script premendo F5

Vari script usano *$PSScriptRoot* per esplorare le cartelle e *$PSScriptRoot* viene valutato solo quando gli script vengono eseguiti premendo **F5**.  L'evidenziazione e l'esecuzione di una selezione (**F8**) può causare errori, quindi premere **F5** per l'esecuzione degli script.

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>Eseguire gli script un'istruzione alla volta per esaminare l'implementazione

Il modo migliore di comprendere gli script è di scorrerli per capirne la funzione. Esaminare gli script **Demo-** inclusi che presentano un flusso di lavoro generale facilmente interpretabile. Gli script **Demo-** illustrano la procedura necessaria per eseguire ogni attività, quindi impostare punti di interruzione e analizzare in modo più approfondito le singole chiamate per vedere i dettagli dell'implementazione per i diversi schemi SaaS.

Suggerimenti per esplorare e scorrere gli script PowerShell:

* Aprire gli script **Demo-** in PowerShell ISE.
* Eseguire o continuare con **F5**. Non è consigliabile usare **F8** perché la variabile *$PSScriptRoot* non viene valutata durante l'esecuzione di selezioni di uno script.
* Aggiungere punti di interruzione facendo clic o selezionando una riga e premendo **F9**.
* Eseguire una funzione o una chiamata dello script in blocco con **F10** (comando Esegui istruzione/routine).
* Eseguire una funzione o una chiamata dello script un'istruzione alla volta con **F11**.
* Uscire dalla funzione o dalla chiamata dello script corrente con **MAIUSC+F11**.


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>Esplorare lo schema del database ed eseguire query SQL tramite SSMS

Usare [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) per connettersi ai server e ai database dell'applicazione e per esplorarli.

La distribuzione inizialmente ha due server di database SQL a cui connettersi, il server *tenants1-&lt;Utente&gt;* e il server *catalog-&lt;Utente&gt;*. Per assicurare una connessione demo corretta, entrambi i server hanno una [regola del firewall](sql-database-firewall-configure.md) che consente tutti gli IP.


1. Aprire *SSMS* e connettersi al server *tenants1-&lt;User&gt;.database.windows.net*.
1. Fare clic su **Connetti** > **Motore di database...**:

   ![server di catalogo](media/sql-database-wtp-overview/connect.png)

1. Le credenziali per la demo sono: Account di accesso = *developer*, Password = *P@ssword1*

   ![connessione](media\sql-database-wtp-overview\tenants1-connect.png)

1. Ripetere i passaggi 2 e 3 e connettersi al server *catalog-&lt;Utente&gt;.database.windows.net*.

Dopo avere stabilito la connessione, entrambi i server dovrebbero essere visibili. L'elenco dei propri database potrebbe variare a seconda dei tenant di cui si è effettuato il provisioning:

![esplora oggetti](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>Passaggi successivi

[Distribuire l'applicazione SaaS Wingtip](sql-database-saas-tutorial.md)
