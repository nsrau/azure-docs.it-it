---
title: Gestire lo schema del database SQL di Azure in un'app multi-tenant | Microsoft Docs
description: "Gestire lo schema per più tenant in un'applicazione multi-tenant che usa il database SQL di Azure"
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.openlocfilehash: ad7434efcead9a250bda9958ade74e798609a25d
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gestire lo schema per più tenant in un'applicazione multi-tenant che usa il database SQL di Azure

La [prima esercitazione sul database per tenant SaaS Wingtip Tickets](saas-dbpertenant-get-started-deploy.md) illustra come l'app può effettuare il provisioning di un database tenant e registrarlo nel catalogo. Come qualsiasi altra applicazione, l'app del database per tenant SaaS Wingtip Tickets evolverà nel tempo e a volte saranno necessarie modifiche al database. Tali modifiche possono includere uno schema nuovo o modificato, dati di riferimento nuovi o modificati e attività di manutenzione periodiche del database per garantire prestazioni ottimali dell'app. Con un'applicazione SaaS queste modifiche devono essere distribuite in modo coordinato a un gruppo potenzialmente enorme di database tenant. Per essere incluse in database tenant futuri, le modifiche devono essere incorporate nel processo di provisioning.

Questa esercitazione illustra due scenari: distribuzione degli aggiornamenti dei dati di riferimento per tutti i tenant e restituzione di un indice per la tabella contenente i dati di riferimento. La funzionalità [processi elastici](sql-database-elastic-jobs-overview.md) viene usata per eseguire queste operazioni su tutti i tenant e il database tenant *di riferimento* viene usato come modello per i nuovi database.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Creare un account di processo
> * Eseguire query in più tenant
> * Aggiornare i dati in tutti i database tenant
> * Creare un indice su una tabella in tutti i database tenant


Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* È stata distribuita l'app del database per tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Deploy and explore the Wingtip Tickets SaaS Database Per Tenant application](saas-dbpertenant-get-started-deploy.md) (Distribuire ed esplorare l'applicazione del database per tenant SaaS Wingtip Tickets)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* La versione più recente di SQL Server Management Studio (SSMS) è installata. [Scaricare e installare SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Questa esercitazione usa funzionalità del servizio database SQL incluse in un'anteprima limitata (processi di Database elastico). Per eseguire questa esercitazione, fornire l'ID della sottoscrizione a SaaSFeedback@microsoft.com indicando nell'oggetto del messaggio "Elastic Jobs Preview". Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, [scaricare e installare i cmdlet più recenti della versione preliminare](https://github.com/jaredmoo/azure-powershell/releases). Questa versione di anteprima è limitata. Per eventuali domande o richieste di supporto, contattare SaaSFeedback@microsoft.com.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Introduzione ai modelli di gestione dello schema di SaaS

Il modello SaaS con tenant singolo per ogni database trae numerosi vantaggi dall'isolamento dei dati risultante, ma allo stesso tempo introduce complicazioni aggiuntive correlate alla manutenzione e gestione di numerosi database. Il servizio [Processi elastici](sql-database-elastic-jobs-overview.md) facilita l'amministrazione e la gestione del livello dati SQL. I processi consentono di eseguire attività (script T-SQL) in modo sicuro e affidabile su un gruppo di database, indipendentemente dall'interazione con gli utenti o dal loro input. Questo metodo può essere usato per distribuire modifiche dello schema e dei dati di riferimento comuni a tutti i tenant in un'applicazione. Il servizio Processi elastici può essere usato anche per gestire una copia *di riferimento* del database usato per creare nuovi tenant, assicurandosi che disponga sempre dello schema e dei dati di riferimento più recenti.

![schermata](media/saas-tenancy-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Anteprima limitata del servizio Processi elastici

È disponibile una nuova versione del servizio Processi elastici che è ora una funzionalità integrata di database SQL di Azure, che non richiede servizi o componenti aggiuntivi. Questa nuova versione del servizio Processi elastici è attualmente in anteprima limitata. Questa anteprima limitata supporta attualmente PowerShell per creare gli account per processi e T-SQL per creare e gestire i processi.

> [!NOTE]
> *Questa esercitazione usa funzionalità del servizio database SQL incluse in un'anteprima limitata (processi di Database elastico). Per eseguire questa esercitazione, fornire l'ID della sottoscrizione a SaaSFeedback@microsoft.com indicando nell'oggetto del messaggio "Elastic Jobs Preview". Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, [scaricare e installare i cmdlet più recenti della versione preliminare](https://github.com/jaredmoo/azure-powershell/releases). Questa versione di anteprima è limitata. Per eventuali domande o richieste di supporto, contattare SaaSFeedback@microsoft.com.*

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script e il codice sorgente dell'applicazione del database per tenant SaaS Wingtip Tickets sono disponibili nel [repository GitHub WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). [Passaggi per scaricare gli script dell'applicazione del database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="create-a-job-account-database-and-new-job-account"></a>Creare un database di account per processi e un nuovo account per processi

Per questa esercitazione è necessario usare PowerShell per creare il database degli account per processi e l'account per processi. Come MSDB e SQL Agent, il servizio Processi elastici usa un database SQL di Azure per archiviare le definizioni dei processi, lo stato dei processi e la cronologia. Dopo aver creato l'account di processo, è possibile creare e monitorare i processi immediatamente.

1. In **PowerShell ISE** aprire \\…\\Learning Modules (Moduli di apprendimento)\\Schema Management (Gestione schema)*Demo-SchemaManagement.ps1*.
1. Premere **F5** per eseguire lo script.

Lo script *Demo-SchemaManagement.ps1* chiama lo script *Deploy-SchemaManagement.ps1* per creare un database *S2* denominato **jobaccount** nel server di catalogo. Viene quindi creato l'account per processi, passando il database jobaccount come parametro per la chiamata di creazione dell'account per processi.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creare un processo per distribuire nuovi dati di riferimento a tutti i tenant

Ogni database tenant include un set di tipi di sedi che definiscono il tipo di eventi ospitato in una sede. Questo esercizio illustra come distribuire un aggiornamento a tutti i database tenant per aggiungere due tipi di eventi aggiuntivi: *Motorcycle Racing* (Gare motociclistiche) e *Swimming Club* (Club nuoto). Questi tipi di sedi di eventi corrispondono all'immagine di sfondo visualizzata nell'app degli eventi del tenant.

Fare clic nel menu a discesa Venue Type (Tipo di sede) e verificare che siano disponibili solo 10 opzioni e in particolare che i tipi 'Motorcycle Racing' e 'Swimming Club' non siano inclusi nell'elenco.

L'esercizio prevede ora la creazione di un processo per aggiornare la tabella *VenueTypes* in tutti i database tenant e aggiungere i nuovi tipi di eventi.

Per creare un nuovo processo, viene usato un set di stored procedure di sistema per i processi creato nel database jobaccount al momento della creazione dell'account per processi.

1. Aprire SSMS e connettersi al server di catalogo: catalog-dpt-\<utente\>.database.windows.net server
1. Connettersi anche al server tenant: tenants1-dpt-\<utente\>.database.windows.net
1. Passare al database *contosoconcerthall* nel server *tenants1-dpt-\<utente\>* ed eseguire una query sulla tabella *VenueTypes* per verificare che *Motorcycle Racing* e *Swimming Club* **non siano inclusi** nel set di risultati.
1. In SSMS, aprire il file …\\Learning Modules\\Schema Management\\DeployReferenceData.sql
1. Modificare l'istruzione: SET @wtpUser = &lt;utente&gt; e sostituire il valore Utente usato per la distribuzione dell'app del database per tenant SaaS Wingtip Tickets
1. Assicurarsi di essere connessi al database jobaccount e premere **F5** per eseguire lo script

* **sp\_add\_target\_group** crea il nome del gruppo di destinazione DemoServerGroup a cui è ora necessario aggiungere i membri di destinazione.
* **sp\_add\_target\_group\_member** aggiunge un tipo di membro di destinazione *server* che desume che tutti i database nel server al momento dell'esecuzione del processo devono essere inclusi nel processo (si noti che si tratta del server tenants1-dpt-&lt;Utente&gt; contenente i database tenant). Viene poi aggiunto un tipo di membro di destinazione *database*, nello specifico il database di riferimento basetenantdb che risiede nel server catalog-dpt-&lt;Utente&gt; e infine viene aggiunto un altro tipo di membro del gruppo di destinazione *database* per includere il database adhocanalytics usato in un'esercitazione successiva.
* **sp\_add\_job** crea un processo denominato "Reference Data Deployment" (Distribuzione dati di riferimento)
* **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare la tabella di riferimento VenueTypes
* Le restanti viste nello script consentono di confermare l'esistenza degli oggetti e gestire il monitoraggio dell'esecuzione del processo. Usare queste query per esaminare il valore di stato nella colonna **lifecycle** e determinare quando il processo viene completato in tutti i database tenant e nei due database aggiuntivi che contengono la tabella di riferimento.

1. In SSMS passare al database *contosoconcerthall* nel server *tenants1-dpt-\<utente\>* ed eseguire una query sulla tabella *VenueTypes* per verificare che *Motorcycle Racing* e *Swimming Club* **siano inclusi** nel set di risultati.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creare un processo per gestire l'indice della tabella di riferimento

In modo simile all'esercizio precedente, questo esercizio crea un processo per ricompilare l'indice sulla chiave primaria della tabella di riferimento, ovvero un'operazione tipica di gestione dei database che un amministratore potrebbe eseguire dopo un caricamento di dati di grandi dimensioni in una tabella.

Creare un processo usando le stesse stored procedure di sistema per i processi.

1. Aprire SSMS e connettersi al server catalog-dpt-&lt;Utente&gt;.database.windows.net
1. Aprire il file …\\Learning Modules\\Schema Management\\OnlineReindex.sql
1. Fare clic con il pulsante destro del mouse, scegliere Connessione e connettersi al server catalog-dpt-&lt;Utente&gt;.database.windows.net, se necessario
1. Assicurarsi di essere connessi al database jobaccount e premere F5 per eseguire lo script

* sp\_add\_job crea un nuovo processo denominato "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* sp\_add\_jobstep crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare l'indice




## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Creare un account per processi per eseguire query su più tenant
> * Aggiornare i dati in tutti i database tenant
> * Creare un indice su una tabella in tutti i database tenant

[Esercitazione sull'analisi ad hoc](saas-tenancy-adhoc-analytics.md)


## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione dell'applicazione del database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Gestione dei database cloud con scalabilità orizzontale](sql-database-elastic-jobs-overview.md)
* [Creare e gestire database SQL di Azure con scalabilità orizzontale](sql-database-elastic-jobs-create-and-manage.md)