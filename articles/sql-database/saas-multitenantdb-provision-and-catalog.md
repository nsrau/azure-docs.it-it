---
title: Effettuare il provisioning di nuovi tenant e catalogarli in un'applicazione SaaS con un database multi-tenant SQL di database SQL di Azure | Microsoft Docs
description: Informazioni su come effettuare il provisioning di nuovi tenant e catalogarli in un'app SaaS multi-tenant del database SQL di Azure
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: saas apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: billgib
ms.openlocfilehash: e7de7bb545e0ce04dc1b3dd398cc920213d09bae
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="provision-and-catalog-new-tenants-in-a-saas-application-using-a-sharded-multi-tenant-sql-database"></a>Effettuare il provisioning di nuovi tenant e catalogarli in un'applicazione SaaS di esempio con un database SQL partizionato multi-tenant

Questa esercitazione contiene informazioni sui modelli per il provisioning e la catalogazione dei tenant quando si usa un modello di database multi-tenant partizionato. 

Uno schema multi-tenant, che include un ID tenant nella chiave primaria delle tabelle contenente i dati del tenant, consente di archiviare più tenant in un singolo database. Per supportare un numero elevato di tenant, i dati del tenant vengono distribuiti tra più partizioni o database. I dati di ciascun tenant sono sempre contenuti tutti in un singolo database.  Viene usato un catalogo per contenere il mapping dei tenant nei database.   

È anche possibile scegliere di popolare alcuni database solo con un tenant. I database che contengono più tenant prediligono un costo inferiore per ogni tenant a scapito dell'isolamento del tenant.  I database che contengono un solo tenant preferiscono l'isolamento rispetto al basso costo.  I database con più tenant e con tenant singolo possono essere combinati nella stessa applicazione SaaS per ottimizzare i costi o l'isolamento per ciascun tenant. I tenant possono essere dotati del proprio database quando si esegue il provisioning o possono essere spostati nel proprio database in un secondo momento.

   ![App del database multi-tenant partizionato con catalogo del tenant](media/saas-multitenantdb-provision-and-catalog/MultiTenantCatalog.png)


## <a name="tenant-catalog-pattern"></a>Modello di catalogo del tenant

Se i dati del tenant vengono distribuiti tra più database, è importante sapere dove viene archiviato ciascun dato del tenant. Un database di catalogo contiene il mapping tra un tenant e il database che ne conserva i dati.

Ogni tenant viene identificato da una chiave nel catalogo. Nelle app SaaS Wingtip Tickets la chiave del tenant viene creata a partire da un hash del nome del tenant. In questo modo l'applicazione può estrarre il nome del tenant dall'URL di una pagina Web e usarlo per connettersi al corretto database. È possibile usare anche altri schemi della chiave del tenant.

L'uso di un catalogo consente di modificare il nome o il percorso di un database tenant in seguito al provisioning senza interrompere l'applicazione.  In un modello di database multi-tenant, ciò consente di "spostare" un tenant da un database a un altro.  Il catalogo può essere usato anche per indicare a un'applicazione se un tenant è offline per manutenzione o altre azioni.

Il catalogo può anche essere esteso per archiviare metadati aggiuntivi di un tenant o di un database, ad esempio il livello o l'edizione del database, la versione dello schema del databse, il nome del tenant, il piano di servizio o il contratto di servizio, oltre ad altre informazioni che consentono la gestione delle applicazioni, l'assistenza clienti o i processi DevOps.  

Il catalogo può essere utile anche per abilitare la creazione di report tra tenant, la gestione dello schema e l'estrazione dei dati allo scopo di analizzarli. 

### <a name="elastic-database-client-library"></a>Libreria client del database elastico 
Nelle app SaaS Wingtip Tickets il catalogo viene implementato nel database *tenantcatalog* tramite le funzionalità di gestione delle partizioni della [libreria client dei database elastici](sql-database-elastic-database-client-library.md). La libreria consente a un'applicazione di creare, gestire e usare una "mappa partizioni" supportata da database. Una mappa partizioni contiene un elenco di partizioni, ovvero database, e il mapping tra le chiavi, ovvero gli ID del tenant, e le partizioni.  È possibile usare le funzioni della libreria client dei database elastici da applicazioni o script di PowerShell durante il provisioning di tenant per creare le voci della mappa partizioni e, successivamente, connettersi al database corretto. La libreria memorizza nella cache le informazioni di connessione per ridurre al minimo il traffico verso il database del catalogo e per velocizzare la connessione. 

> [!IMPORTANT]
> I dati di mapping sono accessibili nel database del catalogo, ma *non devono essere modificati*. Modificare i dati di mapping solo tramite le API della libreria client dei database elastici. La modifica diretta dei dati di mapping comporta il rischio di danneggiare il catalogo e non è supportata.


## <a name="tenant-provisioning-pattern"></a>Modello di provisioning del tenant

Durante il provisioning di un nuovo tenant nel modello di database multi-tenant partizionato, è necessario innanzitutto determinare se eseguire il provisioning sul tenant in un database condiviso o assegnare al tenant un database specifico. Nel caso di un database condiviso, è necessario determinare se c'è spazio in un database esistente o è necessario un nuovo database. Se è necessario un nuovo database, eseguirne il provisioning nella posizione e nel livello di servizio appropriati, inizializzarlo con lo schema e i dati di riferimento corretti e quindi registrarlo nel catalogo. Infine, è possibile inserire il mapping del tenant per fare riferimento alla partizione appropriata.

Eseguire il provisioning del database eseguendo gli script SQL, distribuendo un bacpac o copiando un database del modello. Le applicazioni SaaS Wingtip Tickets copiano un database del modello per creare nuovi database tenant.

L'approccio usato per il provisioning del database deve essere compreso nella strategia generale di gestione dello schema, che deve garantire che il provisioning di nuovi database venga effettuato con lo schema più recente.  Questo aspetto viene esaminato approfonditamente nell'[esercitazione relativa alla gestione dello schema](saas-tenancy-schema-management.md).  

Gli script di provisioning del tenant in questa esercitazione includono sia il provisioning di un tenant in un database esistente condiviso con altri tenant, che il provisioning di un tenant nel proprio database. I dati del tenant vengono quindi inizializzati e registrati nella mappa partizioni del catalogo. Nell'app di esempio ai database che contengono più tenant viene assegnato un nome generico, ad esempio *tenants1*, *tenants2* e così via, mentre ai database che contengono un solo tenant viene assegnato il nome del tenant. Le convenzioni di denominazione specifiche usate nell'esempio non sono una parte essenziale del modello, poiché l'uso di un catalogo consente l'assegnazione di un nome qualsiasi al database.  

## <a name="provision-and-catalog-tutorial"></a>Esercitazione sul provisioning e la catalogazione

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Eseguire il provisioning di un tenant in un database multi-tenant
> * Eseguire il provisioning di un tenant in un database a tenant singolo
> * Eseguire il provisioning di un batch di tenant in database multi-tenant e a tenant singolo
> * Registrare un database ed eseguire il mapping del tenant in un catalogo

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* È stata distribuita l'app SaaS di database multi-tenant Wingtip Tickets. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare l'applicazione SaaS di database multi-tenant Wingtip Tickets](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Ottenere gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets

Gli script e il codice sorgente dell'applicazione SaaS di database multi-tenant Wingtip Tickets sono disponibili nel repository [WingtipTicketsSaaS-MultiTenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) di GitHub. Leggere le [linee guida generali](saas-tenancy-wingtip-app-guidance-tips.md) per i passaggi da seguire per scaricare e sbloccare gli script dell'app SaaS Wingtip Tickets. 

## <a name="provision-a-tenant-in-a-shared-database-with-other-tenants"></a>Eseguire il provisioning di un tenant in un database condiviso con altri tenant

Per comprendere in che modo l'applicazione Wingtip Tickets implementi il nuovo provisioning di tenant in un database condiviso, procedere passo per passo attraverso il flusso di lavoro dopo aver aggiunto un punto di interruzione:

1. In _PowerShell ISE_ aprire ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_ e impostare i parametri seguenti:
   * **$TenantName** = **Bushwillow Blues**, nome di una nuova sede di eventi.
   * **$VenueType** = **blues**, uno dei tipi di sede predefiniti: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (in minuscolo e senza spazi).
   * **$DemoScenario** = **1** per *eseguire il provisioning di un tenant in un database condiviso con altri tenant*.

1. Aggiungere un punto di interruzione posizionando il cursore in un punto qualsiasi nella riga 38 contenente il testo: *New-Tenant `* e premere **F9**.

   ![punto di interruzione](media/saas-multitenantdb-provision-and-catalog/breakpoint.png)

1. Premere **F5** per eseguire lo script.

1. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.

   ![debug](media/saas-multitenantdb-provision-and-catalog/debug.png)

Tenere traccia dell'esecuzione dello script usando le opzioni del menu **Debug** **F10** e **F11** per eseguire le istruzioni delle funzioni chiamate. Per altre informazioni sul debug degli script di PowerShell, vedere [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Di seguito sono indicati gli elementi chiave del flusso di lavoro di provisioning che si esegue:

* **Calcolo della chiave per il nuovo tenant**. Viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
* **Controllo dell'esistenza della chiave del tenant**. Il catalogo viene controllato per verificare che la chiave non sia già stata registrata.
* **Inizializzare il tenant nel database tenant predefinito**. Il database tenant viene aggiornato per aggiungere le nuove informazioni del tenant.  
* **Registrare il tenant nel catalogo.** Il mapping tra la nuova chiave del tenant e il database tenants1 esistente viene aggiunto al catalogo. 
* **Aggiungere il nome del tenant a una tabella di estensione del catalogo**. Il nome dell'evento viene aggiunto alla tabella Tenant nel catalogo.  Questa tabella illustra in che modo è possibile estendere il database Catalogo perché sia in grado di supportare dati aggiuntivi specifici dell'applicazione.
* **Aprire la pagina Eventi per il nuovo tenant**. La pagina degli eventi *Bushwillow Blues* si apre nel browser:

   ![eventi](media/saas-multitenantdb-provision-and-catalog/bushwillow.png)


## <a name="provision-a-tenant-in-its-own-database"></a>Effettuare il provisioning di un tenant in un database autonomo

A questo punto seguire dettagliatamente questa procedura durante la creazione di un tenant nel database autonomo:

1. Ancora in ...\\Learning Modules\\ProvisionTenants\\_Demo-ProvisionTenants.ps1_ impostare i parametri seguenti:
   * **$TenantName** = **Sequoia Soccer**, il nome di una nuova sede di eventi.
   * **$VenueType** = **soccer**, uno dei tipi di sede predefiniti: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer (in minuscolo e senza spazi).
   * **$DemoScenario** = **2** per *effettuare il provisioning di un tenant in un database dedicato*.

1. Aggiungere un nuovo punto di interruzione posizionando il cursore in un punto qualsiasi nella riga 57 contenente il testo: *&&nbsp;$PSScriptRoot\New-TenantAndDatabase `* e premere **F9**.

   ![punto di interruzione](media/saas-multitenantdb-provision-and-catalog/breakpoint2.png)

1. Premere **F5** per eseguire lo script.

1. Quando l'esecuzione dello script si arresta in corrispondenza del punto di interruzione, premere **F11** per eseguire l'istruzione del codice.  Usare **F10** e **F11** per proseguire ed eseguire l'istruzione relativa alle funzioni per tracciare l'esecuzione.

Di seguito sono indicati gli elementi chiave del flusso di lavoro di provisioning che si esegue quando si tiene traccia dello script:

* **Calcolo della chiave per il nuovo tenant**. Viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
* **Controllo dell'esistenza della chiave del tenant**. Il catalogo viene controllato per verificare che la chiave non sia già stata registrata.
* **Creare un nuovo database tenant**. Il database viene creato copiando il database *basetenantdb* tramite un modello di Resource Manager.  Il nuovo nome del database si basa sul nome del tenant.
* **Aggiungere il database al catalogo**. Il nuovo database tenant è registrato come una partizione nel catalogo.
* **Inizializzare il tenant nel database tenant predefinito**. Il database tenant viene aggiornato per aggiungere le nuove informazioni del tenant.  
* **Registrare il tenant nel catalogo.** Il mapping tra la nuova chiave del tenant e il database *sequoiasoccer* viene aggiunto al catalogo.
* **Il nome del tenant viene aggiunto al catalogo**. Il nome dell'evento viene aggiunto alla tabella di estensione Tenant nel catalogo.
* **Aprire la pagina Eventi per il nuovo tenant**. La pagina degli eventi *Sequoia Soccer* si apre nel browser:

   ![eventi](media/saas-multitenantdb-provision-and-catalog/sequoiasoccer.png)


## <a name="provision-a-batch-of-tenants"></a>Eseguire il provisioning di un batch di tenant

Questo esercizio descrive come effettuare il provisioning di un batch di 17 tenant. È consigliabile eseguire il provisioning di questo batch di tenant prima di iniziare le altre esercitazioni di Wingtip Tickets, in modo da avere più database da usare.


1. In *PowerShell ISE* aprire ...\\Learning Modules\\ProvisionTenants\\*Demo-ProvisionTenants.ps1* e impostare il parametro *$DemoScenario* su 4:
   * **$DemoScenario** = **4** per *effettuare il provisioning di un batch di tenant in un database condiviso*.
1. Premere **F5** ed eseguire lo script.


### <a name="verify-the-deployed-set-of-tenants"></a>Verificare il set di tenant distribuito 
A questo punto l'utente dispone di diversi tenant distribuiti in un database condiviso e tenant distribuiti in database dedicati. Il portale di Azure può essere usato per controllare i database creati. Nel [portale di Azure](https://portal.azure.com) aprire il server **tenants1-mt-\<UTENTE\>** passando all'elenco di server SQL.  L'elenco di **database SQL** deve includere il database **tenants1** condiviso e i database per i tenant che si trovano nel proprio database:

   ![elenco di database](media/saas-multitenantdb-provision-and-catalog/Databases.png)

Sebbene il portale di Azure mostri il tenant di database, non è possibile vedere i tenant *all'interno* del database condiviso. L'elenco completo dei tenant è consultabile nella pagina dell'hub eventi di Wingtip Tickets ed esplorando il catalogo:   

**Uso della pagina dell'hub eventi di Wingtip Tickets** <br>
Aprire la pagina Hub eventi nel browser (http:events.wingtip-mt.\<UTENTE\>.trafficmanager.net)  

**Uso del database di catalogo** <br>
L'elenco completo dei tenant e i database corrispondenti è disponibile nel catalogo. Una visualizzazione SQL è disponibile nel database tenantcatalog che unisce il nome del tenant archiviato nella tabella dei tenant al nome del database nelle tabelle di gestione delle partizioni. Questa visualizzazione dimostra correttamente il valore dell'estensione dei metadati archiviati nel catalogo.

1. In *SQL Server Management Studio* connettersi al server del tenant **catalog-mt.\<UTENTE\>.database.windows.net**, con l'account di accesso: **developer**, Password: **P@ssword1**

    ![Finestra di dialogo di connessione di SQL Server Management Studio](media/saas-multitenantdb-provision-and-catalog/SSMSConnection.png)

1. In *Esplora oggetti* selezionare le viste del database *tenantcatalog*.
1. Fare clic con il pulsante destro del mouse sulla vista *TenantsExtended* e scegliere **Select Top 1000 Rows** (Seleziona le prime 1000 righe). Si noti il mapping tra nome e il database del tenant per diversi tenant.

    ![Visualizzazione ExtendedTenants in SQL Server Management Studio](media/saas-multitenantdb-provision-and-catalog/extendedtenantsview.png)
      
## <a name="other-provisioning-patterns"></a>Altri modelli di provisioning

Altri modelli di provisioning interessanti includono:

**Pre-provisioning di database in pool elastici.** Il modello di pre-provisioning sfrutta il fatto che quando si usa un pool elastico, la fatturazione dipende dal pool e non dai database. Pertanto è possibile aggiungere i database a un pool elastico prima che siano necessari senza incorrere in costi aggiuntivi. Effettuando il pre-provisioning dei database in un pool e allocandoli all'occorrenza, il tempo di provisioning di un tenant in un database può essere ridotto significativamente. Il numero di database di cui viene effettuato il pre-provisioning può essere modificato in base alle esigenze, per ottenere un buffer adatto alla frequenza di provisioning prevista.

**Provisioning automatico.** In questo modello viene usato un servizio di provisioning dedicato per eseguire il provisioning di server, pool e database automaticamente in base alle esigenze, incluso il pre-provisioning di database in pool elastici. In caso di ritiro ed eliminazione di database, i vuoti creati nei pool elastici possono essere riempiti dal servizio di provisioning in base alle esigenze. Un servizio di questo tipo può essere semplice o complesso, come nel caso della gestione del provisioning in più aree geografiche, e può includere la configurazione della replica geografica per il ripristino di emergenza. Con il modello di provisioning automatico, un'applicazione client o uno script invia una richiesta di provisioning a una coda per l'elaborazione da parte del servizio di provisioning che quindi effettuerà il polling per stabilire quando viene completata l'operazione. Se si usa il pre-provisioning, le richieste vengono gestite rapidamente ed è un altro servizio a effettuare il provisioning di un database sostitutivo in background.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Eseguire il provisioning di un singolo tenant nuovo in un database multi-tenant condiviso e nel database autonomo
> * Effettuare il provisioning di un batch di altri tenant
> * Eseguire in dettaglio le procedure per il provisioning dei tenant e la relativa registrazione nel catalogo

Provare l'[Esercitazione sul monitoraggio delle prestazioni](saas-multitenantdb-performance-monitoring.md).

## <a name="additional-resources"></a>Risorse aggiuntive

<!--* Additional [tutorials that build upon the Wingtip SaaS application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Libreria client dei database elastici](sql-database-elastic-database-client-library.md)
* [Modalità di esecuzione del debug degli script in Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)
