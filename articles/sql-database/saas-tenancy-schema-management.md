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
ms.openlocfilehash: ac60888d1464d3245bb35e2e3505b16ef4128d36
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Gestire lo schema in un'applicazione SaaS usando il modello con un database per ogni tenant con il database SQL di Azure

Quando l'applicazione di database evolve, è necessario apportare modifiche allo schema di database o ai dati di riferimento  nonché eseguire periodicamente attività di manutenzione del database. La gestione di un'applicazione che usa il modello con un database per ogni tenant richiede di applicare le modifiche o le attività di manutenzione in una serie di database del tenant.

Questa esercitazione illustra due scenari: distribuzione degli aggiornamenti dei dati di riferimento per tutti i tenant e ricompilazione di un indice per la tabella contenente i dati di riferimento. La funzionalità [processi elastici](sql-database-elastic-jobs-overview.md) consente di eseguire tali azioni su tutti i database del tenant e sul database modello usato per creare nuovi database tenant.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Creare un agente processo
> * Eseguire i processi T-SQL in tutti i database tenant
> * Aggiornare i dati di riferimento in tutti i database tenant
> * Creare un indice su una tabella in tutti i database tenant


Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* È stata distribuita l'app del database per tenant SaaS Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Deploy and explore the Wingtip Tickets SaaS Database Per Tenant application](saas-dbpertenant-get-started-deploy.md) (Distribuire ed esplorare l'applicazione del database per tenant SaaS Wingtip Tickets)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* La versione più recente di SQL Server Management Studio (SSMS) è installata. [Scaricare e installare SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Questa esercitazione usa funzionalità del servizio database SQL incluse in un'anteprima limitata (processi di database elastico). Per eseguire questa esercitazione, fornire l'ID della sottoscrizione a SaaSFeedback@microsoft.com indicando nell'oggetto del messaggio Elastic Jobs Preview. Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, [scaricare e installare i cmdlet più recenti della versione preliminare](https://github.com/jaredmoo/azure-powershell/releases). Questa versione di anteprima è limitata. Per eventuali domande o richieste di supporto, contattare SaaSFeedback@microsoft.com.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduzione ai modelli di gestione dello schema SaaS

Il modello con un database per ogni tenant consente di isolare i dati del tenant in modo efficace, ma aumenta il numero di database per cui eseguire gestione e manutenzione. Il servizio [Processi elastici](sql-database-elastic-jobs-overview.md) facilita l'amministrazione e la gestione dei database SQL. I processi consentono di eseguire in modo sicuro e affidabile attività (script T-SQL) in un gruppo di database. I processi possono anche distribuire modifiche ai dati di riferimento comuni e degli schemi in tutti i database tenant in un'applicazione. Il servizio Processi elastici può essere usato infine per gestire un database *modello* del database usato per creare nuovi tenant, assicurandosi che disponga sempre dello schema e dei dati di riferimento più recenti.

![schermata](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Anteprima limitata del servizio Processi elastici

È disponibile una nuova versione del servizio Processi elastici che è ora una funzionalità integrata di database SQL di Azure. Questa nuova versione del servizio Processi elastici è attualmente in anteprima limitata. Questa anteprima limitata supporta attualmente l'utilizzo di PowerShell per creare un agente processo e T-SQL per creare e gestire i processi.

> [!NOTE]
> Questa esercitazione usa funzionalità del servizio database SQL incluse in un'anteprima limitata (processi di database elastico). Per eseguire questa esercitazione, fornire l'ID della sottoscrizione a SaaSFeedback@microsoft.com indicando nell'oggetto del messaggio Elastic Jobs Preview. Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, [scaricare e installare i cmdlet più recenti della versione preliminare](https://github.com/jaredmoo/azure-powershell/releases). Questa versione di anteprima è limitata. Per eventuali domande o richieste di supporto, contattare SaaSFeedback@microsoft.com.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione del database per tenant SaaS Wingtip Tickets

Gli script di gestione e il codice sorgente sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Creare un database dell'agente processo e un nuovo agente processo

Questa esercitazione richiede l'uso di PowerShell per creare un agente processo e il relativo database di backup. Il database dell'agente processo contiene le definizioni e lo stato del processo e la cronologia. Dopo la creazione dell'agente processo e del relativo database, è possibile creare e monitorare i processi immediatamente.

1. In **PowerShell ISE** aprire \\…\\Learning Modules (Moduli di apprendimento)\\Schema Management (Gestione schema)*Demo-SchemaManagement.ps1*.
1. Premere **F5** per eseguire lo script.

Lo script *Demo-SchemaManagement.ps1* chiama lo script *Deploy-SchemaManagement.ps1* per creare un database SQL denominato *osagent* nel server di catalogo. Lo script crea quindi l'agente processo usando il database come parametro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creare un processo per distribuire nuovi dati di riferimento a tutti i tenant

Nell'app Wingtip Tickets ogni database tenant include un set di tipi di sedi supportati. Ogni sede è di un tipo specifico, che definisce il tipo di eventi che possono essere ospitati e determina l'immagine di sfondo usata nell'app. Per fare in modo che l'applicazione supporti nuovi tipi di eventi, è necessario aggiornare i dati di riferimento e aggiungere nuovi tipi di sede.  Questo esercizio illustra come distribuire un aggiornamento a tutti i database tenant per aggiungere due tipi di eventi aggiuntivi: *Motorcycle Racing* (Gare motociclistiche) e *Swimming Club* (Club nuoto).

Esaminare prima i tipi di sede inclusi in ogni database tenant. Connettersi a uno dei database tenant in SQL Server Management Studio (SSMS) ed esaminare la tabella VenueTypes.  È anche possibile eseguire query su questa tabella nell'editor di query nel portale di Azure, accessibile dalla pagina di database. 

1. Aprire SSMS e connettersi al server del tenant *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Per verificare che *Motorcycle Racing* e *Swimming Club* **non siano** attualmente inclusi, passare al database _contosoconcerthall_ database nel server *tenants1-dpt-&lt;user&gt;* ed eseguire una query sulla tabella *VenueTypes*.

L'esercizio prevede ora la creazione di un processo per aggiornare la tabella *VenueTypes* in tutti i database tenant per aggiungere i nuovi tipi di sede.

Per creare un nuovo processo, usare un set di stored procedure di sistema per i processi creato nel database _jobagent_ al momento della creazione dell'agente processo.

1. In SSMS connettersi al server di catalogo *catalog-dpt-&lt;user&gt;.database.windows.net* nel server 
1. In SSMS, aprire il file …\\Learning Modules\\Schema Management\\DeployReferenceData.sql
1. Modificare l'istruzione: SET @wtpUser = &lt;utente&gt; e sostituire il valore Utente usato per la distribuzione dell'app del database per tenant SaaS Wingtip Tickets
1. Verificare di essere connessi al database _jobagent_ e premere **F5** per eseguire lo script

Esaminare gli elementi seguenti nello script *DeployReferenceData.sql*:
* **sp\_add\_target\_group** crea il nome del gruppo di destinazione DemoServerGroup.
* **sp\_add\_target\_group\_member** viene usato per definire il set di database di destinazione.  Inizialmente viene aggiunto il server _tenants1-dpt-&lt;user&gt;_.  Se si aggiunge il server come destinazione, al momento dell'esecuzione del processo i database in tale server vengono inclusi nel processo stesso. A questo punto i database _basetenantdb_ e *adhocreporting* (usato in un'esercitazione successiva) vengono aggiunti come destinazione.
* **sp\_add\_job** crea un processo denominato _Reference Data Deployment_.
* **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare la tabella di riferimento VenueTypes.
* Le restanti viste nello script consentono di confermare l'esistenza degli oggetti e gestire il monitoraggio dell'esecuzione del processo. Usare queste query per esaminare il valore di stato nella colonna **lifecycle** per determinare il momento in cui l'esecuzione del processo termina in tutti i database di destinazione.

Dopo il completamento dello script, è possibile verificare se i dati di riferimento sono stati aggiornati.  In SSMS passare al database *contosoconcerthall* nel server *tenants1-dpt-&lt;user&gt;* ed eseguire una query sulla tabella *VenueTypes*.  Verificare che *Motorcycle Racing* e *Swimming Club* **siano** attualmente presenti.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creare un processo per gestire l'indice della tabella di riferimento

Questo esercizio usa un processo per ricompilare l'indice sulla chiave primaria della tabella di riferimento.  Si tratta di un'operazione di manutenzione di database tipica che può essere eseguita dopo il caricamento di grandi quantità di dati.

Creare un processo usando le stesse stored procedure di sistema per i processi.

1. Aprire SSMS e connettersi al server _catalog-dpt-&lt;user&gt;.database.windows.net_
1. Aprire il file _…\\Learning Modules\\Schema Management\\OnlineReindex.sql_
1. Fare clic con il pulsante destro del mouse, scegliere Connessione e quindi connettersi al server _catalog-dpt-&lt;user&gt;.database.windows.net_, se non si è già connessi
1. Verificare di essere connessi al database _jobagent_ e premere **F5** per eseguire lo script

Esaminare gli elementi seguenti nello script _OnlineReindex.sql_:
* **sp\_add\_job** crea un nuovo processo denominato "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885".
* **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare l'indice.
* Le viste rimaste nello script monitorano l'esecuzione del processo. Usare queste query per esaminare il valore di stato nella colonna **lifecycle** per determinare quando il processo viene completato su tutti i membri del gruppo di destinazione.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Creare un processo agente per eseguire processi T-SQL in più database
> * Aggiornare i dati di riferimento in tutti i database tenant
> * Creare un indice su una tabella in tutti i database tenant

Provare quindi a eseguire l'[esercitazione per database di report ad-hoc](saas-tenancy-cross-tenant-reporting.md) per esaminare l'esecuzione di query distribuite tra database tenant.


## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione dell'applicazione del database per tenant SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Gestione dei database cloud con scalabilità orizzontale](sql-database-elastic-jobs-overview.md)
* [Creare e gestire database SQL di Azure con scalabilità orizzontale](sql-database-elastic-jobs-create-and-manage.md)