---
title: Effettuare il provisioning di nuovi tenant in un&quot;app multi-tenant che usa il database SQL di Azure | Microsoft Docs
description: Effettuare il provisioning di nuovi tenant e catalogarli nell&quot;app SaaS Wingtip Tickets (WTP) di esempio del database SQL
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
ms.openlocfilehash: aae5d85a18f93b7821a6ef8fc7161dd9a6ebe533
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Effettuare il provisioning di nuovi tenant e registrarli nel catalogo

Questa esercitazione illustra come effettuare il provisioning di nuovi tenant nell'applicazione SaaS Wingtip Tickets Platform (WTP). Vengono descritte le procedure per creare i tenant e i database tenant, quindi per registrare i tenant nel catalogo. Il *catalogo* è un database che gestisce il mapping tra molti tenant di applicazioni SaaS e i relativi dati. Usare questi script per esplorare i modelli di provisioning e catalogazione usati, nonché per scoprire come funziona la registrazione di nuovi tenant nel catalogo. Il catalogo svolge un ruolo importante nell'indirizzamento delle richieste delle applicazioni ai database corretti.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]

> * Effettuare il provisioning di un nuovo tenant singolo
> * Effettuare il provisioning di un batch di altri tenant
> * Eseguire in dettaglio le procedure per il provisioning di nuovi tenant e la loro registrazione nel catalogo.


Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* L'app WTP è stata distribuita. Per eseguire la distribuzione in meno di cinque minuti, vedere [Distribuire ed esplorare un'applicazione SaaS multi-tenant di esempio che usa il database di SQL Azure](sql-database-saas-tutorial.md)
* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introduzione al modello di catalogazione SaaS

In un'applicazione SaaS multi-tenant supportata da database è importante sapere dove sono archiviate le informazioni per ogni tenant. Nel modello di catalogo SaaS, viene usato un database di catalogo per contenere il mapping tra tenant e la posizione in cui sono archiviati i relativi dati. L'app WTP usa un'architettura di database single-tenant, ma viene applicato il modello di base che prevede l'archiviazione del mapping tenant-database in un catalogo, indipendentemente dal fatto che venga usato un database multi-tenant o a tenant singolo.

A ogni tenant viene assegnata una chiave per distinguere i relativi dati nel catalogo. Nell'applicazione WTP la chiave viene formata da un hash del nome del tenant. Questo modello consente di usare la parte del nome del tenant dell'URL dell'applicazione per comporre la chiave e recuperare la connessione di un tenant specifico. Si potrebbero usare altri schemi di identificazione senza effetti sul modello nel suo complesso.

Il catalogo nell'app WTP viene implementato mediante la tecnologia di gestione delle partizioni nella [libreria client di database elastici](sql-database-elastic-database-client-library.md). La libreria client di database elastici è responsabile della creazione e gestione di un _catalogo_ supportato da database in cui viene mantenuta aggiornata una _mappa partizioni_. Il catalogo contiene il mapping tra le chiavi (tenant) e i relativi database (partizioni).

> [!IMPORTANT]
> I dati di mapping sono accessibili nel database del catalogo, ma *non modificarli*. Modificare i dati di mapping solo tramite le API della libreria client dei database elastici. La modifica diretta dei dati di mapping comporta il rischio di danneggiare il catalogo e non è supportata.

L'app SaaS Wingtip effettua il provisioning dei nuovi tenant copiando un database *modello*.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="provision-a-new-tenant"></a>Effettuare il provisioning di un nuovo tenant

Se è già stato creato un tenant nella prima esercitazione WTP, è possibile passare alla sezione successiva dedicata al [provisioning di un batch di tenant](#provision-a-batch-of-tenants).

Eseguire lo script *Demo-ProvisionAndCatalog* per creare velocemente un tenant e registrarlo nel catalogo:

1. Aprire **Demo-ProvisionAndCatalog.ps1** in PowerShell ISE e impostare i valori seguenti:
   * **$TenantName** = nome della nuova sede di eventi, ad esempio *Bushwillow Blues*. 
   * **$VenueType** = uno dei tipi di sedi predefiniti: blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = 1, lasciare il valore impostato su _1_ per **effettuare il provisioning di un singolo tenant**.

1. Premere **F5** ed eseguire lo script.

Dopo il completamento dello script, viene effettuato il provisioning del nuovo tenant e l'app degli eventi** corrispondente verrà aperta nel browser:

![Nuovo tenant](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Effettuare il provisioning di un batch di tenant

Questo esercizio descrive come effettuare il provisioning di un batch di altri tenant. È consigliabile farlo prima di completare altre esercitazioni relative all'app WTP.

1. Aprire ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* in *PowerShell ISE* e impostare il valore seguente:
   * **$DemoScenario** = **3**, impostare su **3** per **effettuare il provisioning di un batch di tenant**.
1. Premere **F5** ed eseguire lo script.

Lo script distribuisce un batch di altri tenant e usa [modello di Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) che controlla il batch, quindi delega il provisioning di ogni database a un modello collegato. L'uso dei modelli in questo modo consente ad Azure Resource Manager di gestire il processo di provisioning per lo script. I modelli effettuano il provisioning dei database in parallelo, quando possibile, e gestiscono la ripetizione dei tentativi se necessario, ottimizzando il processo nel suo complesso. Lo script è idempotente, quindi viene eseguito di nuovo in caso di interruzione.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificare la corretta distribuzione del batch di tenant

* Aprire il server *tenants1* nel [portale di Azure](https://portal.azure.com) e fare clic su **Database SQL**:

   ![elenco di database](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Procedura dettagliata per il provisioning e la catalogazione

Per capire meglio come l'applicazione Wingtip implementa il provisioning di nuovi tenant, eseguire di nuovo lo script *Demo-ProvisionAndCatalog* ed effettuare il provisioning di un altro tenant. Questa volta, aggiungere un punto di interruzione ed eseguire il flusso di lavoro istruzione per istruzione:

1. Aprire ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ e impostare le opzioni seguenti sui valori del nuovo tenant non esistenti nel catalogo corrente:
   * **$TenantName** = impostare un nuovo nome, ad esempio *Hackberry Hitters*.
   * **$VenueType** = usare uno dei tipi di sedi predefiniti, ad esempio *judo*.
   * **$DemoScenario** = 1, impostare su **1** per **effettuare il provisioning di un singolo tenant**.

1. Aggiungere un punto di interruzione posizionando il cursore in un punto qualsiasi nella riga seguente: *New-Tenant `* e premere **F9**.

   ![punto di interruzione](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Premere **F5** per eseguire lo script. Quando viene raggiunto il punto di interruzione, premere **F11** per eseguire l'istruzione. Tracciare l'esecuzione dello script usando **F10** e **F11** per eseguire un'istruzione alla volta o singolarmente le varie funzioni chiamate. [Suggerimenti per l'utilizzo e il debug degli script di PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Esaminare nei dettagli l'implementazione di provisioning e catalogazione eseguendo lo script un'istruzione alla volta

Lo script effettua il provisioning dei nuovi tenant e li cataloga eseguendo le operazioni seguenti:

1. **Importazione del modulo SubscriptionManagement.psm1** che contiene funzioni per l'accesso ad Azure e la selezione della sottoscrizione di Azure da usare.
1. **Importazione del modulo CatalogAndDatabaseManagement.psm1** che fornisce un catalogo e l'astrazione a livello di tenant per le funzioni di [gestione delle partizioni](sql-database-elastic-scale-shard-map-management.md). Questo è un modulo importante che incapsula gran parte del modello di catalogazione e vale la pena esplorare.
1. **Recupero dei dettagli di configurazione**. Eseguire l'istruzione _Get-configurazione_ (con **F11**) e vedere come viene specificata la configurazione dell'app. In questa posizione sono definiti i nomi delle risorse e altri valori specifici dell'app, ma non modificare questi valori fino a quando non si ha familiarità con gli script.
1. **Recupero dell'oggetto catalogo**. Eseguire l'istruzione *Get-Catalog* per vedere come viene inizializzato il catalogo con le funzioni di gestione delle partizioni importate da **AzureShardManagement.psm1**. Il catalogo è composto dagli oggetti seguenti:
   * $catalogServerFullyQualifiedName viene costruito usando l'origine standard e il nome utente: _catalog-\<utente\>.database.windows.net_.
   * Il valore $catalogDatabaseName viene recuperato dalla configurazione: *tenantcatalog*.
   * L'oggetto $shardMapManager viene inizializzato dal database del catalogo.
   * L'oggetto $shardMap viene inizializzato dalla mappa partizioni *tenantcatalog* nel database del catalogo.
   Viene composto e restituito un oggetto catalogo, usato nello script di livello superiore.
1. **Calcolo della chiave per il nuovo tenant**. Viene usata una funzione hash per creare la chiave del tenant dal nome del tenant.
1. **Controllo dell'esistenza della chiave del tenant**. Il catalogo viene controllato per verificare che la chiave sia disponibile.
1. **Provisioning del database tenant con New-TenantDatabase.** Usare **F11** per eseguire le singole istruzioni e vedere come viene effettuato il provisioning del database con un modello di Resource Manager.
    
Il nome del database viene costruito dal nome del tenant, in modo che sia chiaro quale partizione appartiene a quale tenant. (È possibile usare tranquillamente altre strategie per la denominazione dei database.)

Viene usato un modello di Resource Manager per **creare un database tenant copiando un database *di riferimento*** (baseTenantDB) nel server di catalogo.  Un approccio alternativo potrebbe consistere nel creare un database vuoto e quindi inizializzarlo importando un file bacpac.

Il modello di Resource Manager è disponibile nella cartella …\\Learning Modules\\Common\\: *tenantdatabasecopytemplate.json*

Dopo la creazione, il database tenant viene inizializzato con il nome e il tipo della sede di eventi (tenant). In questa posizione è possibile eseguire anche altre inizializzazioni.

Il database tenant viene registrato nel catalogo con *Add-TenantDatabaseToCatalog* usando la chiave del tenant. Usare **F11** per esaminare i dettagli:

* Il database del catalogo viene aggiunto alla mappa partizioni (elenco dei database noti).
* Viene creato il mapping che collega il valore della chiave alla partizione.
* Vengono aggiunti ulteriori metadati per il tenant (nome della sede).

Dopo aver completato il provisioning, l'esecuzione torna allo script originale*Demo-ProvisionAndCatalog* e la pagina degli eventi****  per il nuovo tenant verrà aperta nel browser:

   ![eventi](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Altri modelli di provisioning

Gli altri modelli di provisioning non inclusi in questa esercitazione includono:

**Pre-provisioning di database.** Questo modello sfrutta il fatto che i database in un pool elastico non comportano costi aggiuntivi (la fatturazione è collegata al pool elastico e non ai database) e che i database inattivi non consumano risorse. Effettuando il pre-provisioning dei database in un pool e allocandoli poi all'occorrenza, il tempo di onboarding dei tenant può essere ridotto significativamente. Il numero di database di cui viene effettuato il pre-provisioning può essere modificato in base alle esigenze, per ottenere un buffer adatto alla frequenza di provisioning prevista.

**Provisioning automatico.** In questo modello viene usato un servizio di provisioning dedicato per effettuare il provisioning di server, pool e database automaticamente in base alle esigenze, incluso il pre-provisioning di database in pool elastici se lo si desidera. In caso di ritiro ed eliminazione di database, inoltre, i vuoti nei pool elastici possono essere riempiti dal servizio di provisioning in base alle esigenze. Un servizio di questo tipo può essere semplice o complesso, come nel caso della gestione del provisioning in più aree geografiche, e può includere la configurazione automatica della replica geografica se si usa tale strategia per il ripristino di emergenza. Con il modello di provisioning automatico, un'applicazione client o uno script invia una richiesta di provisioning a una coda per l'elaborazione da parte del servizio di provisioning e quindi effettuerà il polling del servizio per stabilire quando viene completata l'operazione. Se si usa il pre-provisioning, le richieste vengono gestite rapidamente ed è il servizio a gestire il provisioning di un database sostitutivo in background.


## <a name="stopping-wingtip-saas-application-related-billing"></a>Interruzione della fatturazione correlata all'applicazione SaaS Wingtip

Se non si prevede di continuare con un'altra esercitazione, si consiglia di eliminare tutte le risorse per sospendere la fatturazione. Eliminare il gruppo di risorse in cui è stata distribuita l'applicazione WTP. Verranno eliminate anche tutte le risorse corrispondenti.

* Passare al gruppo di risorse dell'applicazione nel portale ed eliminarlo per interrompere la fatturazione correlata a questa distribuzione dell'applicazione WTP.

## <a name="tips"></a>Suggerimenti

* La libreria client di database elastici include anche funzionalità importanti che consentono alle applicazioni client di connettersi al catalogo e modificarlo. È possibile usare la libreria client di database elastici anche per recuperare una connessione ADO.NET per un determinato valore di chiave, consentendo all'applicazione di connettersi al database corretto. Il client memorizza nella cache queste informazioni di connessione per ridurre al minimo il traffico verso il database del catalogo e per velocizzare l'applicazione.



## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come:

> [!div class="checklist"]

> * Effettuare il provisioning di un nuovo tenant singolo
> * Effettuare il provisioning di un batch di altri tenant
> * Eseguire in dettaglio le procedure per il provisioning di nuovi tenant e la loro registrazione nel catalogo.

[Esercitazione sul monitoraggio delle prestazioni](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Libreria client dei database elastici](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Modalità di esecuzione del debug degli script in Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

