---
title: Distribuire un'applicazione SaaS di database multi-tenant partizionato che usa il database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare l'applicazione SaaS di database multi-tenant partizionato Wingtip Tickets che illustra i modelli SaaS usando il database SQL di Azure.
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: billgib;anjangsh
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: genemi
ms.openlocfilehash: a7e6e319fb2fa8fee762055b625427403d14d679
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione di database multi-tenant partizionato che usa il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare un'applicazione SaaS di database multi-tenant di esempio denominata Wingtip Tickets. L'app Wingtip è stata progettata per illustrare le funzionalità del database SQL di Azure che semplificano l'implementazione di scenari SaaS.

Questa implementazione di Wingtip usa un modello di database multi-tenant partizionato in base all'identificatore del tenant. Dati del tenant viene distribuiti a un database specifico in base ai valori dell'identificatore di tenant. Indipendentemente dal numero di tenant presenti in un determinato database, tutti i database sono multi-tenant, nel senso che gli schemi di tabella includono un identificatore di tenant. 

Questo modello consente di archiviare uno o più tenant in ogni partizione o database. È possibile ottimizzare il modello per la riduzione dei costi, consentendo la condivisione di ogni database tra più tenant, oppure per l'isolamento, configurando un solo tenant per ogni archivio di database. La scelta di ottimizzazione può essere effettuata in modo indipendente per ogni tenant, sia quando questo viene archiviato per la prima volta sia in un secondo momento. L'applicazione è stata progettata in modo da funzionare correttamente in entrambi i casi.

#### <a name="app-deploys-quickly"></a>Distribuzione rapida dell'app

La sezione di distribuzione che segue fornisce il blu **Distribuisci in Azure** pulsante. Quando si fa clic sul pulsante, la distribuzione dell'app Wingtip viene completata entro cinque minuti. L'app Wingtip viene eseguita nel cloud di Azure e usa il database SQL di Azure. Wingtip viene distribuita nella sottoscrizione di Azure. Si ha così l'accesso completo per l'uso dei singoli componenti dell'applicazione.

L'applicazione viene distribuita con i dati relativi a tre tenant di esempio, archiviati insieme in un unico database multi-tenant.

Chiunque può scaricare il codice sorgente c# e PowerShell per i ticket Wingtip dal [relativo repository GitHub][link-github-wingtip-multitenantdb-55g].

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

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

- È stata installata la versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuire l'app Wingtip Tickets

#### <a name="plan-the-names"></a>Pianificare i nomi

Nei passaggi di questa sezione sono disponibili due posizioni in cui è necessario immettere i nomi non come un *utente* e per il nuovo *gruppo di risorse*. Per un utente denominato *Ann Finley*, è consigliabile i nomi seguenti:
- *Utente:* &nbsp; **af1** &nbsp; *(proprio iniziali, più una cifra.)*
- *Gruppo di risorse:* &nbsp; **wingtip af1** &nbsp; *(è consigliabile tutti in lettere minuscole. Aggiungere un segno meno, quindi il nome utente.)*

Scegliere i nomi di ora e annotare queste istruzioni.

#### <a name="steps"></a>Passaggi

1. Fare clic su blu seguente **Distribuisci in Azure** pulsante.
    - Verrà aperto il portale di Azure con il modello di distribuzione SaaS Wingtip Tickets.

    [![Pulsante per distribuire in Azure.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

2. Immettere i valori di parametro obbligatorio per la distribuzione.

    > [!IMPORTANT]
    > Per questa dimostrazione, si utilizza qualsiasi preesistente gruppi di risorse, server o i pool. Al contrario, scegliere **creare un nuovo gruppo di risorse**. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.
    > Non utilizzare l'applicazione o le risorse che crea per la produzione. Alcuni aspetti di autenticazione e le impostazioni firewall del server, sono intenzionalmente non protette nell'app per facilitare la dimostrazione.

    - Per **Gruppo di risorse**, selezionare **Crea nuovo** e quindi specificare un valore in **Nome**, rispettando la distinzione tra maiuscole e minuscole.
        - Selezionare un **percorso** nell'elenco a discesa.
    - Per **utente** -si consiglia di scegliere un breve **utente** valore.

3. **Distribuire l'applicazione**.

    - Fare clic per accettare i termini e le condizioni.
    - Fare clic su **Acquista**.

4. Monitorare lo stato della distribuzione facendo clic su **Notifiche**, l'icona a forma di campanella a destra della casella di ricerca. La distribuzione dell'app Wingtip richiede circa cinque minuti.

   ![distribuzione completata](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Scaricare e sbloccare gli script di gestione

Durante la distribuzione dell'applicazione, scaricare gli script di gestione e il codice sorgente dell'applicazione.

> [!NOTE]
> Il contenuto eseguibile (script, DLL) può essere bloccato da Windows quando vengono scaricati da un'origine esterna e si estratti i file zip. Quando si estraggono gli script da un file con estensione zip, seguire questa procedura per sbloccare il file prima di estrarlo. Lo sblocco di file con estensione zip, consente di garantire che gli script possono essere eseguiti.

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

1. Apri... \\Moduli di formazione\\*UserConfig.psm1* nel *PowerShell ISE*.
2. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
3. Salvare le modifiche.

I valori impostati in questo file vengono utilizzati da tutti gli script, pertanto è importante che siano precise. Se si ridistribuisce l'app, è necessario scegliere valori diversi per l'utente e gruppo di risorse. Aggiornare quindi il file UserConfig.psm1 nuovamente con i nuovi valori.

## <a name="run-the-application"></a>Eseguire l'applicazione

Nell'app Wingtip, i tenant sono eventi. Un evento può essere hall insieme, un club sportivi o qualsiasi altra posizione che ospita gli eventi. Il tipo di eventi registrato nella Wingtip come clienti e per ogni evento viene generato un identificatore di tenant. Per ogni sessione sono elencati i relativi eventi futuri in Wingtip, in modo pubblico possibile acquistare il ticket agli eventi.

Per ogni sessione ottiene un'app web personalizzati per elencare gli eventi e vendere il ticket. Ogni app web è isolato dagli altri tenant e indipendenti. Internamente nel Database di SQL Azure, ogni i dati per ogni tenant vengono archiviati in un database partizionato multi-tenant, per impostazione predefinita. Tutti i dati vengono contrassegnati con l'identificatore del tenant.

Un centro **Hub eventi** pagina Web viene fornito un elenco di collegamenti per i tenant nella distribuzione in uso. Attenersi alla seguente procedura per provare il **Hub eventi** pagina Web e un'app web singoli:

1. Aprire la pagina **Events Hub** (Hub eventi) nel Web browser:
    - http://Events.Wingtip. &lt;Utente&gt;. trafficmanager.net &nbsp; *(sostituire &lt;utente&gt; con valore utente della distribuzione.)*

    ![Hub eventi](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Fare clic su **Fabrikam Jazz Club** in **Events Hub** (Hub eventi).

   ![Eventi](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Gestione traffico di Azure

Per controllare la distribuzione delle richieste in ingresso, l'app Wingtip Usa [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). La pagina di eventi per ogni tenant include il nome del tenant nell'URL. Ogni URL include anche il valore di utente specifico. Ogni URL rispetta il formato illustrato utilizzando la procedura seguente:

- http://events.wingtip.&lt;UTENTE&gt;.trafficmanager.net/*fabrikamjazzclub*

1. L'app eventi analizza il nome del tenant dall'URL. Il nome del tenant è *fabrikamjazzclub* nell'URL di esempio precedente.
2. L'app esegue quindi l'hashing per creare una chiave per accedere a un catalogo tramite il nome del tenant [gestione mappa partizioni](sql-database-elastic-scale-shard-map-management.md).
3. L'app trova la chiave nel catalogo e ottiene la posizione del database del tenant corrispondente.
4. L'app Usa le informazioni sul percorso per individuare e accedere a un database che contiene tutti i dati per il tenant.

#### <a name="events-hub"></a>Hub di eventi

1. Il **Hub eventi** Elenca tutti i tenant che vengono registrati nel catalogo e i relativi eventi.
2. **e** usa i metadati estesi nel catalogo per recuperare il nome del tenant associato a ogni mapping in modo da generare gli URL.

In un ambiente di produzione, è in genere creare un record DNS CNAME [puntare un dominio internet della società](../traffic-manager/traffic-manager-point-internet-domain.md) per il profilo di gestione traffico.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Dopo la distribuzione, è possibile usare l'app. Lo script di PowerShell *Demo-LoadGenerator* avvia un carico di lavoro per ogni tenant. Il carico del mondo reale in molte app SaaS è generalmente sporadico e imprevedibile. Per simulare questo tipo di carico, il generatore produce un carico distribuito tra tutti i tenant. Il carico include picchi casuali su ogni tenant che si verificano a intervalli casuali. Il modello di carico si manifesta completamente solo dopo alcuni minuti. È pertanto consigliabile lasciare agire il generatore per almeno tre o quattro minuti prima di monitorare il carico.

1. In *PowerShell ISE* aprire lo script \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Premere **F5** per eseguire lo script e avviare il generatore di carico (lasciare i valori dei parametri predefiniti per il momento).

Lo script *Demo-LoadGenerator.ps1* apre un'altra sessione di PowerShell in cui viene eseguito il generatore di carico. In questa sessione il generatore di carico viene eseguito come attività in primo piano che richiama i processi in background di generazione del carico, uno per ogni tenant.

Dopo l'avvio dell'attività in primo piano, il generatore rimane in uno stato di richiamo di processo. L'attività avvia altri processi in background per i nuovi tenant di cui viene successivamente effettuato il provisioning.

Se si chiude la sessione di PowerShell, tutti i processi vengono arrestati.

Può essere opportuno riavviare la sessione del generatore di carico per usare valori di parametro diversi. In questo caso, chiudere la sessione di generazione di PowerShell ed eseguire nuovamente lo script *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Effettuare il provisioning di un nuovo tenant nel database partizionato

La distribuzione iniziale include tre tenant di esempio nel database *Tenants1*. Si crea un altro tenant e osservare gli effetti sull'applicazione distribuita. In questo passaggio, si preme un tasto per creare un nuovo tenant:

1. Apri... \\Moduli di formazione\\il provisioning e catalogo\\*Demo ProvisionTenants.ps1* nel *PowerShell ISE*.
2. Premere **F5** (non **F8**) per eseguire lo script (lasciare i valori predefiniti per il momento).

   > [!NOTE]
   > È necessario eseguire gli script di PowerShell solo premendo il **F5** chiave, non premendo **F8** per l'esecuzione di una parte selezionata dello script. Il problema con **F8** è che il *$PSScriptRoot* variabile non viene valutata. Questa variabile è necessario per molti script per spostarsi di cartelle, o di richiamare altri script o per importare i moduli.

Il nuovo tenant Red Maple Racing viene aggiunto al database *Tenants1* e registrato nel catalogo. Nel browser viene visualizzato il sito degli **eventi** del nuovo tenant per la vendita di biglietti:

![Nuovo tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aggiornare il **Hub eventi**, e al nuovo tenant viene visualizzato nell'elenco.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Effettuare il provisioning di un nuovo tenant in un database autonomo

Il modello multi-tenant partizionato consente di scegliere se eseguire il provisioning di un nuovo tenant in un database che contiene altri tenant o in un database di un proprio. Un tenant isolato in un proprio database goda i vantaggi seguenti:
- Le prestazioni del database del tenant possono essere gestite senza la necessità di compromettere con le esigenze di altri tenant.
- Se necessario, il database può essere ripristinato a un punto precedente nel tempo, perché altri tenant non sarebbero interessate.

È possibile scegliere di mettere i clienti di prova gratuito, o ai clienti di economia, in database multi-tenant. È possibile inserire ogni tenant premium nel proprio database dedicato. Se si crea un numero elevato di database che contengono un solo tenant, è possibile gestire tali tutti insieme in un pool elastico per ottimizzare i costi delle risorse.

Successivamente, è il provisioning di un altro tenant, l'ora nel relativo database:

1. In... \\Moduli learning\\il provisioning e catalogo\\*Demo ProvisionTenants.ps1*, modificare *$TenantName* per **a Salix Salsa**, *$VenueType* a **danza** e *$Scenario* a **2**.

2. Premere **F5** per eseguire nuovamente lo script.
    - Questo **F5** premere esegue il provisioning al nuovo tenant in un database separato. Il database e il tenant vengono registrati nel catalogo e quindi viene visualizzata la pagina degli eventi del tenant nel browser.

   ![Pagina degli eventi di Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Scorrere fino alla parte inferiore della pagina. Nel banner viene visualizzato il nome del database in cui sono archiviati i dati del tenant.

3. Aggiornare il **Hub eventi** i due nuovi tenant non viene più visualizzato nell'elenco.

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

Se il generatore di carico è stata eseguita per diversi minuti, dati di telemetria sufficienti è disponibile per la ricerca nel database di monitoraggio di funzionalità incorporate nel portale di Azure.

1. Passare al server **tenants1-mt&lt;UTENTE&gt;** e fare clic su **tenants1** per visualizzare l'utilizzo delle risorse del database che include quattro tenant. Ogni tenant è sottoposto sporadicamente a un carico pesante dal generatore di carico:

   ![monitorare tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Il grafico relativo all'utilizzo delle DTU illustra chiaramente come un database multi-tenant può supportare un carico di lavoro imprevisto su più tenant. In questo caso, il generatore di carico applica sporadicamente un carico di circa 30 DTU a ogni tenant. Questo carico equivale al 60% di utilizzo di un database da 50 DTU. Si verificano picchi superiori al 60% quando il carico viene applicato contemporaneamente a più di un tenant.

2. Individuare il **tenants1 mt&lt;utente&gt;**  server e scegliere il **salixsalsa** database. È possibile visualizzare l'utilizzo delle risorse nel database che contiene un solo tenant.

   ![database salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Il generatore di carico applica un carico simile a ogni tenant, indipendentemente dal database in cui si trova. Con un solo tenant nel database **salixsalsa**, è possibile osservare che il database è riuscito a sostenere un carico decisamente superiore rispetto al database con più tenant. 

#### <a name="resource-allocations-vary-by-workload"></a>Allocazioni variano a seconda del carico di lavoro

Talvolta un database di multi-tenant richiede più risorse per prestazioni ottimali rispetto a un singolo tenant database, ma non sempre. L'allocazione delle risorse ottimale dipende le caratteristiche del carico di lavoro specifico per i tenant nel sistema.

I carichi di lavoro generati da script il generatore di carico sono solo a scopo illustrativo.

## <a name="additional-resources"></a>Risorse aggiuntive

- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).

- Per ulteriori informazioni sui pool elastico, vedere:
    - [In un pool elastico consentono di gestire e scalare più database SQL di Azure](sql-database-elastic-pool.md)
    - [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> - Come distribuire l'applicazione di Database di Wingtip ticket SaaS multi-tenant.
> - Sul server e database che costituiscono l'app.
> - I tenant vengono mappati ai dati con il *catalogo*.
> - Come effettuare il provisioning di nuovi tenant, in un database multi-tenant e in un database a tenant singolo
> - Come visualizzare l'utilizzo di pool per monitorare l'attività di tenant.
> - Come eliminare le risorse di esempio per interrompere la fatturazione correlata

È ora possibile continuare provando l'[esercitazione su provisioning e catalogazione](sql-database-saas-tutorial-provision-and-catalog.md).


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

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Pulsante per la distribuzione in Azure."

