---
title: Distribuire un'applicazione SaaS di database multi-tenant partizionato che usa il database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare l'applicazione SaaS di database multi-tenant partizionato Wingtip Tickets che illustra i modelli SaaS usando il database SQL di Azure.
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: billgib;MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: sstein
ms.openlocfilehash: 1ef4355f7234bc6a534d21a57fa52b480983b99b
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione di database multi-tenant partizionato che usa il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare un'applicazione SaaS di database multi-tenant di esempio denominata Wingtip Tickets. L'app Wingtip è stata progettata per illustrare le funzionalità del database SQL di Azure che semplificano l'implementazione di scenari SaaS.

Questa implementazione di Wingtip usa un modello di database multi-tenant partizionato in base all'identificatore del tenant. I dati dei tenant vengono distribuiti a un determinato database secondo i valori degli identificatori. Indipendentemente dal numero di tenant presenti in un determinato database, tutti i database sono multi-tenant, nel senso che gli schemi di tabella includono un identificatore di tenant. 

Questo modello consente di archiviare uno o più tenant in ogni partizione o database. È possibile ottimizzare il modello per la riduzione dei costi, consentendo la condivisione di ogni database tra più tenant, oppure per l'isolamento, configurando un solo tenant per ogni archivio di database. La scelta di ottimizzazione può essere effettuata in modo indipendente per ogni tenant, sia quando questo viene archiviato per la prima volta sia in un secondo momento. L'applicazione è stata progettata in modo da funzionare correttamente in entrambi i casi.

#### <a name="app-deploys-quickly"></a>Distribuzione rapida dell'app

La sezione seguente relativa alla distribuzione prevede l'uso del pulsante **Distribuisci in Azure**. Quando si fa clic sul pulsante, la distribuzione dell'app Wingtip viene completata entro cinque minuti. L'app Wingtip viene eseguita nel cloud di Azure e usa il database SQL di Azure. Wingtip viene distribuita nella sottoscrizione di Azure. Si ha così l'accesso completo per l'uso dei singoli componenti dell'applicazione.

L'applicazione viene distribuita con i dati relativi a tre tenant di esempio, archiviati insieme in un unico database multi-tenant.

Tutti gli utenti possono scaricare il codice sorgente C# e PowerShell per Wingtip Tickets dal [repository di GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Informazioni in questa esercitazione

> [!div class="checklist"]

> - Come distribuire l'applicazione SaaS Wingtip
> - Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> - Informazioni su server e database che costituiscono l'app
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di un nuovo tenant
> - Come monitorare l'attività dei tenant nell'app

È disponibile una serie di esercitazioni correlate, basate su questa distribuzione iniziale, che consentono di esaminare vari modelli di progettazione e gestione SaaS. Nel corso delle esercitazioni gli utenti sono incoraggiati a eseguire gli script passo per passo in modo da osservare la modalità di implementazione dei diversi modelli SaaS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

- È stata installata la versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuire l'app Wingtip Tickets

1. Fare clic sul pulsante **Distribuisci in Azure** illustrato di seguito.
    - Verrà aperto il portale di Azure con il modello di distribuzione SaaS Wingtip Tickets. Nel modello è necessario specificare i valori di due parametri, un nome per un nuovo gruppo di risorse e un valore relativo a un utente che distingue questa distribuzione da altre distribuzioni dell'app. Il passaggio seguente include i dettagli per l'impostazione di questi valori.
        - Prendere nota dei valori esatti specificati poiché saranno necessari in un secondo momento per un file di configurazione.

    [![Pulsante per la distribuzione in Azure][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Immettere i valori dei parametri necessari per la distribuzione.

    > [!IMPORTANT]
    > Per alcune impostazioni di autenticazione e per quelle relative al firewall del server sono stati scelti intenzionalmente valori non sicuri allo scopo di facilitare la dimostrazione. Scegliere **Crea un nuovo gruppo di risorse** e non usare gruppi di risorse, server o pool esistenti. Non utilizzare l'applicazione o le risorse che crea per la produzione. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.

    Nei nomi delle risorse è preferibile usare solo lettere minuscole, numeri e trattini.

    - Per **Gruppo di risorse**, selezionare **Crea nuovo** e quindi specificare un valore in **Nome**, rispettando la distinzione tra maiuscole e minuscole.
        - Per il nome del gruppo di risorse è consigliabile usare solo lettere minuscole.
        - È inoltre consigliabile aggiungere un trattino, seguito dalle proprie iniziali e quindi da una cifra, ad esempio *wingtip-af1*.
        - Selezionare un **percorso** nell'elenco a discesa.
    - Per **Utente**, è consigliabile specificare un valore di **Utente** breve, come le proprie iniziali seguite da una cifra, ad esempio *af1*.

3. **Distribuire l'applicazione**.

    - Fare clic per accettare i termini e le condizioni.
    - Fare clic su **Acquista**.

4. Monitorare lo stato della distribuzione facendo clic su **Notifiche**, l'icona a forma di campanella a destra della casella di ricerca. La distribuzione dell'app Wingtip richiede circa cinque minuti.

   ![distribuzione completata](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Scaricare e sbloccare gli script di gestione

Durante la distribuzione dell'applicazione, scaricare gli script di gestione e il codice sorgente dell'applicazione.

> [!IMPORTANT]
> I contenuti eseguibili (script, DLL) possono essere bloccati da Windows quando si scaricano e si estraggono i file ZIP da un'origine esterna. Quando si estraggono gli script da un file con estensione zip, seguire questa procedura per sbloccare il file prima di estrarlo. In questo modo, si ha la certezza che gli script possano essere eseguiti.

1. Passare al [repository WingtipTicketsSaaS-MultiTenantDb di GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb).
2. Fare clic su **Clona o scarica**.
3. Fare clic su **Scarica ZIP** e salvare il file.
4. Fare clic con il pulsante destro del mouse su **WingtipTicketsSaaS-MultiTenantDb-master.zip** e scegliere **Proprietà**.
5. Nella scheda **Generale** selezionare **Annulla blocco** e fare clic su **Applica**.
6. Fare clic su **OK**.
7. Estrarre i file.

Gli script si trovano nella cartella *..\\WingtipTicketsSaaS-MultiTenantDb-master\\Learning Modules\\*.

## <a name="update-the-configuration-file-for-this-deployment"></a>Aggiornare il file di configurazione per la distribuzione

Prima di eseguire qualsiasi script, impostare i valori *gruppo di risorse* e *utente* in **UserConfig.psm1**. Impostare queste variabili sugli stessi valori usati durante la distribuzione.

1. Aprire ...\\Learning Modules\\*UserConfig.psm1* in *PowerShell ISE*
2. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
3. Salvare le modifiche.

I valori impostati in questo file vengono usati da tutti gli script ed è quindi importante che siano precisi. Se si ridistribuisce l'app, assicurarsi di scegliere un nome di gruppo di risorse e un valore di utente differenti. Aggiornare quindi il file UserConfig con i nuovi valori.

## <a name="run-the-application"></a>Eseguire l'applicazione

L'app presenta sedi di eventi, come sale concerto, jazz club e club sportivi. Queste sedi vengono registrate come clienti (o tenant) della piattaforma Wingtip, per presentare elenchi di eventi e vendere i biglietti con facilità. Ogni sede di eventi ottiene un'app Web personalizzata per gestire ed elencare gli eventi e vendere i biglietti, in modo indipendente e isolato dagli altri tenant. A livello sottostante, i dati di ogni tenant vengono archiviati per impostazione predefinita in un database multi-tenant partizionato.

Un **hub eventi** centrale visualizza un elenco di collegamenti ai tenant nella specifica distribuzione.

1. Aprire la pagina *Events Hub* (Hub eventi) nel Web browser:
    - http://events.wingtip.&lt;UTENTE&gt;.trafficmanager.net &nbsp; *(sostituire UTENTE con il valore relativo all'utente della distribuzione)*

    ![Hub eventi](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Fare clic su **Fabrikam Jazz Club** in *Events Hub* (Hub eventi).

   ![Events](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

Per controllare la distribuzione delle richieste in ingresso, l'app usa [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). Negli URL delle pagine degli eventi, che sono specifiche del tenant, sono inclusi il nome del tenant e il valore specifico relativo all'utente nel formato seguente:

- http://events.wingtip.&lt;UTENTE&gt;.trafficmanager.net/*fabrikamjazzclub*
 
L'app degli eventi analizza il nome del tenant dall'URL e ne esegue l'hash per creare una chiave di accesso a un catalogo usando il [gestore delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md). Il catalogo esegue il mapping della chiave alla posizione del database del tenant. L'**hub eventi** elenca tutti i tenant registrati nel catalogo **e** usa i metadati estesi nel catalogo per recuperare il nome del tenant associato a ogni mapping in modo da generare gli URL.

In ambiente di produzione si crea in genere un record DNS CNAME per [impostare un dominio Internet aziendale in modo che punti](../traffic-manager/traffic-manager-point-internet-domain.md) al profilo di Gestione traffico.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Dopo la distribuzione, è possibile usare l'app. Lo script di PowerShell *Demo-LoadGenerator* avvia un carico di lavoro per ogni tenant. Il carico del mondo reale in molte app SaaS è generalmente sporadico e imprevedibile. Per simulare questo tipo di carico, il generatore produce un carico distribuito tra tutti i tenant. Il carico include picchi casuali su ogni tenant che si verificano a intervalli casuali. Il modello di carico si manifesta completamente solo dopo alcuni minuti. È pertanto consigliabile lasciare agire il generatore per almeno tre o quattro minuti prima di monitorare il carico.

1. In *PowerShell ISE* aprire lo script \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Premere **F5** per eseguire lo script e avviare il generatore di carico (lasciare i valori dei parametri predefiniti per il momento).

> [!IMPORTANT]
> Lo script *Demo-LoadGenerator.ps1* apre un'altra sessione di PowerShell in cui viene eseguito il generatore di carico. In questa sessione il generatore di carico viene eseguito come attività in primo piano che richiama i processi in background di generazione del carico, uno per ogni tenant.

Dopo l'avvio dell'attività in primo piano, il generatore rimane in uno stato di richiamo di processo. L'attività avvia altri processi in background per i nuovi tenant di cui viene successivamente effettuato il provisioning.

Se si chiude la sessione di PowerShell, tutti i processi vengono arrestati.

Può essere opportuno riavviare la sessione del generatore di carico per usare valori di parametro diversi. In questo caso, chiudere la sessione di generazione di PowerShell ed eseguire nuovamente lo script *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Effettuare il provisioning di un nuovo tenant nel database partizionato

La distribuzione iniziale include tre tenant di esempio nel database *Tenants1*. Si creerà un altro tenant per verificarne l'impatto sull'applicazione distribuita. Questo passaggio descrive come creare rapidamente un nuovo tenant.

1. Aprire ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionTenants.ps1* in *PowerShell ISE*.
2. Premere **F5** per eseguire lo script (per il momento lasciare i valori predefiniti).

   > [!NOTE]
   > Molti script dell'applicazione SaaS Wingtip Tickets usano *$PSScriptRoot* per consentire l'esplorazione delle cartelle, richiamare altri script o importare moduli. Questa variabile viene valutata solo quando lo script viene eseguito per intero premendo **F5**.  L'evidenziazione e l'esecuzione di una selezione (**F8**) può causare errori, quindi premere **F5** per l'esecuzione di script.

Il nuovo tenant Red Maple Racing viene aggiunto al database *Tenants1* e registrato nel catalogo. Nel browser viene visualizzato il sito degli *eventi* del nuovo tenant per la vendita di biglietti:

![Nuovo tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aggiornare l'*hub eventi* e verificare che il nuovo tenant sia incluso nell'elenco.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Effettuare il provisioning di un nuovo tenant in un database autonomo

Il modello multi-tenant partizionato consente di scegliere se effettuare il provisioning di un nuovo tenant in un database che ne contiene altri oppure in un database autonomo. Un tenant isolato offre il vantaggio di poter archiviare i dati separatamente da quelli di altri tenant. L'isolamento consente di gestire le prestazioni del tenant in modo indipendente dagli altri. Inoltre, per il tenant isolato, è più semplice eseguire il ripristino di dati precedenti. Può essere utile inserire i clienti standard o i sottoscrittori di una prova gratuita in un database multi-tenant e i clienti premium in singoli database. Se si creano numerosi database, ciascuno con un solo tenant, è possibile gestirli tutti insieme in un pool elastico per ottimizzare i costi delle risorse.  

A questo punto si effettua il provisioning di un altro tenant, questa volta in un database autonomo.

1. In ...\\Learning Modules\\Provision and Catalog\*Demo-ProvisionTenants.ps1*, impostare *$TenantName* su **Salix Salsa**, *$VenueType* su **dance** e *$Scenario* su **2**.

2. Premere **F5** per eseguire nuovamente lo script.
    - Premendo F5 si effettua il provisioning del nuovo tenant in un database separato. Il database e il tenant vengono registrati nel catalogo e quindi viene visualizzata la pagina degli eventi del tenant nel browser.

   ![Pagina degli eventi di Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Scorrere fino alla parte inferiore della pagina. Nel banner viene visualizzato il nome del database in cui sono archiviati i dati del tenant.

3. Aggiornare l'hub eventi. Si noterà che i due nuovi tenant sono ora inclusi nell'elenco.



## <a name="explore-the-servers-and-tenant-databases"></a>Esplorare i server e i database tenant

Ora si esamineranno alcune delle risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco dei gruppi di risorse. Aprire il gruppo di risorse creato al momento della distribuzione dell'applicazione.

   ![gruppo di risorse](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Fare clic sul server **catalog-mt&lt;UTENTE&gt;**. Il server di catalogo contiene due database denominati *tenantcatalog* e *basetenantdb*. Il database *basetenantdb* è un database modello vuoto. Viene copiato per creare un nuovo database, da usare per più tenant o per uno solo.

   ![server di catalogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Tornare al gruppo di risorse e selezionare il server *tenants1-mt* che ospita i database tenant.
    - Il database tenants1 è un database multi-tenant in cui sono archiviati i tre tenant originali e il primo tenant che è stato aggiunto. È configurato come database Standard da 50 DTU.
    - Il database **salixsalsa** contiene il sito di eventi di danza Salix Salsa come unico tenant. È configurato per impostazione predefinita come database Standard da 50 DTU.

   ![server di tenant](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Monitorare le prestazioni del database

Se il generatore di carico è rimasto in esecuzione per diversi minuti, sarà disponibile una quantità sufficiente di dati di telemetria per iniziare a esaminare alcune delle funzionalità di monitoraggio dei database integrate nel portale.

1. Passare al server **tenants1-mt&lt;UTENTE&gt;** e fare clic su **tenants1** per visualizzare l'utilizzo delle risorse del database che include quattro tenant. Ogni tenant è sottoposto sporadicamente a un carico pesante dal generatore di carico:

   ![monitorare tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Il grafico relativo all'utilizzo delle DTU illustra chiaramente come un database multi-tenant può supportare un carico di lavoro imprevisto su più tenant. In questo caso, il generatore di carico applica sporadicamente un carico di circa 30 DTU a ogni tenant. Questo carico equivale al 60% di utilizzo di un database da 50 DTU. Si verificano picchi superiori al 60% quando il carico viene applicato contemporaneamente a più di un tenant.

2. Passare al server **tenants1-mt&lt;UTENTE&gt;** e fare clic sul database **salixsalsa** per visualizzare l'utilizzo delle risorse su questo database che contiene un solo tenant.

   ![database salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Il generatore di carico applica un carico simile a ogni tenant, indipendentemente dal database in cui si trova. Con un solo tenant nel database **salixsalsa**, è possibile osservare che il database è riuscito a sostenere un carico decisamente superiore rispetto al database con più tenant. 

> [!NOTE]
> I carichi creati dal generatore sono riportati solo a scopo illustrativo.  Le risorse allocate ai database multi-tenant e a tenant singolo e il numero di tenant che possono essere ospitati in un database multi-tenant dipendono dai modelli di carico di lavoro effettivo dell'applicazione.


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]

> - Come distribuire l'applicazione SaaS di database multi-tenant Wingtip Tickets
> - Informazioni su server e database che costituiscono l'app
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di nuovi tenant, in un database multi-tenant e in un database a tenant singolo
> - Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> - Come eliminare le risorse di esempio per interrompere la fatturazione correlata

È ora possibile continuare provando l'[esercitazione su provisioning e catalogazione](sql-database-saas-tutorial-provision-and-catalog.md).



## <a name="additional-resources"></a>Risorse aggiuntive

- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [*Modelli di progettazione per le applicazioni SaaS multi-tenant*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)








<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: http://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: https://docs.microsoft.com/powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/





<!--  Image references.

[image-deploy-to-azure-blue-48d]: http://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."

-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png

