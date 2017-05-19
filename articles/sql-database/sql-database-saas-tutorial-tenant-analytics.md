---
title: "Eseguire query di analisi su più tenant (applicazione SaaS di esempio con database SQL di Azure) | Microsoft Docs"
description: "Eseguire query di analisi su più tenant"
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
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b512e2f7833be1947ef7674d6e0266879789ac5a
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="run-analytics-queries-against-multiple-tenants"></a>Eseguire query di analisi su più tenant

Questa esercitazione descrive come eseguire query di analisi su ogni tenant nel catalogo. Viene creato un processo elastico che esegue le query. Il processo recupera i dati e li carica in un database di analisi separato creato nel server di catalogo. È possibile eseguire query su questo database per estrarre informazioni dettagliate nascoste nei dati operativi quotidiani di tutti i tenant. Come output del processo, viene creata una tabella dalle query che restituiscono risultati all'interno del database di analisi tenant.


In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare il database di analisi tenant
> * Creare un processo pianificato per recuperare i dati e popolare il database di analisi

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app WTP è stata distribuita. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare un'applicazione SaaS multi-tenant di esempio che usa il database di SQL Azure](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* La versione più recente di SQL Server Management Studio (SSMS) è installata. [Scaricare e installare SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Modello di analisi operativa del tenant

Una delle grandi opportunità offerte dalle applicazioni SaaS è la possibilità di usare i dati completi del tenant archiviati nel cloud. Questi dati possono essere usati per ottenere informazioni dettagliate sul funzionamento e l'utilizzo dell'applicazione e dei tenant, nonché come base per progettare sviluppi futuri, miglioramenti dell'usabilità e altri investimenti per l'app e la piattaforma. L'accesso a questi dati è semplice quando sono raccolti in un singolo database multi-tenant, ma non è altrettanto semplice in una distribuzione su larga scala potenzialmente in migliaia di database. Uno degli approcci possibili per l'accesso ai dati è l'uso dei processi elastici, che consentono di acquisire in un database e una tabella di output i risultati delle query che restituiscono risultati dall'esecuzione del processo.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="deploy-a-database-for-tenant-analytics-results"></a>Distribuire un database per i risultati di analisi dei tenant

Per questa esercitazione è necessario avere distribuito un database per acquisire i risultati dall'esecuzione dei processi degli script, che contengono query che restituiscono risultati. Per iniziare verrà creato un database denominato tenantanalytics a questo scopo.

1. Aprire …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* in *PowerShell ISE* e impostare il valore seguente:
   * **$DemoScenario** = **2** *Distribuire il database di analisi operativo*
1. Premere **F5** per eseguire lo script della demo (che chiama lo script *Deploy-TenantAnalyticsDB.ps1*) che crea il database di analisi del tenant.

## <a name="create-some-data-for-the-demo"></a>Creare alcuni dati per la demo

1. Aprire …\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*Demo-TenantAnalyticsDB.ps1* in *PowerShell ISE* e impostare il valore seguente:
   * **$DemoScenario** = **1** *Acquistare biglietti per gli eventi in tutte le sedi*
1. Premere **F5** per eseguire lo script e creare la cronologia di acquisto dei biglietti.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Creare un processo pianificato per recuperare i dati analitici dei tenant sugli acquisti di biglietti

Questo script crea un processo per recuperare informazioni sull'acquisto di biglietti da tutti i tenant. Dopo l'aggregazione dei risultati in una singola tabella, si ottengono metriche dettagliate e approfondite sui modelli di acquisto dei biglietti in tutti i tenant.

1. Aprire SSMS e connettersi al server catalog-\<utente\>.database.windows.net
1. Aprire ...\\Learning Modules\\Operational Analytics\\Tenant Analytics\\*TicketPurchasesfromAllTenants.sql*
1. Modificare \<WtpUser\>, usare il nome utente usato durante la distribuzione dell'app WTP all'inizio dello script, **sp\_add\_target\_group\_member** e **sp\_add\_jobstep**
1. Fare clic con il pulsante destro del mouse, scegliere **Connessione** e connettersi al server catalog-\<UtenteWtp\>.database.windows.net, se non è già connesso
1. Assicurarsi di essere connessi al database **jobaccount** e premere **F5** per eseguire lo script

* **sp\_add\_target\_group** crea il nome del gruppo di destinazione*TenantGroup* a cui è necessario aggiungere i membri di destinazione.
* **sp\_add\_target\_group\_member** aggiunge un tipo di membro di destinazione *server*, che desume che tutti i database nel server al momento dell'esecuzione del processo devono essere inclusi nel processo (si noti che si tratta del database customer1-&lt;UtenteWtp&gt; contenente i database tenant).
* **sp\_add\_job** crea un nuovo processo pianificato settimanale denominato "Ticket Purchases from all Tenants" (Acquisti di biglietti da tutti i tenant)
* **sp\_add\_jobstep** crea il passaggio del processo che contiene il testo dei comandi T-SQL per recuperare tutte le informazioni sugli acquisti di biglietti da tutti i tenant e per copiare il set di risultati restituito in una tabella denominata *AllTicketsPurchasesfromAllTenants*
* Le restanti viste nello script consentono di confermare l'esistenza degli oggetti e gestire il monitoraggio dell'esecuzione del processo. Controllare il valore di stato nella colonna **lifecycle** per monitorare lo stato. Lo stato Succeeded indica che il processo è stato completato correttamente in tutti i database tenant e che sono stati creati i due database aggiuntivi contenenti la tabella di riferimento.

La corretta esecuzione dello script dovrebbe restituire risultati simili ai seguenti:

![risultati](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Creare un processo per recuperare un conteggio di riepilogo degli acquisti di biglietti da tutti i tenant

Questo script crea un processo per recuperare la somma di tutti gli acquisiti di biglietti da tutti i tenant.

1. Aprire SSMS e connettersi al server *catalog- &lt;utente&gt;.database.windows.net*
1. Aprire il file …\\Learning Modules\\Provision and Catalog\\Operational Analytics\\Tenant Analytics\\*Results-TicketPurchasesfromAllTenants.sql*
1. Modificare &lt;WtpUser&gt;, usare il nome utente usato durante la distribuzione dell'app WTP nello script, nella stored procedure **sp\_add\_jobstep**
1. Fare clic con il pulsante destro del mouse, scegliere **Connessione** e connettersi al server catalog-\<UtenteWtp\>.database.windows.net, se non è già connesso
1. Assicurarsi di essere connessi al database **tenantanalytics** e premere **F5** per eseguire lo script

La corretta esecuzione dello script dovrebbe restituire risultati simili ai seguenti:

![results](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* **sp\_add\_job** crea un nuovo processo pianificato settimanale denominato "ResultsTicketsOrders"

* **sp\_add\_jobstep** crea il passaggio del processo che contiene il testo dei comandi T-SQL per recuperare tutte le informazioni sugli acquisti di biglietti da tutti i tenant e per copiare il set di risultati restituito in una tabella denominata CountofTicketOrders

* Le restanti viste nello script consentono di confermare l'esistenza degli oggetti e gestire il monitoraggio dell'esecuzione del processo. Controllare il valore di stato nella colonna **lifecycle** per monitorare lo stato. Lo stato Succeeded indica che il processo è stato completato correttamente in tutti i database tenant e che sono stati creati i due database aggiuntivi contenenti la tabella di riferimento.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Distribuire un database di analisi dei tenant
> * Creare un processo pianificato per recuperare i dati analitici da tutti i tenant

Congratulazioni.

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Processi elastici](sql-database-elastic-jobs-overview.md)
