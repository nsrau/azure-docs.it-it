---
title: Esercitazione sulle app SaaS multi-tenant - Database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare l'applicazione SaaS Wingtip multi-tenant, che illustra i modelli di SaaS utilizzando il database SQL di Azure.
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
ms.date: 07/26/2017
ms.author: sstein
ms.openlocfilehash: f624485d32901abb5f0d01c86fc30ddaaae261a3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/31/2017
---
# <a name="deploy-and-explore-a-multi-tenant-application-that-uses-azure-sql-database---wingtip-saas"></a>Distribuire ed esplorare un'applicazione multi-tenant che utilizza il database SQL di Azure - SaaS Wingtip

Questa esercitazione illustra come distribuire ed esplorare l'applicazione SaaS Wingtip. L'applicazione usa un database per ogni tenant, il modello di applicazione SaaS, per soddisfare le richieste di più tenant. L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS.

Cinque minuti dopo aver fatto clic sul pulsante *Deploy to Azure* (Distribuisci in Azure) riportato di seguito, si avrà a disposizione un'applicazione SaaS multi-tenant, che usa database SQL, in esecuzione nel cloud. L'applicazione viene distribuita con tre tenant di esempio, ognuno con un database proprio, tutti distribuiti in un pool elastico SQL. L'app viene distribuita nella sottoscrizione di Azure e consente l'accesso completo per esaminare e utilizzare i singoli componenti dell'applicazione. Il codice sorgente e gli script di gestione dell'applicazione sono disponibili nel repository GitHub WingtipSaaS.


In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Come distribuire l'applicazione SaaS Wingtip
> * Dove ottenere il codice sorgente e gli script di gestione dell'applicazione
> * Informazioni sui server, i pool e i database che costituiscono l'app
> * Come vengono mappati i tenant e i relativi dati con il *catalogo*
> * Come effettuare il provisioning di un nuovo tenant
> * Come monitorare l'attività del tenant nell'app

Per esplorare i vari modelli di progettazione e gestione SaaS, è disponibile una [serie di esercitazioni correlate](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials) basate su questa distribuzione iniziale. Esaminando le varie esercitazioni, approfondire gli script forniti e analizzare le modalità di implementazione dei diversi modelli SaaS. Analizzare in dettaglio i vari passaggi degli script in ogni esercitazione per comprendere in modo più approfondito come implementare le molte funzionalità di database SQL che semplificano lo sviluppo di applicazioni SaaS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-saas-application"></a>Distribuire l'applicazione SaaS Wingtip

Distribuire l'app SaaS Wingtip:

1. Fare clic sul pulsante **Distribuisci in Azure** per aprire il portale di Azure con il modello di distribuzione SaaS Wingtip. Il modello richiede due valori di parametro, un nome per un nuovo gruppo di risorse e un nome utente che distingue questa distribuzione da altre distribuzioni dell'app SaaS Wingtip. Il passaggio seguente fornisce dettagli per l'impostazione di questi valori.

   Assicurarsi di annotare i valori esatti utilizzati che sarà necessario immettere in un file di configurazione in un secondo momento.

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Immettere i valori dei parametri necessari per la distribuzione:

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse** e non utilizzare gruppi di risorse, server o pool esistenti. Non utilizzare l'applicazione o le risorse che crea per la produzione. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.

    * **Gruppo di risorse**: selezionare **Crea nuovo** e specificare il **nome** del nuovo gruppo di risorse. Selezionare un **percorso** nell'elenco a discesa.
    * **Utente**: alcune risorse richiedono nomi univoci a livello globale. Per garantire l'univocità, ogni volta che si distribuisce l'applicazione fornire un valore per distinguere le risorse create da quelle create da altre distribuzioni dell'applicazione Wingtip. È consigliabile usare un nome **Utente** breve, come le iniziali più un numero (ad esempio, *bg1*), quindi usare tale nome nel nome del gruppo di risorse (ad esempio, *wingtip-bg1*). Il parametro **Utente** può contenere solo lettere, numeri e trattini (senza spazi). Il primo e ultimo carattere devono essere una lettera o un numero (si consiglia di usare solo lettere minuscole).


1. **Distribuire l'applicazione**.

    * Fare clic per accettare i termini e le condizioni.
    * Fare clic su **Acquista**.

1. Monitorare lo stato di distribuzione facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app SaaS Wingtip richiede circa cinque minuti.

   ![distribuzione completata](media/sql-database-saas-tutorial/succeeded.png)

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>Scaricare e sbloccare gli script dell'app SaaS Wingtip

Durante la distribuzione dell'applicazione, scaricare il codice sorgente e gli script di gestione.

> [!IMPORTANT]
> I contenuti eseguibili (script, DLL) possono essere bloccati da Windows quando si scaricano e si estraggono i file ZIP da un'origine esterna. Quando si estraggono gli script da un file ZIP, seguire questa procedura per sbloccare il file ZIP prima di estrarlo. In questo modo sarà possibile eseguire gli script.

1. Passare al [repository GitHub SaaS Wingtip](https://github.com/Microsoft/WingtipSaaS).
1. Fare clic su **Clona o scarica**.
1. Fare clic su **Scarica ZIP** e salvare il file.
1. Fare clic con il pulsante destro del mouse su **WingtipSaaS-master.zip** e selezionare **Proprietà**.
1. Nella scheda **Generale** selezionare **Annulla blocco** e fare clic su **Applica**.
1. Fare clic su **OK**.
1. Estrarre i file.

Gli script si trovano nella cartella *..\\WingtipSaaS-master\\Learning Modules*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Aggiornare il file di configurazione per la distribuzione

Prima di eseguire qualsiasi script, impostare i valori *gruppo di risorse* e *utente* in **UserConfig.psm1**. Impostare queste variabili sui valori impostati durante la distribuzione.

1. Aprire ...\\Learning Modules\\*UserConfig.psm1* in *PowerShell ISE*
1. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
1. Salvare le modifiche.

Impostando qui questi valori specifici della distribuzione, non sarà necessario aggiornarli in ogni script.

## <a name="run-the-application"></a>Eseguire l'applicazione

L'app presenta sedi di eventi, come sale concerto, jazz club e club sportivi. Queste sedi vengono registrate come clienti (o tenant) della piattaforma Wingtip, per presentare elenchi di eventi e vendere i biglietti con facilità. Ogni sede di eventi ottiene un'app Web personalizzata per gestire ed elencare gli eventi e vendere i biglietti, in modo indipendente e isolato dagli altri tenant. Dietro le quinte, ogni tenant ottiene un database SQL distribuito in un pool elastico SQL.

Un **Events Hub** (Hub eventi) centrale presenta un elenco degli URL dei tenant specifici della distribuzione.

1. Aprire l'_hub eventi_ nel Web browser: http://events.wtp.&lt;UTENTE&gt;.trafficmanager.net (sostituire con il nome utente della distribuzione):

    ![Hub eventi](media/sql-database-saas-tutorial/events-hub.png)

1. Fare clic su **Fabrikam Jazz Club** in *Events Hub* (Hub eventi).

   ![Eventi](./media/sql-database-saas-tutorial/fabrikam.png)


Per controllare la distribuzione delle richieste in ingresso, l'app usa [*Gestione traffico di Microsoft Azure*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview). Le pagine degli eventi, specifiche del tenant, richiedono che gli URL includano i nomi del tenant. Tutti gli URL dei tenant includono il valore *Utente* specifico nel formato: http://events.wtp.&lt;UTENTE&gt;.trafficmanager.net/*fabrikamjazzclub*. L'app degli eventi analizza il nome del tenant dall'URL e lo usa per creare una chiave per accedere a un catalogo usando il [*gestore delle mappe partizioni*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). Il catalogo esegue il mapping della chiave alla posizione del database del tenant. L'**hub eventi** usa i metadati estesi nel catalogo per recuperare il nome del tenant associato a ogni database che fornisce l'elenco di URL.

In ambiente di produzione si crea in genere un record DNS CNAME per [*puntare un dominio Internet aziendale*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) al profilo di Gestione traffico.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Dopo la distribuzione, è possibile utilizzare l'app. Lo script di PowerShell *Demo-LoadGenerator* avvia un carico di lavoro eseguito su tutti i database tenant. Il carico del mondo reale in molte app SaaS è generalmente sporadico e imprevedibile. Per simulare questo tipo di carico, il generatore produce un carico distribuito su tutti i tenant, con picchi casuali che si presentano in ogni tenant a intervalli casuali. Per questo motivo l'affiorare del modello di carico può richiedere diversi minuti, è pertanto consigliabile lasciare agire il generatore per almeno tre o quattro minuti prima di monitorare il carico.

1. In *PowerShell ISE* aprire lo script \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
1. Premere **F5** per eseguire lo script e avviare il generatore di carico (lasciare i valori dei parametri predefiniti per il momento).

> [!IMPORTANT]
> Per eseguire altri script, aprire una nuova finestra di PowerShell ISE. Il generatore di carico esegue una serie di processi nella sessione di PowerShell locale. Lo script *Demo-LoadGenerator.ps1* avvia lo script del generatore del carico effettivo che viene eseguito come attività di primo piano oltre a una serie di processi di generazione del carico in background. Un processo del generatore di carico viene richiamato per ogni database registrato nel catalogo. I processi sono in esecuzione nella sessione di PowerShell locale, pertanto la chiusura della sessione di PowerShell arresta tutti i processi. Se si sospende il computer, la generazione del carico viene messa in pausa e riprende quando si riattiva il computer.

Quando il generatore di carico richiama i processi di generazione del carico per ogni tenant, l'attività di primo piano rimane nello stato di richiamo del processo in cui processi aggiuntivi in background sono avviati per eventuali nuovi tenant di cui viene eseguito il provisioning successivamente. È possibile utilizzare *CTRL-C* o premere il pulsante *Interrompi* per arrestare l'attività in primo piano, ma i processi in background esistenti continueranno a generare il carico in ogni database. Se si desidera monitorare e controllare i processi in background, utilizzare *Get-Job*, *Receive-Job* e *Stop-Job*. Durante l'esecuzione dell'attività in primo piano, non è possibile utilizzare la stessa sessione di PowerShell per eseguire altri script. Per eseguire altri script, aprire una nuova finestra di PowerShell ISE.

Se si desidera riavviare la sessione del generatore di carico, ad esempio con parametri diversi, è possibile arrestare l'attività in primo piano e quindi eseguire nuovamente lo script *Demo LoadGenerator.ps1*. La ripetizione dell'esecuzione di *Demo LoadGenerator.ps1* prima arresta i processi attualmente in esecuzione e quindi riavvia il generatore che avvia un nuovo set di processi utilizzando i parametri correnti.

Per il momento, lasciare il generatore di carico in esecuzione nello stato di richiamo del processo.


## <a name="provision-a-new-tenant"></a>Effettuare il provisioning di un nuovo tenant

La distribuzione iniziale crea tre tenant di esempio, ma è possibile crearne un altro per valutarne l'impatto sull'applicazione distribuita. Il flusso di lavoro dei tenant di provisioning SaaS Wingtip è descritto dettagliatamente nell'[esercitazione sul provisioning e la catalogazione](sql-database-saas-tutorial-provision-and-catalog.md). Questo passaggio descrive come creare rapidamente un nuovo tenant.

1. Aprire ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* in *PowerShell ISE*.
1. Premere **F5** per eseguire lo script (per il momento lasciare i valori predefiniti).

   > [!NOTE]
   > Molti script SaaS Wingtip utilizzano *$PSScriptRoot* per consentire l'esplorazione delle cartelle per chiamare le funzioni in altri script. Questa variabile viene valutata solo quando viene eseguito lo script premendo **F5**.  L'evidenziazione e l'esecuzione di una selezione (**F8**) può causare errori, quindi premere **F5** per l'esecuzione di script.

Il nuovo database tenant viene creato in un pool elastico SQL, inizializzato e registrato nel catalogo. Dopo il corretto completamento del provisioning, il sito *Eventi* per la vendita dei biglietti del nuovo tenant viene visualizzato nel browser:

![Nuovo tenant](./media/sql-database-saas-tutorial/red-maple-racing.png)

Aggiornare l'*hub eventi* e verificare che il nuovo tenant sia incluso nell'elenco.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Esplorare i server, i pool e i database tenant

Dopo aver avviato l'esecuzione di un carico sulla raccolta di tenant, è tempo di esaminare alcune delle risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco dei server SQL e aprire il server **catalog-&lt;UTENTE&gt;**. Il server di catalogo contiene due database: **tenantcatalog** e **basetenantdb**, ovvero un database *di riferimento* o di esempio vuoto copiato per creare i nuovi tenant.

   ![database](./media/sql-database-saas-tutorial/databases.png)

1. Tornare all'elenco dei server SQL e aprire il server **tenants1-&lt;UTENTE&gt;** che include i database tenant. Ogni database tenant è un database _standard elastico_ in un pool standard da 50 eDTU. Si noti anche che è presente un database per _Red Maple Racing_, ovvero il database tenant di cui è stato eseguito il provisioning in precedenza.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Monitorare il pool

Se il generatore di carico è rimasto in esecuzione per diversi minuti, dovrebbero essere disponibili abbastanza dati per iniziare a esaminare alcune delle funzionalità di monitoraggio incorporate nei pool e nei database.

1. Individuare il server **tenants1-&lt;UTENTE&gt;** e fare clic su **Pool1** per visualizzare l'utilizzo delle risorse per il pool (il generatore di carico è stato eseguito per un'ora nei grafici seguenti):

   ![monitorare il pool](./media/sql-database-saas-tutorial/monitor-pool.png)

Quello che dimostrano chiaramente questi due grafici è quanto i pool elastici e database SQL siano adatti ai carichi di lavoro di applicazioni SaaS. Quattro database, ognuno dei quali con picchi di utilizzo fino a 40 eDTU, possono essere supportati senza problemi in pool da 50 eDTU. Se ne è stato eseguito il provisioning come database autonomi, ciascuno deve essere un S2 (50 DTU) per supportare i picchi. Il costo di quattro database S2 autonomi è circa il triplo del prezzo del pool, senza contare che il pool ha ancora spazio per molti altri database. Nelle situazioni reali, i clienti di database SQL eseguono attualmente fino a 500 database in pool da 200 eDTU. Per altre informazioni, vedere l'[esercitazione sul monitoraggio delle prestazioni](sql-database-saas-tutorial-performance-monitoring.md).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]

> * Come distribuire l'applicazione SaaS Wingtip
> * Informazioni sui server, i pool e i database che costituiscono l'app
> * Come vengono mappati i tenant e i relativi dati con il *catalogo*
> * Come effettuare il provisioning di nuovi tenant
> * Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

È ora possibile continuare provando l'[esercitazione su provisioning e catalogazione](sql-database-saas-tutorial-provision-and-catalog.md)



## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* Per informazioni sui pool elastici, vedere [*Informazioni sul pool elastico di Azure SQL*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* Per informazioni sui processi elastici, vedere [*Gestione dei database cloud con scalabilità orizzontale*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview)
* Per informazioni sulle applicazioni SaaS multi-tenant, vedere [*Modelli di progettazione per le applicazioni SaaS multi-tenant*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)
