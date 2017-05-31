---
title: Distribuire ed esplorare un&quot;applicazione SaaS che usa il database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare l&quot;app Wingtip Tickets (WTP) di esempio del database SQL di Azure
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
ms.openlocfilehash: 04859fb8a9f4a8bf04d92e7139d9202885c52503
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione SaaS multi-tenant che usa il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare l'applicazione SaaS Wingtip Tickets Platform (WTP). L'applicazione usa un database per ogni tenant, il modello di applicazione SaaS, per soddisfare le richieste di più tenant. L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che supportano gli scenari SaaS e i modelli di progettazione e gestione SaaS.

Cinque minuti dopo aver fatto clic sul pulsante *Deploy to Azure* (Distribuisci in Azure) riportato di seguito, si avrà a disposizione un'applicazione SaaS multi-tenant, che usa database SQL, in esecuzione nel cloud. L'applicazione viene distribuita con tre tenant di esempio, ognuno con un database proprio, tutti distribuiti in un pool elastico SQL. L'app viene distribuita nella sottoscrizione di Azure e consente l'accesso completo per esaminare e utilizzare i singoli componenti dell'applicazione.

Gli script e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS).

In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Come distribuire l'applicazione WTP
> * Informazioni sui server, i pool e i database che costituiscono l'app
> * Come vengono mappati i tenant e i relativi dati con il *catalogo*
> * Come effettuare il provisioning di nuovi tenant
> * Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

Per esplorare i vari modelli di progettazione e gestione SaaS, è disponibile una [serie di esercitazioni correlate](sql-database-wtp-overview.md) basate su questa distribuzione iniziale. Esaminando le varie esercitazioni, approfondire gli script forniti e analizzare le modalità di implementazione dei diversi modelli SaaS. Analizzare in dettaglio i vari passaggi degli script in ogni esercitazione per comprendere in modo più approfondito come implementare le molte funzionalità di database SQL che semplificano lo sviluppo di applicazioni SaaS.

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>Distribuire l'applicazione SaaS Wingtip Tickets (WTP)

La distribuzione della piattaforma Wingtip Tickets richiede meno di cinque minuti:

1. Fare clic per distribuirla:

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Immettere i valori dei parametri necessari per la distribuzione:

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse** e non usare gruppi di risorse, server o pool esistenti e non usare questa applicazione, o le risorse da essa create, in ambiente di produzione. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.

    * **Gruppo di risorse**: selezionare **Crea nuovo** e specificare **Nome** e **Posizione**.
    * **Utente**: alcune risorse richiedono nomi univoci per tutte le sottoscrizioni di Azure. Per garantire l'univocità, fornire un valore per distinguere le risorse create da quelle create da altri utenti che distribuiscono l'applicazione Wingtip. È consigliabile usare un nome **Utente** breve, come le iniziali più un numero (ad esempio, *bg1*), quindi usare tale nome nel nome del gruppo di risorse (ad esempio, *wingtip-bg1*). Il parametro **Utente** può contenere solo numeri, lettere e trattini. Il primo e ultimo carattere devono essere una lettera o un numero (si consiglia di usare solo lettere minuscole).

     ![template](./media/sql-database-saas-tutorial/template.png)

1. **Distribuire l'applicazione**.

    * Fare clic se si accettano i termini e le condizioni.
    * Selezionare **Aggiungi al dashboard**.
    * Fare clic su **Acquista**.

1. Monitorare lo stato di distribuzione facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'applicazione WTP richiede circa quattro minuti.

   ![distribuzione completata](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>Esplorare l'applicazione

L'app presenta sedi di eventi, come sale concerto, jazz club e club sportivi. Queste sedi vengono registrate come clienti (o tenant) di Wingtip Tickets Platform (WTP), per ottenere un modo semplice per presentare elenchi di eventi e vendere i biglietti. Ogni sede di eventi ottiene un'app Web personalizzata per gestire ed elencare gli eventi e vendere i biglietti, in modo indipendente e isolato dagli altri tenant. Dietro le quinte, ogni tenant ottiene un database SQL distribuito in un pool elastico SQL.

Un **Events Hub** (Hub eventi) centrale presenta un elenco degli URL dei tenant specifici della distribuzione. Tutti gli URL dei tenant includono il valore *Utente* specifico nel formato: http://events.wtp.&lt;UTENTE&gt;.trafficmanager.net/*fabrikamjazzclub*. 

1. Aprire _Events Hub_ (Hub eventi): http://events.wtp.&lt;UTENTE&gt;.trafficmanager.net (sostituire con il proprio nome utente):

    ![hub eventi](media/sql-database-saas-tutorial/events-hub.png)

1. Fare clic su **Fabrikam Jazz Club** in *Events Hub* (Hub eventi).

   ![Eventi](./media/sql-database-saas-tutorial/fabrikam.png)

1. Fare clic su **Tickets** (Biglietti) ed esplorare le opzioni per l'acquisto dei biglietti per un evento.

L'applicazione WTP usa [*Gestione traffico di Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) per controllare la distribuzione del traffico in ingresso. Le pagine degli eventi, specifiche del tenant, richiedono che gli URL includano i nomi del tenant. L'app degli eventi analizza il nome del tenant dall'URL e lo usa per creare una chiave per accedere a un catalogo usando il [*gestore delle mappe partizioni*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). Il catalogo esegue il mapping della chiave alla posizione del database del tenant. **Events Hub** (Hub eventi) usa i metadati estesi nel catalogo per recuperare il nome del tenant associato a ogni database.

In ambiente di produzione si crea in genere un record DNS CNAME per [*puntare un dominio Internet aziendale*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) al profilo di Gestione traffico.

## <a name="get-the-wingtip-application-scripts"></a>Ottenere gli script dell'applicazione Wingtip

Gli script di Wingtip Tickets e il codice sorgente dell'applicazione sono disponibili nel repository GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). I file di script si trovano nella [cartella Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Scaricare la cartella **Learning Modules** nel computer locale, mantenendo la struttura delle cartelle.

## <a name="provision-a-new-tenant"></a>Effettuare il provisioning di un nuovo tenant

La distribuzione iniziale crea tre tenant di esempio, ma è necessario creare altri tenant per ottenere un'esperienza ottimale per l'esercitazione. Questo passaggio descrive come creare rapidamente un nuovo tenant. In un secondo momento è consigliabile approfondire in dettaglio il processo di provisioning di nuovi tenant nell'[esercitazione su provisioning e catalogazione](sql-database-saas-tutorial-provision-and-catalog.md), nella quale viene illustrato come sia semplice implementare un componente di registrazione nell'applicazione ed eseguire automaticamente il provisioning dei tenant quando si registrano i clienti.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>Inizializzare il file di configurazione dell'utente per la distribuzione

Trattandosi del primo script da eseguire dopo la distribuzione iniziale dell'applicazione WTP, è necessario aggiornare il file di configurazione dell'utente. Aggiornare le variabili con i valori specifici della distribuzione.

   1. Aprire ...\\Learning Modules\\*UserConfig.psm1* in *PowerShell ISE*
   1. Modificare _$userConfig.ResourceGroupName_ specificando il _gruppo di risorse_ impostato al momento della distribuzione dell'app.
   1. Modificare _$userConfig.Name_ specificando il nome _Utente_ impostato al momento della distribuzione dell'app.

### <a name="provision-the-new-tenant"></a>Effettuare il provisioning del nuovo tenant

1. Aprire ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* in *PowerShell ISE*.
1. Premere **F5** per eseguire lo script (per il momento lasciare i valori predefiniti dello script).

Il nuovo tenant viene registrato nel catalogo. Il database corrispondente viene creato e aggiunto a un pool elastico SQL. Dopo il corretto completamento del provisioning, il sito per la vendita dei biglietti del nuovo tenant viene visualizzato nel browser:

![Nuovo tenant](./media/sql-database-saas-tutorial/red-maple-racing.png)

Aggiornare l'hub eventi e verificare che il nuovo tenant sia incluso nell'elenco.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Ora che sono disponibili vari database tenant, è possibile iniziare a usarli. È disponibile uno script di PowerShell che simula un carico di lavoro eseguito su tutti i database tenant. Il carico viene eseguito per 60 minuti per impostazione predefinita. Wingtip Tickets è un'app SaaS e i carichi di lavoro per le app SaaS reali sono in genere sporadici e imprevedibili. Per simulare questa situazione, il generatore produce un carico casuale distribuito tra tutti i tenant. Servono alcuni minuti affinché emerga il modello, quindi lasciare in esecuzione il generatore di carico per circa 5-10 minuti prima di tentare di monitorare il carico come descritto nelle sezioni seguenti.

1. Aprire ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1* in **PowerShell ISE**
1. Premere **F5** per eseguire lo script e avviare il generatore di carico (lasciare i valori dei parametri predefiniti per il momento).

> [!IMPORTANT]
> Il generatore di carico esegue una serie di processi nella sessione di PowerShell locale, quindi è importante mantenere aperta la sessione di PowerShell. Se si chiude PowerShell o la scheda da cui è stato avviato il generatore di carico oppure se si sospende il computer, i processi verranno arrestati.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Esplorare i server, i pool e i database tenant

Dopo aver esplorato l'applicazione, completato il provisioning di un nuovo tenant e avviato l'esecuzione di un carico sulla raccolta di tenant, è tempo di esaminare alcune delle risorse distribuite.

1. Nel [portale di Azure](http://portal.azure.com) aprire il server **catalog-&lt;UTENTE&gt;**. Il server di catalogo contiene due database: **tenantcatalog** e **basetenantdb**, ovvero un database *di riferimento* vuoto copiato per creare i nuovi tenant.

   ![database](./media/sql-database-saas-tutorial/databases.png)

1. Aprire il server **tenants1-&lt;UTENTE&gt;** che include i database tenant. Si noti che ogni database tenant è un database _standard elastico_ in un pool standard da 50 eDTU. Notare inoltre che è presente un database per _Red Maple Racing_, ovvero il tenant di cui è stato eseguito il provisioning in precedenza.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Monitorare il pool

Se il generatore di carico è rimasto in esecuzione per diversi minuti, dovrebbero essere disponibili abbastanza dati per iniziare a esaminare alcune delle funzionalità di monitoraggio incorporate nei pool e nei database.

1. Passare al server *tenants1-&lt;UTENTE&gt;* e fare clic su **Pool1** per visualizzare l'utilizzo di risorse per il pool:

   ![monitorare il pool](./media/sql-database-saas-tutorial/monitor-pool.png)

Quello che dimostrano chiaramente questi due grafici è quanto i pool elastici e database SQL siano adatti ai carichi di lavoro di applicazioni SaaS. Quattro database, ognuno dei quali con picchi di utilizzo fino a 40 eDTU, possono essere supportati senza problemi in pool da 50 eDTU. Se ogni database venisse sottoposto a provisioning come database autonomo, ognuno dovrebbe essere di livello S2 (50 DTU) per supportare i picchi, ma il costo di 4 database autonomi S2 sarebbe pari a quasi 3 volte il prezzo del pool. Senza contare che il pool ha ancora spazio per molti altri database. In situazioni reali, i clienti possono eseguire attualmente fino a 500 database in pool da 200 eDTU. Per altre informazioni, vedere l'[esercitazione sul monitoraggio delle prestazioni](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="deleting-the-resources-created-with-this-tutorial"></a>Eliminazione delle risorse create con questa esercitazione

Dopo aver completato l'esplorazione e l'analisi dell'app WTP, passare al gruppo di risorse dell'applicazione nel portale ed eliminarlo per interrompere la fatturazione correlata a questa distribuzione. Con l'applicazione verranno eliminate anche le risorse eventualmente create durante l'esecuzione delle esercitazioni correlate.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]

> * Come distribuire l'applicazione WTP
> * Informazioni sui server, i pool e i database che costituiscono l'app
> * Come vengono mappati i tenant e i relativi dati con il *catalogo*
> * Come effettuare il provisioning di nuovi tenant
> * Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

È ora possibile continuare provando l'[esercitazione su provisioning e catalogazione](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Risorse aggiuntive

* [Altre esercitazioni basate sulla distribuzione iniziale dell'applicazione Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* Per informazioni sui pool elastici, vedere [*Informazioni sul pool elastico di Azure SQL*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Per informazioni sui processi elastici, vedere [*Gestione dei database cloud con scalabilità orizzontale*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Per informazioni sulle applicazioni SaaS multi-tenant, vedere [*Modelli di progettazione per le applicazioni SaaS multi-tenant*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

