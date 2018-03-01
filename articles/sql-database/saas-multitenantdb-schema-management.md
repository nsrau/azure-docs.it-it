---
title: Gestire lo schema del database SQL di Azure in un'app multi-tenant | Microsoft Docs
description: "Gestire lo schema per più tenant in un'applicazione multi-tenant che usa il database SQL di Azure"
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 0303da917ecb03ca27e0444afb56f49766b70029
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Gestire lo schema in un'applicazione SaaS che usa più database SQL multi-tenant

Questa esercitazione esamina le problematiche correlate alla gestione di un insieme di database in un'applicazione SaaS (software come un servizio). Vengono illustrate soluzioni per il fan-out delle modifiche allo schema nell'insieme di database.

Come qualsiasi altra applicazione, l'app SaaS Wingtip Tickets evolverà nel tempo e saranno necessarie modifiche al database. Le modifiche possono influire sui dati di schemi o di riferimento o applicare attività di manutenzione database. Con un'applicazione SaaS che usa un modello di un database per ogni tenant, le modifiche devono essere coordinate in un insieme potenzialmente grande di database tenant. Inoltre, è necessario incorporare queste modifiche nel processo di provisioning del database per assicurare che siano incluse nei nuovi database al momento della creazione.

#### <a name="two-scenarios"></a>Due scenari

Questa esercitazione illustra i due scenari seguenti:
- Distribuire gli aggiornamenti dei dati di riferimento per tutti i tenant.
- Ricompilare un indice nella tabella contenente i dati di riferimento.

La funzionalità [Processi elastici](sql-database-elastic-jobs-overview.md) del database SQL di Azure viene usata per eseguire queste operazioni tra i database tenant. I processi funzionano anche sul database tenant "modello". Nell'app di esempio Wingtip Tickets il database modello viene copiato per effettuare il provisioning di un nuovo database tenant.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un agente processo.
> * Eseguire una query T-SQL su più database tenant.
> * Aggiornare i dati di riferimento in tutti i database tenant.
> * Creare un indice su una tabella in tutti i database tenant.

## <a name="prerequisites"></a>prerequisiti

- L'app SaaS Wingtip Tickets per database multi-tenant deve essere già stata distribuita:
    - Per istruzioni, vedere la prima esercitazione, in cui viene presentata l'app SaaS per database multi-tenant Wingtip Tickets:<br />[Distribuire ed esplorare un'applicazione di database multi-tenant partizionato che usa il database SQL di Azure](saas-multitenantdb-get-started-deploy.md).
        - Il processo di distribuzione viene eseguito in meno di cinque minuti.
    - È necessario disporre di una versione *multi-tenant partizionato* di Wingtip installata. Le versioni per *autonoma* e *database per tenant* non supportano questa esercitazione.

- La versione più recente di SQL Server Management Studio (SSMS) deve essere installata. [Scaricare e installare SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell deve essere installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Questa esercitazione usa funzionalità del servizio database SQL di Azure incluse in un'anteprima limitata ([processi di database elastico](sql-database-elastic-database-client-library.md)). Per eseguire questa esercitazione, fornire l'ID della sottoscrizione a *SaaSFeedback@microsoft.com* indicando nell'oggetto del messaggio "Elastic Jobs Preview". Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, [scaricare e installare i cmdlet più recenti della versione preliminare](https://github.com/jaredmoo/azure-powershell/releases). Questa versione di anteprima è limitata. Per eventuali domande o richieste di supporto, contattare *SaaSFeedback@microsoft.com*.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduzione ai modelli di gestione dello schema SaaS

Il modello di database multi-tenant partizionato usato in questo esempio consente a un database tenant di contenere uno o più tenant. Questo esempio illustra la possibilità di usare una combinazione di database per più tenant e a tenant singolo, che abilita un modello *ibrido* di gestione del tenant. La gestione delle modifiche a questi database può essere complessa. Il servizio [Processi elastici](sql-database-elastic-jobs-overview.md) facilita l'amministrazione e la gestione di un numero elevato di database. I processi consentono di eseguire script Transact-SQL in modo sicuro e affidabile come attività, rispetto a un gruppo di database tenant. Le attività sono indipendenti dall'interazione o dall'input dell'utente. Questo metodo può essere usato per distribuire modifiche dello schema e dei dati di riferimento comuni a tutti i tenant in un'applicazione. Il servizio Processi elastici possono essere usati anche per conservare una copia del modello finale del database. Il modello viene usato per creare nuovi tenant, assicurando che siano sempre in uso lo schema e i dati di riferimento più recenti.

![schermata](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Anteprima limitata del servizio Processi elastici

È disponibile una nuova versione del servizio Processi elastici che è ora una funzionalità integrata di database SQL di Azure. Questa nuova versione del servizio Processi elastici è attualmente in anteprima limitata. L'anteprima limitata supporta attualmente l'utilizzo di PowerShell per creare un agente processo e T-SQL per creare e gestire i processi.
> [!NOTE] 
> Questa esercitazione usa funzionalità del servizio database SQL incluse in un'anteprima limitata (processi di database elastico). Per eseguire questa esercitazione, fornire l'ID della sottoscrizione a SaaSFeedback@microsoft.com indicando nell'oggetto del messaggio "Elastic Jobs Preview". Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, scaricare e installare i cmdlet più recenti della versione preliminare. Questa versione di anteprima è limitata. Per eventuali domande o richieste di supporto, contattare SaaSFeedback@microsoft.com.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Ottenere gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets

Gli script del database multi-tenant SaaS Wingtip Tickets e un codice sorgente dell'applicazione sono disponibili nel repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) su GitHub. Vedere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets. 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Creare un database di agenti processo e un nuovo agente processo

Per questa esercitazione è necessario usare PowerShell per creare il database di agenti processo e l'agente processo. Come il database MSDB usato da SQL Agent, l'agente processo usa un database SQL di Azure per archiviare le definizioni dei processi, lo stato dei processi e la cronologia. Dopo aver creato l'agente processo, è possibile creare e monitorare i processi immediatamente.

1. In **PowerShell ISE** aprire *...\\Learning Modules (Moduli di apprendimento)\\Schema Management (Gestione schema)\\Demo-SchemaManagement.ps1*.
2. Premere **F5** per eseguire lo script.

Lo script *Demo-SchemaManagement.ps1* chiama lo script *Deploy-SchemaManagement.ps1* per creare un database denominato _jobagent_ nel server di catalogo. Lo script crea quindi l'agente processo, passando il database _jobagent_ come parametro.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creare un processo per distribuire nuovi dati di riferimento a tutti i tenant

#### <a name="prepare"></a>Preparazione

Ogni database del tenant include un set di tipi di sedi nella tabella **VenueTypes**. Ogni tipo di sede definisce il tipo di eventi ospitati in una sede. Questi tipi di sedi corrispondono alle immagini di sfondo visualizzate nell'app degli eventi del tenant.  Questo esercizio illustra come distribuire un aggiornamento a tutti i database per aggiungere due tipi di eventi aggiuntivi: *Motorcycle Racing* (Gare motociclistiche) e *Swimming Club* (Club nuoto). 

Esaminare prima i tipi di sede inclusi in ogni database tenant. Connettersi a uno dei database tenant in SQL Server Management Studio (SSMS) ed esaminare la tabella VenueTypes.  È anche possibile eseguire una query su questa tabella nell'editor di query nel portale di Azure, accessibile dalla pagina di database. 

1. Aprire SSMS e connettersi al server del tenant *tenants1-dpt-&lt;utente&gt;.database.windows.net*.
1. Per verificare che *Motorcycle Racing* e *Swimming Club*  **non siano** attualmente inclusi, passare al database *contosoconcerthall* nel server *tenants1-dpt-&lt;utente&gt;* ed eseguire una query sulla tabella *VenueTypes*.



#### <a name="steps"></a>Passaggi

L'esercizio prevede ora la creazione di un processo per aggiornare la tabella **VenueTypes** in ogni database tenant, aggiungendo i due nuovi tipi di eventi.

Per creare un nuovo processo, usare il set di stored procedure di sistema dei processi create nel database _jobagent_. Le stored procedure sono state create quando è stato creato l'agente processo.

1. In SSMS, connettersi al server tenant: tenants1-mt-&lt;utente&gt;.database.windows.net

2. Individuare il database *tenants1*.

3. Eseguire una query nella tabella *VenueTypes* per verificare che né *Motorcycle Racing* (Gare motociclistiche) né *Swimming Club* (Club nuoto) siano ancora nell'elenco dei risultati.

4. Connettersi al server di catalogo, *catalog-mt-&lt;user&gt;.database.windows.net*.

5. Connettersi al database _jobagent_ nel server di catalogo.

6. In SSMS, aprire il file *...\\Learning Modules\\Schema Management\\DeployReferenceData.sql*.

7. Modificare l'istruzione: set @User = &lt;utente&gt; e sostituire il valore Utente usato per la distribuzione dell'applicazione del database multi-tenant SaaS Wingtip Tickets.

8. Premere **F5** per eseguire lo script.

#### <a name="observe"></a>Osservazione

Osservare gli elementi seguenti nello script *DeployReferenceData.sql*:

- **sp\_add\_target\_group** crea il nome del gruppo di destinazione *DemoServerGroup* e aggiunge i membri di destinazione al gruppo.

- **sp\_add\_target\_group\_member** aggiunge gli elementi seguenti:
    - Un tipo di membro di destinazione del *server*.
        - Si tratta del server *tenants1-mt-&lt;user&gt;* che contiene i database tenant.
        - Includere il server include i database tenant esistenti in fase di esecuzione del processo.
    - Un tipo di membro di destinazione del *database* per il database modello (*basetenantdb*) presente nel server *catalog-mt-&lt;user&gt;*,
    - Un tipo di membro di destinazione del *database* per includere il database *adhocreporting* usati in un'esercitazione successiva.

- **sp\_add\_job** crea un processo denominato *Reference Data Deployment* (Distribuzione dati di riferimento).

- **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare la tabella di riferimento VenueTypes.

- Le restanti viste nello script consentono di confermare l'esistenza degli oggetti e gestire il monitoraggio dell'esecuzione del processo. Usare queste query per esaminare il valore di stato nella colonna **lifecycle** per determinare quando il processo è stato completato. Il processo aggiorna il database tenant e aggiorna i due database aggiuntivi che contengono la tabella di riferimento.

In SSMS, passare al database tenant nel server *tenants1-mt-&lt;user&gt;*. Eseguire una query nella tabella *VenueTypes* per verificare che *Motorcycle Racing* (Gare motociclistiche) e *Swimming Club* (Club nuoto) siano ora aggiunti alla tabella. Il conteggio totale dei tipi di eventi deve essere aumentato di due.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creare un processo per gestire l'indice della tabella di riferimento

In questo esercizio viene creato un processo per ricompilare l'indice sulla chiave primaria della tabella di riferimento su tutti i database tenant. La ricompilazione di un indice è una tipica operazione di gestione del database che può essere eseguita da un amministratore dopo un carico di dati di grandi dimensioni, per migliorare le prestazioni.

1. In SSMS connettersi al database _jobagent_ nel server *catalog-mt-&lt;utente&gt;.database.windows.net*.

2. In SSMS aprire il file *...\\Learning Modules\\Schema Management\\OnlineReindex.sql*.

3. Premere **F5** per eseguire lo script.

#### <a name="observe"></a>Osservazione

Nello script *OnlineReindex.sql* osservare gli elementi seguenti:

* **sp\_add\_job** crea un nuovo processo denominato *Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare l'indice.

* Le viste rimaste nello script monitorano l'esecuzione del processo. Usare queste query per esaminare il valore di stato nella colonna **lifecycle** per determinare quando il processo viene completato su tutti i membri del gruppo di destinazione.

## <a name="additional-resources"></a>Risorse aggiuntive

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Gestione dei database cloud con scalabilità orizzontale](sql-database-elastic-jobs-overview.md)
* [Creare e gestire database SQL di Azure con scalabilità orizzontale](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
.
> * Creare un agente processo per eseguire i processi T-SQL in più database
> * Aggiornare i dati di riferimento in tutti i database tenant.
> * Creare un indice su una tabella in tutti i database tenant

Successivamente, eseguire l'esercitazione sul [reporting ad-hoc] (saas-multitenantdb-ad hoc-reporting.md) per esplorare l'esecuzione di query distribuite tra i database tenant.

