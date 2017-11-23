---
title: Eseguire il provisioning di nuovi tenant in un'app multi-tenant che usa il database SQL di Azure | Microsoft Docs
description: Informazioni su come effettuare il provisioning di nuovi tenant e catalogarli in un'app SaaS multi-tenant del database SQL di Azure
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
ms.date: 08/11/2017
ms.author: sstein
ms.openlocfilehash: 17eb9b3ff059912e4fe3fafda0b9c435e3983888
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2017
---
# <a name="learn-how-to-provision-new-tenants-and-register-them-in-the-catalog"></a>Effettuare il provisioning di nuovi tenant e registrarli nel catalogo

Questa esercitazione illustra i modelli SaaS di provisioning e catalogazione e il modo in cui questi modelli vengono implementati nell'applicazione SaaS di database per tenant Wingtip Tickets. Verranno creati e inizializzati nuovi database tenant, che verranno registrati nel catalogo dei tenant dell'applicazione. Il catalogo è un database che gestisce il mapping tra i molti tenant dell'applicazione SaaS e i relativi dati. Il catalogo svolge un ruolo importante indirizzando al database corretto le richieste dell'applicazione e quelle di gestione.  

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Eseguire il provisioning di un nuovo tenant singolo, con i passaggi dettagliati della relativa implementazione
> * Effettuare il provisioning di un batch di altri tenant


Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* È stata distribuita l'app SaaS di database per tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduzione al modello di catalogazione SaaS

In un'applicazione SaaS multi-tenant supportata da database è importante sapere dove sono archiviate le informazioni per ogni tenant. Nel modello di catalogo SaaS il mapping tra ogni tenant e il database in cui sono archiviati i relativi dati viene inserito all'interno di un database di catalogo. L'app SaaS di database per tenant Wingtip Tickets usa un modello di database a tenant singolo, ma il modello di base che prevede l'archiviazione del mapping tenant-database in un catalogo viene applicato ogni volta che i dati di tenant sono distribuiti tra più database, indipendentemente dal fatto che venga usato un database multi-tenant o a tenant singolo.

A ogni tenant viene assegnata una chiave che lo identifica all'interno del catalogo. Ogni chiave è mappata alla posizione del database appropriato. Nell'app SaaS Wingtip Tickets la chiave viene creata a partire da un hash del nome del tenant. Ciò consente di comporre la chiave usando la parte dell'URL dell'applicazione relativa al nome del tenant. Per la chiave del tenant è possibile usare anche altri schemi.  

Il catalogo consente di modificare il nome o la posizione del database con un impatto minimo sull'applicazione.  In un modello di database multi-tenant, ciò consente anche di "spostare" un tenant da un database a un altro.  Il catalogo può essere usato anche per indicare se un tenant o un database è offline per manutenzione o altre azioni. Questo aspetto viene esaminato nell'[esercitazione relativa al ripristino di un tenant singolo](saas-dbpertenant-restore-single-tenant.md).

Il catalogo, che in effetti è un database di gestione per un'applicazione SaaS, può anche archiviare metadati aggiuntivi di un tenant o di un database, ad esempio il livello o l'edizione del database, la versione dello schema, il piano di servizio o i contratti di servizio offerti ai tenant, oltre ad altre informazioni che consentono la gestione delle applicazioni, l'assistenza clienti o i processi DevOps.  

Oltre all'applicazione SaaS, il catalogo può abilitare strumenti di database.  Nell'esempio SaaS di database per tenant Wingtip Tickets il catalogo viene usato per abilitare le query tra tenant, prese in esame nell'[esercitazione relativa all'analisi ad-hoc](saas-tenancy-adhoc-analytics.md). La gestione dei processi tra database viene esaminata nelle esercitazioni relative alla [gestione dello schema](saas-tenancy-schema-management.md) e all'[analisi del tenant](saas-tenancy-tenant-analytics.md). 

Negli esempi SaaS Wingtip Tickets il catalogo viene implementato tramite le funzionalità di gestione delle partizioni della [libreria EDCL (Elastic Database Client Library, libreria client dei database elastici)](sql-database-elastic-database-client-library.md). La libreria EDCL è disponibile in Java e .Net Framework. La libreria EDCL consente a un'applicazione di creare, gestire e usare una mappa partizioni supportata da database. Una mappa partizioni contiene un elenco di partizioni (database) e il mapping tra le chiavi (tenant) e le partizioni.  È possibile usare le funzioni della libreria EDCL da applicazioni o script di PowerShell durante il provisioning di tenant per creare le voci della mappa partizioni e da applicazioni per connettersi al database corretto in modo efficiente. La libreria EDCL memorizza nella cache le informazioni di connessione per ridurre al minimo il traffico verso il database del catalogo e per velocizzare l'applicazione.  

> [!IMPORTANT]
> I dati di mapping sono accessibili nel database del catalogo, ma *non modificarli*. Modificare i dati di mapping solo tramite le API della libreria client dei database elastici. La modifica diretta dei dati di mapping comporta il rischio di danneggiare il catalogo e non è supportata.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Introduzione al modello di provisioning SaaS

Al momento del caricamento di un nuovo tenant in un'applicazione SaaS che usa un modello di database a tenant singolo, è necessario eseguire il provisioning di un nuovo database tenant.  Questo deve essere creato nella posizione e nel livello di servizio appropriati, inizializzato con lo schema e i dati di riferimento corretti e quindi registrato nel catalogo sotto la chiave tenant appropriata.  

Per il provisioning del database è possibile adottare approcci diversi, ad esempio l'esecuzione di script SQL, la distribuzione di un file BACPAC o la copia di un database modello.  

L'approccio usato per il provisioning deve essere compreso nella strategia generale di gestione dello schema, che deve garantire che il provisioning di nuovi database venga effettuato con lo schema più recente.  Questo aspetto viene esaminato nell'[esercitazione relativa alla gestione dello schema](saas-tenancy-schema-management.md).  

L'app SaaS di database per tenant Wingtip Tickets effettua il provisioning di nuovi tenant copiando un database modello denominato _basetenantdb_, distribuito nel server di catalogo.  Il provisioning può essere integrato nell'applicazione nell'ambito dell'esperienza di iscrizione e/o può essere supportato offline tramite script. Questa esercitazione illustra il provisioning tramite PowerShell. Gli script di provisioning copiano il database basetenantdb per creare un nuovo database tenant in un pool elastico e quindi lo inizializzano con informazioni specifiche del tenant e lo registrano nella mappa partizioni del catalogo.  Nell'app SaaS di database per tenant Wingtip Tickets, i nomi dei database vengono assegnati in base al tenant, ma questo non è un aspetto essenziale del modello. L'uso del catalogo, infatti, consente di assegnare qualsiasi nome al database. 


## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script e il codice sorgente dell'applicazione SaaS Wingtip sono disponibili nel repository [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) di GitHub. [Procedura per scaricare gli script dell'applicazione SaaS Wingtip Tickets](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts).


## <a name="provision-and-catalog-detailed-walkthrough"></a>Procedura dettagliata per il provisioning e la catalogazione

Per comprendere in che modo l'applicazione Wingtip implementa il nuovo provisioning di tenant, eseguire il provisioning di un tenant procedendo passo per passo attraverso il flusso di lavoro dopo aver aggiunto un punto di interruzione:

1. In _PowerShell ISE_, aprire ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ e impostare i parametri seguenti:
   * **$TenantName** = nome della nuova sede di eventi, ad esempio *Bushwillow Blues*.
   * **$VenueType** = uno dei tipi di sede predefiniti: *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = **1**, per *effettuare il provisioning di un tenant singolo*.

1. Aggiungere un punto di interruzione posizionando il cursore in un punto qualsiasi nella riga 48 contenente il testo: *New-Tenant `* e premere **F9**.

   ![punto di interruzione](media/saas-dbpertenant-provision-and-catalog/breakpoint.png)

1. Premere **F5** per eseguire lo script.

1. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.

   ![debug](media/saas-dbpertenant-provision-and-catalog/debug.png)



Tenere traccia dell'esecuzione dello script usando le opzioni del menu **Debug** **F10** e **F11** per eseguire le istruzioni delle funzioni chiamate. Per altre informazioni sul debug degli script di PowerShell, vedere [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


I seguenti non sono passaggi da usare esplicitamente, ma solo una spiegazione del flusso di lavoro seguito durante il debug dello script:

1. **Importazione del modulo SubscriptionManagement.psm1** che contiene funzioni per l'accesso ad Azure e la selezione della sottoscrizione di Azure da usare.
1. **Importazione del modulo CatalogAndDatabaseManagement.psm1** che fornisce un catalogo e l'astrazione a livello di tenant per le funzioni di [gestione delle partizioni](sql-database-elastic-scale-shard-map-management.md). Questo è un modulo importante che incapsula gran parte del modello di catalogazione e vale la pena esplorare.
1. **Recupero dei dettagli di configurazione**. Eseguire l'istruzione Get-Configuration (con F11) e vedere come viene specificata la configurazione dell'app. In questa posizione sono definiti i nomi delle risorse e altri valori specifici dell'app, ma non modificare questi valori fino a quando non si ha familiarità con gli script.
1. **Recupero dell'oggetto catalogo**. Eseguire l'istruzione Get-Catalog, che compone e restituisce l'oggetto catalogo usato nello script di livello superiore.  Questa funzione usa funzioni di gestione di partizioni importate da **AzureShardManagement.psm1**. L'oggetto catalogo è composto da quanto segue:
   * $catalogServerFullyQualifiedName viene costruito usando l'origine standard e il nome utente: _catalog-\<utente\>.database.windows.net_.
   * Il valore $catalogDatabaseName viene recuperato dalla configurazione: *tenantcatalog*.
   * L'oggetto $shardMapManager viene inizializzato dal database del catalogo.
   * L'oggetto $shardMap viene inizializzato dalla mappa partizioni *tenantcatalog* nel database del catalogo.
   Viene composto e restituito un oggetto catalogo, usato nello script di livello superiore.
1. **Calcolo della chiave per il nuovo tenant**. Viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
1. **Controllo dell'esistenza della chiave del tenant**. Il catalogo viene controllato per verificare che la chiave sia disponibile.
1. **Provisioning del database tenant con New-TenantDatabase.** Usare **F11** per eseguire le singole istruzioni e vedere come viene effettuato il provisioning del database con un [modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

    Il nome del database viene costruito dal nome del tenant, in modo che sia chiaro quale partizione appartiene a quale tenant. (È possibile usare tranquillamente altre strategie per la denominazione dei database.) Viene usato un modello di Resource Manager per creare un database tenant copiando un database modello (baseTenantDB) nel server di catalogo. Un approccio alternativo è costituito dalla creazione di un database vuoto, con la successiva inizializzazione tramite importazione di un file BACPAC, o dall'esecuzione di uno script di inizializzazione da una posizione nota.  

    Il modello di Resource Manager è disponibile nella cartella …\Learning Modules\Common: *tenantdatabasecopytemplate.json*

1. Dopo la creazione, il database tenant viene **inizializzato con il nome e il tipo della sede di eventi (tenant)**. In questa posizione è possibile eseguire anche altre inizializzazioni.

1. Il **database tenant viene registrato nel catalogo** con *Add-TenantDatabaseToCatalog* usando la chiave del tenant. Usare **F11** per esaminare i dettagli:

    * Il database del catalogo viene aggiunto alla mappa partizioni (elenco dei database noti).
    * Viene creato il mapping che collega il valore della chiave alla partizione.
    * Altri metadati relativi al tenant (il nome della sede di eventi) vengono aggiunti alla tabella Tenants nel catalogo.  La tabella Tenants non fa parte dello schema ShardManagement e non viene installata dalla libreria EDCL.  Questa tabella illustra in che modo è possibile estendere il database del catalogo perché sia in grado di supportare dati aggiuntivi specifici dell'applicazione.   


Dopo aver completato il provisioning, l'esecuzione torna allo script originale *Demo-ProvisionAndCatalog*, che apre nel browser la pagina degli **eventi** per il nuovo tenant:

   ![eventi](media/saas-dbpertenant-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Eseguire il provisioning di un batch di tenant

Questo esercizio descrive come effettuare il provisioning di un batch di 17 tenant. È consigliabile effettuare il provisioning di questo batch di tenant prima di iniziare le altre esercitazioni sull'applicazione SaaS di database per tenant Wingtip Tickets, in modo da avere più database da usare.

1. In *PowerShell ISE* aprire ...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* e impostare il parametro *$DemoScenario* su 3:
   * **$DemoScenario** = **3** per *effettuare il provisioning di un batch di tenant*.
1. Premere **F5** per eseguire lo script.

Lo script distribuisce un batch di altri tenant e usa [modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) che controlla il batch, quindi delega il provisioning di ogni database a un modello collegato. L'uso dei modelli in questo modo consente ad Azure Resource Manager di gestire il processo di provisioning per lo script. I modelli effettuano il provisioning dei database in parallelo, quando possibile, e gestiscono la ripetizione dei tentativi se necessario, ottimizzando il processo nel suo complesso. Lo script è idempotent, pertanto, se non riesce o si interrompe per qualsiasi motivo, eseguirlo nuovamente.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificare la corretta distribuzione del batch di tenant

* Nel [portale di Azure](https://portal.azure.com) passare all'elenco dei server e aprire il server *tenants1*. Fare clic su **Database SQL** e verificare che il batch di 17 database aggiuntivi sia ora presente nell'elenco:

   ![elenco di database](media/saas-dbpertenant-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Altri modelli di provisioning

Gli altri modelli di provisioning non inclusi in questa esercitazione includono:

**Pre-provisioning di database.** Il modello di pre-provisioning sfrutta il fatto che i database in un pool elastico non hanno costi aggiuntivi. La fatturazione si basa sul pool elastico, non sui database e i database inattivi non consumano risorse. Effettuando il pre-provisioning dei database in un pool e allocandoli poi all'occorrenza, il tempo di caricamento dei tenant può essere ridotto significativamente. Il numero di database di cui viene effettuato il pre-provisioning può essere modificato in base alle esigenze, per ottenere un buffer adatto alla frequenza di provisioning prevista.

**Provisioning automatico.** In questo modello viene usato un servizio di provisioning dedicato che effettua il provisioning di server, pool e database automaticamente in base alle esigenze, incluso il pre-provisioning di database in pool elastici, se lo si desidera. In caso di ritiro ed eliminazione di database, inoltre, i vuoti nei pool elastici possono essere riempiti dal servizio di provisioning. Un servizio di questo tipo può essere semplice o complesso, come nel caso della gestione del provisioning in più aree geografiche, e può includere la configurazione della replica geografica per il ripristino di emergenza. Con il modello di provisioning automatico, uno script o un'applicazione client invia una richiesta di provisioning a una coda per l'elaborazione da parte del servizio di provisioning e quindi esegue il polling del servizio per stabilire quando viene completata l'operazione. Se si usa il pre-provisioning, le richieste vengono gestite rapidamente ed è il servizio a effettuare il provisioning di un database sostitutivo in background.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Effettuare il provisioning di un nuovo tenant singolo
> * Effettuare il provisioning di un batch di altri tenant
> * Eseguire in dettaglio le procedure per il provisioning dei tenant e la relativa registrazione nel catalogo

Provare l'[Esercitazione sul monitoraggio delle prestazioni](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Libreria client dei database elastici](sql-database-elastic-database-client-library.md)
* [Modalità di esecuzione del debug degli script in Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
