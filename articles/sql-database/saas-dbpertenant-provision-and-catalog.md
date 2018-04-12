---
title: Effettuare il provisioning di nuovi tenant in un'app multi-tenant che usa il database SQL di Azure | Microsoft Docs
description: Informazioni su come effettuare il provisioning di nuovi tenant e catalogarli in un'app SaaS multi-tenant del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 4ddb870d0513d6834aacf0964c240260f18df0fd
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Effettuare il provisioning di nuovi tenant e registrarli nel catalogo

In questa esercitazione si apprenderà come effettuare il provisioning dei modelli SaaS e catalogarli. Si apprenderà inoltre come questi modelli sono implementati nell'applicazione SaaS di database per tenant Wingtip Tickets. L'esercitazione illustra come creare e inizializzare nuovi database tenant e come registrarli nel catalogo dei tenant dell'applicazione. Il catalogo è un database che gestisce il mapping tra i molti tenant dell'applicazione SaaS e i relativi dati. Il catalogo svolge un ruolo importante indirizzando al database corretto le richieste dell'applicazione e quelle di gestione.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Effettuare il provisioning di un nuovo tenant singolo.
> * Effettuare il provisioning di un batch di tenant aggiuntivi.


Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* È stata distribuita l'app SaaS di database per tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell è installato. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduzione al modello di catalogazione SaaS

In un'applicazione SaaS multi-tenant supportata da database è importante sapere dove sono archiviate le informazioni per ogni tenant. Nel modello di catalogo SaaS il mapping tra ogni tenant e il database in cui sono archiviati i relativi dati viene inserito all'interno di un database di catalogo. Questo modello viene applicato ogni volta che i dati del tenant vengono distribuiti tra più database.

Ogni tenant è identificato da una chiave nel catalogo, che è mappata al percorso del database. Nell'app Wingtip Tickets la chiave viene creata a partire da un hash del nome del tenant. Questo schema consente all'app di comporre la chiave usando il nome del tenant incluso nell'URL dell'applicazione. È possibile usare altri schemi di chiave del tenant.  

Il catalogo consente di modificare il nome o la posizione del database con un impatto minimo sull'applicazione. In un modello di database multi-tenant questa funzionalità consente anche di spostare un tenant da un database all'altro. Il catalogo può anche essere usato per indicare se un tenant o un database è offline per manutenzione o altre azioni. Questa funzionalità viene esaminata nell'[esercitazione relativa al ripristino di un tenant singolo](saas-dbpertenant-restore-single-tenant.md).

Il catalogo può inoltre archiviare altri metadati di tenant o database, ad esempio la versione dello schema, il piano di servizio o i contratti di servizio offerti ai tenant, nonché informazioni per la gestione delle applicazioni, il supporto tecnico o DevOps. 

Oltre all'applicazione SaaS, il catalogo può abilitare strumenti di database. Nell'esempio SaaS di database per tenant Wingtip Tickets il catalogo viene usato per abilitare le query tra tenant, come illustrato nell'[esercitazione relativa all'analisi ad-hoc](saas-tenancy-cross-tenant-reporting.md). La gestione dei processi tra database viene esaminata nelle esercitazioni relative alla [gestione dello schema](saas-tenancy-schema-management.md) e all'[analisi del tenant](saas-tenancy-tenant-analytics.md). 

Negli esempi SaaS Wingtip Tickets il catalogo viene implementato tramite le funzionalità di gestione delle partizioni della [libreria EDCL (Elastic Database Client Library, libreria client dei database elastici)](sql-database-elastic-database-client-library.md). La libreria EDCL è disponibile in Java e in .NET Framework. La libreria EDCL consente a un'applicazione di creare, gestire e usare una mappa partizioni supportata da database. 

Una mappa partizioni contiene un elenco di partizioni (database) e il mapping tra le chiavi (tenant) e le partizioni. Le funzioni della libreria EDCL vengono usate durante il provisioning dei tenant per creare le voci nella mappa partizioni. Vengono inoltre usate in fase di esecuzione dalle applicazioni per connettersi al database corretto. La libreria client dei database elastici memorizza nella cache le informazioni di connessione per ridurre al minimo il traffico verso il database del catalogo e per velocizzare l'applicazione. 

> [!IMPORTANT]
> I dati di mapping sono accessibili nel database del catalogo, ma *non devono essere modificati*. Per modificare i dati di mapping è possibile usare solo le API della libreria EDCL. Modificando direttamente i dati di mapping si corre il rischio di danneggiare il catalogo e pertanto questa modifica non è supportata.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduzione al modello di provisioning SaaS

Quando si aggiunge un nuovo tenant in un'applicazione SaaS che usa un modello di database a tenant singolo, è necessario effettuare il provisioning di un nuovo database tenant. Questo deve essere creato nella posizione e nel livello di servizio appropriati. Deve inoltre essere inizializzato con lo schema e i dati di riferimento corretti e quindi registrato nel catalogo sotto la chiave di tenant appropriata. 

Per il provisioning del database è possibile adottare approcci diversi, ad esempio eseguire script SQL, distribuire un file BACPAC o copiare un database modello. 

Il provisioning del database deve essere compreso nella strategia di gestione dello schema. È necessario assicurarsi che il provisioning di nuovi database venga effettuato con lo schema più recente. Questo requisito viene esaminato nell'[esercitazione relativa alla gestione dello schema](saas-tenancy-schema-management.md). 

L'app di database per tenant Wingtip Tickets effettua il provisioning di nuovi tenant copiando un database modello denominato _basetenantdb_, distribuito nel server di catalogo. Il provisioning può essere integrato nell'applicazione nell'ambito dell'esperienza di iscrizione. Può inoltre essere supportato offline tramite script. Questa esercitazione illustra il provisioning tramite PowerShell. 

Gli script di provisioning copiano il database _basetenantdb_ per creare un nuovo database tenant in un pool elastico e quindi inizializzano il database con informazioni specifiche del tenant e lo registrano nella mappa partizioni del catalogo. I nomi dei database tenant vengono assegnati in base al nome del tenant. Questo schema di denominazione non è una parte essenziale del modello. Poiché il catalogo associa la chiave del tenant al nome del database, è possibile usare qualsiasi convenzione di denominazione. 


## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Ottenere gli script dell'applicazione SaaS di database per tenant Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets.


## <a name="provision-and-catalog-detailed-walkthrough"></a>Procedura dettagliata per il provisioning e la catalogazione

Per comprendere come l'applicazione Wingtip Tickets implementa il provisioning di nuovi tenant, quando si effettua il provisioning di un tenant aggiungere un punto di interruzione e seguire il flusso di lavoro.

1. In PowerShell ISE aprire ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ e impostare i parametri seguenti:

   * **$TenantName** = nome della nuova sede di eventi, ad esempio *Bushwillow Blues*.
   * **$VenueType** = uno dei tipi di sede predefiniti: _blues, classicalmusic, dance, jazz, judo, motor racing, multipurpose, opera, rockmusic, soccer_.
   * **$DemoScenario** = **1**, *Provisioning di un tenant singolo*.

2. Per aggiungere un punto di interruzione, posizionare il cursore in un punto qualsiasi sulla riga contenente il testo *New-Tenant `* e quindi premere F9.

   ![Punto di interruzione](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

3. Per eseguire lo script, premere F5.

4. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere F11 per eseguire l'istruzione del codice.

   ![Debug](media/saas-dbpertenant-provision-and-catalog/debug.png)



Tracciare l'esecuzione dello script usando le opzioni del menu **Debug**. Premere F10 e F11 per eseguire un'istruzione alla volta ed esaminare singolarmente le varie funzioni chiamate. Per altre informazioni sul debug degli script di PowerShell, vedere [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Non è necessario seguire esplicitamente questo flusso di lavoro in cui viene illustrato come eseguire il debug dello script.

* **Importazione del modulo CatalogAndDatabaseManagement.psm1**. Fornisce un catalogo e un'astrazione a livello di tenant per le funzioni di [gestione delle partizioni](sql-database-elastic-scale-shard-map-management.md). Questo modulo incapsula gran parte del modello di catalogazione e vale la pena esplorarlo.
* **Importazione del modulo SubscriptionManagement.psm1**. Include le funzioni per l'accesso a Azure e per la selezione della sottoscrizione di Azure che si vuole utilizzare.
* **Recupero dei dettagli di configurazione**. Eseguire l'istruzione Get-Configuration usando F11 e vedere come è specificata la configurazione dell'app. Di seguito sono definiti i nomi delle risorse e altri valori specifici dell'app. Non modificare questi valori finché non si ha familiarità con gli script.
* **Recupero dell'oggetto catalogo**. Eseguire l'istruzione Get-Catalog, che compone e restituisce un oggetto catalogo usato nello script di livello superiore. Questa funzione usa funzioni di gestione di partizioni importate da **AzureShardManagement.psm1**. L'oggetto catalogo è composto da quanto segue:

   * Il valore $catalogServerFullyQualifiedName viene creato usando l'origine standard e il nome utente: _catalog-\<utente\>.database.windows.net_.
   * Il valore $catalogDatabaseName viene recuperato dalla configurazione: *tenantcatalog*.
   * L'oggetto $shardMapManager viene inizializzato dal database del catalogo.
   * L'oggetto $shardMap viene inizializzato dalla mappa partizioni _tenantcatalog_ nel database del catalogo. In questo modo viene composto e restituito un oggetto catalogo, che viene usato nello script di livello superiore.
* **Calcolo della chiave per il nuovo tenant**. Viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
* **Controllo dell'esistenza della chiave del tenant**. Il catalogo viene controllato per verificare che la chiave sia disponibile.
* **Provisioning del database tenant con New-TenantDatabase.** Usare F11 per eseguire le singole istruzioni e vedere come viene effettuato il provisioning del database usando un [modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Il nome del database viene costruito dal nome del tenant, in modo che sia chiaro quale partizione appartiene a quale tenant. È possibile adottare anche altre convenzioni di denominazione per il database. Un modello di Gestione risorse crea un database tenant copiando un database modello (_baseTenantDB_) nel server di catalogo. In alternativa, è possibile creare un database e inizializzarlo importando un file BACPAC oppure eseguire uno script di inizializzazione da un percorso noto.

    Il modello di Resource Manager è disponibile nella cartella …\Learning Modules\Common: *tenantdatabasecopytemplate.json*

* **Inizializzazione del database tenant**. Vengono aggiunti il nome della sede di eventi (tenant) e il tipo di sede. In questa fase è possibile eseguire anche altre operazioni di inizializzazione.

* **Registrazione del database tenant nel catalogo**. Il database viene registrato con *Add-TenantDatabaseToCatalog* usando la chiave del tenant. Usare F11 per esaminare i dettagli:

    * Il database del catalogo viene aggiunto alla mappa partizioni (elenco dei database noti).
    * Viene creato il mapping che collega il valore della chiave alla partizione.
    * Altri metadati relativi al tenant (il nome della sede di eventi) vengono aggiunti alla tabella Tenants nel catalogo. La tabella Tenants non fa parte dello schema di gestione delle partizioni e non viene installata dalla libreria EDCL. Questa tabella illustra in che modo è possibile estendere il database del catalogo perché sia in grado di supportare dati aggiuntivi specifici dell'applicazione.


Al termine del provisioning, l'esecuzione torna allo script originale *Demo-ProvisionAndCatalog* e nel browser viene visualizzata la pagina **Events** per il nuovo tenant.

   ![Pagina Events](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Eseguire il provisioning di un batch di tenant

Questo esercizio descrive come effettuare il provisioning di un batch di 17 tenant. È consigliabile effettuare il provisioning di questo batch di tenant prima di iniziare altre esercitazioni sull'applicazione SaaS di database per tenant Wingtip Tickets. Sarà così disponibile un numero maggiore di database.

1. In PowerShell ISE aprire ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1*. Modificare il parametro *$DemoScenario* impostandolo su 3:

   * **$DemoScenario** = **3** *Provisioning di un batch di tenant*.
2. Per eseguire lo script, premere F5.

Lo script distribuisce un batch di altri tenant e usa un [modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) che controlla il batch e delega il provisioning di ogni database a un modello collegato. L'uso dei modelli in questo modo consente ad Azure Resource Manager di gestire il processo di provisioning per lo script. I modelli effettuano il provisioning dei database in parallelo e, se necessario, gestiscono i tentativi. Lo script è idempotent, pertanto, se non riesce o si interrompe per qualsiasi motivo, eseguirlo nuovamente.

### <a name="verify-the-batch-of-tenants-that-successfully-deployed"></a>Verificare la corretta distribuzione del batch di tenant

* Nel [portale di Azure](https://portal.azure.com) passare all'elenco dei server e aprire il server *tenants1*. Selezionare **Database SQL** e verificare che il batch di 17 database aggiuntivi sia ora presente nell'elenco.

   ![Elenco di database](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Altri modelli di provisioning

Gli altri modelli di provisioning non inclusi in questa esercitazione comprendono:

**Pre-provisioning di database**: il modello di pre-provisioning sfrutta il fatto che i database in un pool elastico non hanno costi aggiuntivi. La fatturazione si basa sul pool elastico, non sui database. I database inattivi non utilizzano risorse. Effettuando il pre-provisioning dei database in un pool e allocandoli all'occorrenza, è possibile limitare il tempo necessario per aggiungere tenant. Il numero di database di cui viene effettuato il pre-provisioning può essere modificato in base alle esigenze, per ottenere un buffer adatto alla frequenza di provisioning prevista.

**Provisioning automatico**: in questo modello viene usato un servizio dedicato per effettuare automaticamente il provisioning di server, pool e database, in base alle esigenze. Se lo si desidera, è possibile includere il pre-provisioning di database in pool elastici. In caso di ritiro ed eliminazione di database, i vuoti nei pool elastici possono essere riempiti dal servizio di provisioning. Un servizio di questo tipo può essere semplice o complesso, come nel caso della gestione del provisioning in più aree geografiche e della configurazione della replica geografica per il ripristino di emergenza. 

Con il modello di provisioning automatico, un'applicazione client o uno script invia una richiesta di provisioning a una coda per l'elaborazione da parte del servizio di provisioning. Viene quindi eseguito il polling del servizio per stabilire quando viene completata l'operazione. Se si usa il pre-provisioning, le richieste vengono gestite rapidamente. Il servizio effettua il provisioning di un database sostitutivo in background.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Effettuare il provisioning di un nuovo tenant singolo.
> * Effettuare il provisioning di un batch di tenant aggiuntivi.
> * Eseguire in dettaglio i passaggi per effettuare il provisioning dei tenant e registrarli nel catalogo.

Provare l'[Esercitazione sul monitoraggio delle prestazioni](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Libreria client dei database elastici](sql-database-elastic-database-client-library.md)
* [Debug degli script in Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
