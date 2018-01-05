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
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Gestire lo schema per più tenant in un'applicazione multi-tenant che usa il database SQL di Azure

In questa esercitazione vengono esaminate le problematiche correlate alla gestione di un fleet potenzialmente grande di database in un Software come un'applicazione di servizio (SaaS) nel cloud. Soluzioni illustrate per gestire i miglioramenti di schema che vengono sviluppati e implementati durante il ciclo di vita di un'app.

Come qualsiasi applicazione continua a evolvere, le modifiche potrebbero verificarsi per le colonne della tabella o di altro schema o ai relativi dati di riferimento, o alle prestazioni di elementi correlati. Con un'applicazione SaaS, queste modifiche devono essere distribuite in modo coordinato in molti database tenant esistente. E queste modifiche devono essere inclusi nei database tenant future che verranno aggiunti all'app. Pertanto le modifiche anche devono essere incorporate nel processo che esegue il provisioning di nuovi database.

#### <a name="two-scenarios"></a>Due scenari

In questa esercitazione vengono illustrati i due scenari seguenti:
- Distribuire gli aggiornamenti dei dati di riferimento per tutti i tenant.
- Regolarli un indice sulla tabella che contiene i dati di riferimento.

Il [processi elastico](sql-database-elastic-jobs-overview.md) funzionalità di Database SQL di Azure viene utilizzato per eseguire queste operazioni tra database tenant. I processi di operano anche sul database tenant modello finale. Questo modello viene utilizzato quando vengono effettuato il provisioning di nuovi database.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di processo.
> * Eseguire una query in più tenant.
> * Aggiornare i dati in tutti i database tenant.
> * Creare un indice in una tabella in tutti i database tenant.

## <a name="prerequisites"></a>Prerequisiti

- L'app ticket Wingtip deve già essere distribuita:
    - Per istruzioni, vedere la prima esercitazione, cui viene presentata la *Wingtip ticket* SaaS multi-tenant database app:<br />[Distribuire ed esplorare un'applicazione multi-tenant partizionata che utilizza il Database di SQL Azure](saas-multitenantdb-get-started-deploy.md).
        - Il processo di distribuzione viene eseguito per meno di cinque minuti.
    - È necessario disporre di *partizionati multi-tenant* versione Wingtip installato. Le versioni per *autonomo* e *Database per ogni tenant* non supportano l'esercitazione presenta.

- Installare la versione più recente di SQL Server Management Studio (SSMS). [Scaricare e installare SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- È necessario installare Azure PowerShell. Per informazioni dettagliate, vedere [Introduzione a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> In questa esercitazione Usa funzionalità del servizio di Database SQL di Azure che sono in anteprima una limitato ([i processi di Database elastico](sql-database-elastic-database-client-library.md)). Se si desidera eseguire questa esercitazione, specificare l'ID sottoscrizione in  *SaaSFeedback@microsoft.com*  con soggetto = elastico anteprima dei processi. Dopo aver ricevuto conferma che la sottoscrizione è stata abilitata, [scaricare e installare i cmdlet più recenti della versione preliminare](https://github.com/jaredmoo/azure-powershell/releases). Questa versione di anteprima è limitato, quindi contattare  *SaaSFeedback@microsoft.com*  per domande correlate o il supporto.

## <a name="introduction-to-saas-schema-management-patterns"></a>Introduzione ai modelli di gestione dello schema SaaS

Il modello di database multi-tenant partizionato utilizzato in questo esempio consente a un database tenant contenere uno o più tenant. In questo esempio viene esaminata la possibilità di utilizzare una combinazione di molti tenant e tenant di un database, abilitazione di un *ibrida* il modello di gestione di tenant. La gestione di questi database è complessa. Il servizio [Processi elastici](sql-database-elastic-jobs-overview.md) facilita l'amministrazione e la gestione del livello dati SQL. I processi consentono di eseguire script Transact-SQL in modo sicuro e affidabile come attività, rispetto a un gruppo di database tenant. Le attività sono indipendenti dall'interazione dell'utente o input. Questo metodo può essere utilizzato per distribuire le modifiche allo schema o ai dati di riferimento comuni, tra tutti i tenant in un'applicazione. Processi elastici consente anche di conservare una copia del modello finale del database. Il modello viene utilizzato per creare nuovi tenant, garantire sempre lo schema più recente e dati di riferimento sono in uso.

![schermata](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Anteprima limitata del servizio Processi elastici

C'è una nuova versione di processi elastico che è ora una funzionalità integrata di Database SQL di Azure. Per integrata si intende che non richiede altri servizi o componenti. Questa nuova versione del servizio Processi elastici è attualmente in anteprima limitata. L'anteprima limitato supporta attualmente PowerShell per creare gli account di processo e T-SQL per creare e gestire i processi.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Ottenere gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets

Gli script di Database di Wingtip ticket SaaS multi-tenant e un codice sorgente dell'applicazione sono disponibili nel [WingtipTicketsSaaS MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repository in Github. Vedere il [indicazioni generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi scaricare e sbloccare gli script Wingtip ticket SaaS. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Creare un database di account per processi e un nuovo account per processi

Questa esercitazione richiede l'uso di PowerShell per creare il database di account di processo e l'account del processo. Ad esempio il database MSDB è utilizzato da SQL Agent, processi elastico Usa un database SQL di Azure per archiviare le definizioni di processo, lo stato del processo e la cronologia. Dopo aver creato l'account di processo, è possibile creare e monitorare i processi immediatamente.

1. In **PowerShell ISE**aprire *... \\Moduli di formazione\\la gestione dello Schema\\Demo SchemaManagement.ps1*.
2. Premere **F5** per eseguire lo script.

Il *Demo SchemaManagement.ps1* lo script viene chiamato il *Distribuisci SchemaManagement.ps1* script per creare un livello *S2* database denominato **jobaccount** nel server di catalogo. Lo script crea quindi l'account di processo, passando il database jobaccount come parametro per la chiamata di creazione di account di processo.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Creare un processo per distribuire nuovi dati di riferimento a tutti i tenant

#### <a name="prepare"></a>Preparazione

Ogni database tenant include un set di tipi di eventi di **VenueTypes** tabella. I tipi di eventi definiscono il tipo di eventi vengono ospitati in un luogo. In questo esercizio, si distribuisce un aggiornamento a tutti i database per aggiungere due tipi di eventi aggiuntivi: *moto NASCAR* e *dei Club*. Questi tipi di sedi di eventi corrispondono all'immagine di sfondo visualizzata nell'app degli eventi del tenant.

Prima di distribuire i nuovi dati di riferimento, annotare il numero di tipi di eventi che esiste già, che potrebbe essere 10. Notare facendo clic sul seguente collegamento per l'interfaccia utente web Wingtip e quindi scegliendo il **il tipo di evento** dal menu a discesa:
- http://Events.Wingtip-MT.<USER>. trafficmanager.net

È ora possibile contare il numero di tipi di evento originale. In particolare, si noti che né *moto NASCAR* né *dei Club* esiste ancora.

#### <a name="steps"></a>Passaggi

Ora si crea un processo per aggiornare il **VenueTypes** tabella in ogni database tenant, aggiungendo i due nuovi tipi di eventi.

Per creare un nuovo processo, utilizzare il set di processi stored procedure di sistema che sono stati creati nel *jobaccount* database. Le procedure sono state create quando è stato creato l'account del processo.

1. In SSMS, connettersi al server tenant: tenants1-mt-\<utente\>.database.windows.net

2. Individuare il *tenants1* database il *tenants1-mt -\<utente\>. database.windows.net* server.

3. Query di *VenueTypes* tabella per assicurarsi che *moto NASCAR* e *dei Club* non sono ancora nell'elenco dei risultati.

4. Connettersi al server di catalogo, ovvero *catalogo-mt -\<utente\>. database.windows.net*.

5. Connettere il *jobaccount* database nel server di catalogo.

6. In SQL Server Management Studio, aprire il file *... \\Moduli di formazione\\la gestione dello Schema\\DeployReferenceData.sql*.

7. Modificare l'istruzione: set @User = &lt;utente&gt; e sostituire il valore Utente usato per la distribuzione dell'applicazione del database multi-tenant SaaS Wingtip Tickets.

8. Premere **F5** per eseguire lo script.

#### <a name="observe"></a>Osservazione

Osservare i seguenti elementi di *DeployReferenceData.sql* script:

- **SP\_aggiungere\_destinazione\_gruppo** crea il nome del gruppo di destinazione *DemoServerGroup*, e l'aggiunta di membri di destinazione al gruppo.

- **SP\_aggiungere\_destinazione\_gruppo\_membro** aggiunge gli elementi seguenti:
    - Oggetto *server* il tipo di membro di destinazione.
        - Si tratta di *tenants1-mt -&lt;utente&gt;*  server che contiene i database tenant.
        - In questo modo tutti i database nel server sono inclusi nel processo quando viene eseguito il processo.
    - Oggetto *database* il tipo di membro per il database finale di destinazione (*basetenantdb*) che si trovi in *catalogo-mt -&lt;utente&gt;*  server,
    - Oggetto *database* il tipo di membro da includere come destinazione il *adhocreporting* database utilizzato in un'esercitazione successiva.

- **SP\_aggiungere\_processo** crea un processo denominato *distribuzione dati di riferimento*.

- **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare la tabella di riferimento VenueTypes.

- Le restanti viste nello script consentono di confermare l'esistenza degli oggetti e gestire il monitoraggio dell'esecuzione del processo. Utilizzare queste query per esaminare il valore di stato di **ciclo di vita** colonna per determinare quando il processo è stata completata correttamente. Il processo aggiorna il database tenant e aggiorna i due database aggiuntivi che contengono la tabella di riferimento.

In SQL Server Management Studio, passare al database tenant nel *tenants1-mt -&lt;utente&gt;*  server. Query di *VenueTypes* tabella per assicurarsi che *moto NASCAR* e *dei Club* ora vengono aggiunte alla tabella. Il conteggio totale dei tipi di eventi deve aumentare di due.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Creare un processo per gestire l'indice della tabella di riferimento

Questo esercizio è simile all'esercizio precedente. Questo esercizio viene creato un processo per ricompilare l'indice sulla chiave primaria della tabella di riferimento. Ricompilazione di un indice è un'operazione di gestione di database tipico che un amministratore potrebbe essere eseguito dopo un carico di dati di grandi dimensioni in una tabella, per migliorare le prestazioni.

1. In SSMS, connettersi a *jobaccount* database *catalogo-mt -&lt;utente&gt;. database.windows.net* server.

2. In SQL Server Management Studio, aprire *... \\Moduli di formazione\\la gestione dello Schema\\OnlineReindex.sql*.

3. Premere **F5** per eseguire lo script.

#### <a name="observe"></a>Osservazione

Osservare i seguenti elementi di *OnlineReindex.sql* script:

* **SP\_aggiungere\_processo** crea un nuovo processo denominato *Reindex Online PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **sp\_add\_jobstep** crea il passaggio del processo contenente il testo del comando T-SQL per aggiornare l'indice.

* Le viste rimaste nello script monitorano l'esecuzione del processo. Utilizzare queste query per esaminare il valore di stato di **ciclo di vita** colonna per determinare quando il processo è stata completata correttamente in tutti i membri del gruppo di destinazione.

## <a name="additional-resources"></a>Risorse aggiuntive

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Gestione dei database cloud con scalabilità orizzontale](sql-database-elastic-jobs-overview.md)
* [Creare e gestire database SQL di Azure con scalabilità orizzontale](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> - Creare un account di processo per eseguire una query in più tenant.
> - Aggiornare i dati in tutti i database tenant.
> - Creare un indice in una tabella in tutti i database tenant.

Successivamente, eseguire l'[esercitazione sul reporting ad hoc](saas-multitenantdb-adhoc-reporting.md).

