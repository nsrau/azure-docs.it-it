---
title: Esercitazione sulle app SaaS multi-tenant - Database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare l'applicazione SaaS multi-tenant Wingtip Tickets, che illustra il modello di database per tenant e altri modelli SaaS usando il database SQL di Azure.
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
ms.date: 11/10/2017
ms.author: sstein
ms.openlocfilehash: 9b1ae219eb1278b818e3e1d4237d04fe54c980ec
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Distribuire ed esplorare un'applicazione SaaS multi-tenant che usa il modello di database per tenant con il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare l'applicazione SaaS di database per tenant Wingtip Tickets. L'applicazione usa un modello di database per tenant per archiviare i dati di più tenant. L'applicazione è progettata per dimostrare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS.

Cinque minuti dopo aver fatto clic sul pulsante *Deploy to Azure* (Distribuisci in Azure) riportato di seguito, si avrà a disposizione un'applicazione SaaS multi-tenant, che usa database SQL, in esecuzione nel cloud. L'applicazione viene distribuita con tre tenant di esempio, ognuno con un database proprio, tutti distribuiti in un pool elastico SQL. L'app viene distribuita nella sottoscrizione di Azure e consente l'accesso completo per esaminare e utilizzare i singoli componenti dell'applicazione. Gli script di gestione e il codice sorgente dell'applicazione sono disponibili nel repository WingtipTicketsSaaS-DbPerTenant di GitHub.


In questa esercitazione si apprenderà:

> [!div class="checklist"]

> * Come distribuire l'applicazione SaaS Wingtip
> * Dove ottenere il codice sorgente e gli script di gestione dell'applicazione
> * Informazioni sui server, i pool e i database che costituiscono l'app
> * Come vengono mappati i tenant e i relativi dati con il *catalogo*
> * Come effettuare il provisioning di un nuovo tenant
> * Come monitorare l'attività dei tenant nell'app

Per esplorare i vari modelli di progettazione e gestione SaaS, è disponibile una [serie di esercitazioni correlate](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) basate su questa distribuzione iniziale. Esaminando le varie esercitazioni, approfondire gli script forniti e analizzare le modalità di implementazione dei diversi modelli SaaS. Analizzare in dettaglio i vari passaggi degli script in ogni esercitazione per comprendere in modo più approfondito come implementare le molte funzionalità di database SQL che semplificano lo sviluppo di applicazioni SaaS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuire l'applicazione SaaS Wingtip Tickets

Distribuire l'app:

1. Fare clic sul pulsante **Distribuisci in Azure** per aprire il modello di distribuzione dell'applicazione SaaS di database per tenant Wingtip Tickets nel portale di Azure. Nel modello è necessario specificare i valori di due parametri, un nome per un nuovo gruppo di risorse e un nome utente che consente di distinguere questa distribuzione da altre distribuzioni dell'app SaaS di database per tenant Wingtip Tickets. Il passaggio seguente fornisce dettagli per l'impostazione di questi valori.

   Assicurarsi di annotare i valori esatti utilizzati che sarà necessario immettere in un file di configurazione in un secondo momento.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Immettere i valori dei parametri necessari per la distribuzione:

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. **Creare un nuovo gruppo di risorse**. Non usare gruppi di risorse, server o pool esistenti. Non usare questa applicazione, gli script o le risorse distribuite per la produzione. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.

    * **Gruppo di risorse**: selezionare **Crea nuovo** e specificare il **nome** del nuovo gruppo di risorse. 
    * **Percorso**: selezionare un **percorso** dall'elenco a discesa.
    * **Utente**: alcune risorse richiedono nomi univoci a livello globale. Per garantire l'univocità, ogni volta che si distribuisce l'applicazione fornire un valore per distinguere le risorse create da quelle create da altre distribuzioni dell'applicazione Wingtip. È consigliabile usare un nome breve per **Utente**, come le iniziali seguite da un numero (ad esempio, *af1*), e quindi includerlo nel nome del gruppo di risorse (ad esempio, *wingtip-af1*). Il parametro **Utente** può contenere solo lettere, numeri e trattini (senza spazi). Il primo e ultimo carattere devono essere una lettera o un numero (si consiglia di usare solo lettere minuscole).


1. **Distribuire l'applicazione**.

    * Fare clic per accettare i termini e le condizioni.
    * Fare clic su **Acquista**.

1. Monitorare lo stato di distribuzione facendo clic su **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app SaaS Wingtip Tickets richiede circa cinque minuti.

   ![distribuzione completata](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Scaricare e sbloccare gli script di gestione di Wingtip Tickets

Durante la distribuzione dell'applicazione, scaricare il codice sorgente e gli script di gestione.

> [!IMPORTANT]
> I contenuti eseguibili (script, DLL) possono essere bloccati da Windows quando si scaricano e si estraggono i file ZIP da un'origine esterna. Quando si estraggono gli script da un file ZIP, seguire questa procedura per sbloccare il file ZIP prima di estrarlo. In questo modo sarà possibile eseguire gli script.

1. Passare al [repository WingtipTicketsSaaS-DbPerTenant di GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Fare clic su **Clona o scarica**.
1. Fare clic su **Scarica ZIP** e salvare il file.
1. Fare clic con il pulsante destro del mouse sul file **WingtipTicketsSaaS-DbPerTenant-master.zip** e scegliere **Proprietà**.
1. Nella scheda **Generale** selezionare **Annulla blocco** e fare clic su **Applica**.
1. Fare clic su **OK**.
1. Estrarre i file.

Gli script si trovano nella cartella *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aggiornare il file di configurazione utente per la distribuzione

Prima di eseguire uno script, impostare i valori relativi al *gruppo di risorse* e all'*utente* in **UserConfig.psm1**. Impostare queste variabili sui valori usati durante la distribuzione.

1. In *PowerShell ISE* aprire ...\\Learning Modules\\*UserConfig.psm1* 
1. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
1. Salvare le modifiche.

Sono presenti riferimenti a questi valori in quasi ogni script.

## <a name="run-the-application"></a>Eseguire l'applicazione

L'app presenta sedi di eventi, come sale concerto, jazz club e club sportivi. Queste sedi vengono registrate come clienti (o tenant) di Wingtip Tickets, per presentare elenchi di eventi e vendere i biglietti con facilità. Ogni sede di eventi ottiene un sito Web personalizzato per gestire ed elencare gli eventi e vendere i biglietti, in modo indipendente e isolato dagli altri tenant. Dietro le quinte, ogni tenant ottiene un database SQL distribuito in un pool elastico SQL.

Una pagina centrale **Events Hub** (Hub eventi) visualizza un elenco di collegamenti ai tenant nella distribuzione.

1. Aprire la pagina _Events Hub_ (Hub eventi) nel Web browser: http://events.wingtip-dpt.&lt;UTENTE&gt;.trafficmanager.net sostituendo &lt;UTENTE&gt; con il valore relativo all'utente della distribuzione:

    ![Hub eventi](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Fare clic su **Fabrikam Jazz Club** in *Events Hub* (Hub eventi).

   ![Eventi](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


Per controllare la distribuzione delle richieste in ingresso, l'app usa [*Gestione traffico di Microsoft Azure*](../traffic-manager/traffic-manager-overview.md). Le pagine degli eventi, specifiche del tenant, richiedono che gli URL includano i nomi del tenant. Tutti gli URL dei tenant includono il valore specifico relativo all'*utente* nel formato http://events.wingtipp-dpt.&lt;UTENTE&gt;.trafficmanager.net/*fabrikamjazzclub*. L'app degli eventi analizza il nome del tenant dall'URL e lo usa per creare una chiave di accesso a un catalogo implementato usando il [*gestore delle mappe partizioni*](sql-database-elastic-scale-shard-map-management.md). Il catalogo esegue il mapping della chiave alla posizione del database del tenant. L'**hub eventi** usa i metadati estesi nel catalogo per recuperare il nome del tenant associato a ogni database che fornisce l'elenco di URL.

In ambiente di produzione si crea in genere un record DNS CNAME per [*impostare un dominio Internet aziendale in modo che punti*](../traffic-manager/traffic-manager-point-internet-domain.md) al profilo di Gestione traffico.

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

La distribuzione iniziale crea tre tenant di esempio, ma è possibile crearne un altro per valutarne l'impatto sull'applicazione distribuita. Il flusso di lavoro dei tenant di provisioning SaaS Wingtip Tickets è descritto in dettaglio nell'[esercitazione su provisioning e catalogazione](saas-dbpertenant-provision-and-catalog.md). Questo passaggio descrive come creare rapidamente un nuovo tenant.

1. In *PowerShell ISE* aprire ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
1. Premere **F5** per eseguire lo script (per il momento lasciare i valori predefiniti).

   > [!NOTE]
   > Molti script dell'applicazione SaaS Wingtip usano *$PSScriptRoot* per l'esplorazione delle cartelle o per chiamare funzioni in altri script. Questa variabile viene valutata solo quando viene eseguito lo script completo premendo **F5**.  L'evidenziazione e l'esecuzione di una selezione (**F8**) può causare errori, quindi premere **F5** per l'esecuzione di script.

Il nuovo database tenant viene creato in un pool elastico SQL, inizializzato e registrato nel catalogo. Al termine del provisioning, nel browser viene visualizzato il sito degli *eventi* del nuovo tenant:

![Nuovo tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aggiornare l'*hub eventi* e verificare che il nuovo tenant sia incluso nell'elenco.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Esplorare i server, i pool e i database tenant

Dopo aver avviato l'esecuzione di un carico sulla raccolta di tenant, è tempo di esaminare alcune delle risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco dei server SQL e aprire il server **catalog-dpt-&lt;UTENTE&gt;**. Il server di catalogo contiene due database, **tenantcatalog** e **basetenantdb**, ovvero un database modello copiato per creare nuovi tenant.

   ![database](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Tornare all'elenco dei server SQL e aprire il server **tenants1-dpt-&lt;UTENTE&gt;** che include i database tenant. Ogni database tenant è un database _standard elastico_ in un pool standard da 50 eDTU. Si noti anche che è presente un database per _Red Maple Racing_, ovvero il database tenant di cui è stato eseguito il provisioning in precedenza.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorare il pool

Se il generatore di carico è rimasto in esecuzione per diversi minuti, dovrebbero essere disponibili abbastanza dati per iniziare a esaminare alcune delle funzionalità di monitoraggio incorporate nei pool e nei database.

1. Passare al server **tenants1-dpt-&lt;UTENTE&gt;** e fare clic su **Pool1** per visualizzare l'utilizzo delle risorse per il pool. Nei grafici seguenti il generatore di carico è stato eseguito per un'ora:

   ![monitorare il pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

Il grafico superiore mostra l'utilizzo di eDTU del pool, mentre quello inferiore mostra l'utilizzo di eDTU dei primi cinque database nel pool.  Quello che dimostrano chiaramente questi due grafici è quanto i pool elastici e database SQL siano adatti ai carichi di lavoro di applicazioni SaaS. Quattro database, ognuno dei quali con picchi di utilizzo fino a 40 eDTU, possono essere supportati senza problemi in pool da 50 eDTU. Se ne è stato eseguito il provisioning come database autonomi, ciascuno deve essere un S2 (50 DTU) per supportare i picchi. Il costo di quattro database S2 autonomi è circa il triplo del prezzo del pool, senza contare che il pool ha ancora spazio per molti altri database. Nelle situazioni reali, i clienti di database SQL eseguono attualmente fino a 500 database in pool da 200 eDTU. Per altre informazioni, vedere l'[esercitazione sul monitoraggio delle prestazioni](saas-dbpertenant-performance-monitoring.md).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]

> * Come distribuire l'applicazione SaaS Wingtip Tickets
> * Informazioni sui server, i pool e i database che costituiscono l'app
> * Come vengono mappati i tenant e i relativi dati con il *catalogo*
> * Come effettuare il provisioning di nuovi tenant
> * Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> * Come eliminare le risorse di esempio per interrompere la fatturazione correlata

È ora possibile continuare provando l'[esercitazione su provisioning e catalogazione](saas-dbpertenant-provision-and-catalog.md).



## <a name="additional-resources"></a>Risorse aggiuntive

* Altre [esercitazioni basate sull'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* Per informazioni sui pool elastici, vedere [*Informazioni sul pool elastico di Azure SQL*](sql-database-elastic-pool.md)
* Per informazioni sui processi elastici, vedere [*Gestione dei database cloud con scalabilità orizzontale*](sql-database-elastic-jobs-overview.md)
* Per informazioni sulle applicazioni SaaS multi-tenant, vedere [*Modelli di progettazione per le applicazioni SaaS multi-tenant*](saas-tenancy-app-design-patterns.md)
