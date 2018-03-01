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
ms.openlocfilehash: 3bbfdccd020f5efc7510d9688ea38f5e1af4ebde
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application-that-uses-azure-sql-database"></a>Distribuire ed esplorare un'applicazione di database multi-tenant partizionato che usa il database SQL di Azure

Questa esercitazione illustra come distribuire ed esplorare Wingtip Tickets, un'applicazione SaaS multi-tenant di esempio. L'app Wingtip Tickets è stata progettata per illustrare le funzionalità del database SQL di Azure che semplificano l'implementazione di scenari SaaS.

Questa implementazione dell'app Wingtip Tickets usa un modello di database multi-tenant partizionato in base all'identificatore del tenant. I dati dei tenant vengono distribuiti in un determinato database secondo i valori degli identificatori. 

Questo modello consente di archiviare uno o più tenant in ogni partizione o database. È possibile ottimizzare il modello per la riduzione dei costi, consentendo la condivisione di ogni database tra più tenant, oppure per l'isolamento, configurando un solo tenant per ogni archivio di database. La scelta di ottimizzazione può essere effettuata in modo indipendente per ogni tenant, sia quando questo viene archiviato per la prima volta sia in un secondo momento. L'applicazione è stata progettata in modo da funzionare correttamente in entrambi i casi.

#### <a name="app-deploys-quickly"></a>Distribuzione rapida dell'app

L'app viene eseguita nel cloud di Azure e usa il database SQL di Azure. Nella sezione seguente relativa alla distribuzione prevede viene usato il pulsante azzurro **Distribuisci in Azure**. Quando si preme il pulsante, l'app viene distribuita completamente nella sottoscrizione di Azure entro cinque minuti. Si ha così l'accesso completo per l'uso dei singoli componenti dell'applicazione.

L'applicazione viene distribuita con i dati relativi a tre tenant di esempio, archiviati insieme in un unico database multi-tenant.

Tutti gli utenti possono scaricare il codice sorgente C# e PowerShell per Wingtip Tickets dal [repository di GitHub][link-github-wingtip-multitenantdb-55g].

#### <a name="learn-in-this-tutorial"></a>Informazioni in questa esercitazione

> [!div class="checklist"]
> - Come distribuire l'applicazione SaaS Wingtip Tickets
> - Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> - Informazioni su server e database che costituiscono l'app
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di un nuovo tenant
> - Come monitorare l'attività dei tenant nell'app

È disponibile una serie di esercitazioni correlate, basate su questa distribuzione iniziale, che consentono di esaminare vari modelli di progettazione e gestione SaaS. Nel corso delle esercitazioni gli utenti sono incoraggiati a eseguire gli script passo per passo in modo da osservare la modalità di implementazione dei diversi modelli SaaS.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

- È stata installata la versione più recente di Azure PowerShell. Per informazioni dettagliate, vedere [Introduzione ad Azure PowerShell][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>Distribuire l'app Wingtip Tickets

#### <a name="plan-the-names"></a>Pianificare i nomi

Nei passaggi di questa sezione si forniscono un valore *utente* usato per assicurare che i nomi delle risorse siano univoci a livello globale e un nome per il *gruppo di risorse* contenente tutte le risorse create da una distribuzione dell'app. Per un utente di nome *Ann Finley* si consiglia:
- *Utente:* **af1***, ossia le iniziali e un numero. Usare un valore diverso, ad esempio af2, se si distribuisce l'app una seconda volta.*
- *Gruppo di risorse:* **wingtip-dpt-af1** *: wingtip dpt indica che si tratta dell'app con un database per ogni tenant. L'aggiunta del nome utente af1 correla il nome del gruppo di risorse con i nomi delle risorse in esso contenute.*

Scegliere ora i nomi e annotarli. 

#### <a name="steps"></a>Passaggi

1. Fare clic sul pulsante azzurro **Distribuisci in Azure** illustrato di seguito.
    - Verrà aperto il portale di Azure con il modello di distribuzione SaaS Wingtip Tickets.

    [![Pulsante per la distribuzione in Azure][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Immettere i valori dei parametri necessari per la distribuzione.

    > [!IMPORTANT]
    > Per questa dimostrazione non usare gruppi di risorse, server o pool preesistenti. Scegliere invece **Crea un nuovo gruppo di risorse**. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.
    > Non utilizzare l'applicazione o le risorse che crea per la produzione. Per alcuni aspetti dell'autenticazione e per le impostazioni relative al firewall del server sono stati scelti intenzionalmente valori non sicuri nell'app allo scopo di facilitare la dimostrazione.

    - Per **Gruppo di risorse**, selezionare **Crea nuovo** e quindi specificare un valore in **Nome**, rispettando la distinzione tra maiuscole e minuscole.
        - Selezionare un **percorso** nell'elenco a discesa.
    - Per **Utente**: si consiglia di scegliere un valore **Utente** breve.

1. **Distribuire l'applicazione**.

    - Fare clic per accettare i termini e le condizioni.
    - Fare clic su **Acquista**.

1. Monitorare lo stato della distribuzione facendo clic su **Notifiche**, l'icona a forma di campanella a destra della casella di ricerca. La distribuzione dell'app Wingtip richiede circa cinque minuti.

   ![distribuzione completata](media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>Scaricare e sbloccare gli script di gestione

Durante la distribuzione dell'applicazione, scaricare gli script di gestione e il codice sorgente dell'applicazione.

> [!NOTE]
> I contenuti eseguibili (script, DLL) possono essere bloccati da Windows quando si scaricano i file ZIP da un'origine esterna e si estraggono. Quando si estraggono gli script da un file con estensione zip, seguire questa procedura per sbloccare il file prima di estrarlo. Lo sblocco del file con estensione zip consente di poter eseguire gli script.

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

1. Aprire ...\\Learning Modules\\*UserConfig.psm1* in *PowerShell ISE*.
2. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
3. Salvare le modifiche.

I valori impostati in questo file vengono usati da tutti gli script ed è quindi importante che siano precisi. Se si ridistribuisce l'app, è necessario scegliere valori diversi per l'utente e il gruppo di risorse. Aggiornare quindi di nuovo il file UserConfig.psm1 con i nuovi valori.

## <a name="run-the-application"></a>Eseguire l'applicazione

Nell'app Wingtip Tickets i tenant sono sedi. Una sede può essere una sala concerti, un club sportivo o qualsiasi altro luogo in cui vengono ospitati eventi. Le sedi vengono registrate nell'app come clienti e viene generato un identificatore di tenant per ogni sede. Per ogni sede sono elencati gli eventi imminenti in Wingtip Tickets per consentire al pubblico di acquistare i biglietti.

Ogni sede è associata a un'app Web personalizzata in cui sono elencati i relativi eventi e in cui avviene la vendita dei biglietti. Ogni app Web è indipendente e isolata dagli altri tenant. All'interno del database SQL di Azure ogni dato per ogni tenant viene archiviato in un database multi-tenant partizionato, per impostazione predefinita. Tutti i dati vengono contrassegnati con l'identificatore del tenant.

In una pagina Web centrale **Events Hub** (Hub eventi) sono elencati i collegamenti ai tenant nella distribuzione specifica. Attenersi alla seguente procedura per provare la pagina Web **Events Hub** (Hub eventi) e un'app Web singola:

1. Aprire la pagina **Events Hub** (Hub eventi) nel Web browser:
    - http://events.wingtip-mt.&lt;utente&gt;.trafficmanager.net &nbsp; *(sostituire &lt;utente&gt; con il valore relativo all'utente della distribuzione).*

    ![Hub eventi](media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Fare clic su **Fabrikam Jazz Club** in **Events Hub** (Hub eventi).

   ![Eventi](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Gestione traffico di Azure

Per controllare la distribuzione delle richieste in ingresso, l'app usa [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md). La pagina degli eventi per ogni tenant include il nome del tenant nell'URL. Ogni URL include anche il valore di utente specifico. Ogni URL rispetta il formato indicato usando la procedura seguente:

- http://events.wingtip-mt.&lt;user&gt;.trafficmanager.net/*fabrikamjazzclub*

1. L'app degli eventi analizza il nome del tenant nell'URL. Il nome del tenant è *fabrikamjazzclub* nell'URL di esempio precedente.
2. L'app esegue quindi l'hash del nome del tenant per creare una chiave di accesso a un catalogo usando il [gestore delle mappe partizioni](sql-database-elastic-scale-shard-map-management.md).
3. L'app trova la chiave nel catalogo e ottiene la posizione corrispondente del database del tenant.
4. L'app usa le informazioni sulla posizione per individuare e accedere a un database contenente tutti i dati per il tenant.

#### <a name="events-hub"></a>Hub eventi

1. L'**hub eventi** elenca tutti i tenant registrati nel catalogo e le relative sedi.
2. **e** usa i metadati estesi nel catalogo per recuperare il nome del tenant associato a ogni mapping in modo da generare gli URL.

In un ambiente di produzione si crea in genere un record DNS CNAME per [impostare un dominio Internet aziendale in modo che punti](../traffic-manager/traffic-manager-point-internet-domain.md) al profilo di Gestione traffico.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Dopo la distribuzione, è possibile usare l'app. Lo script di PowerShell *Demo-LoadGenerator* avvia un carico di lavoro per ogni tenant. Il carico del mondo reale in molte app SaaS è generalmente sporadico e imprevedibile. Per simulare questo tipo di carico, il generatore produce un carico distribuito tra tutti i tenant. Il carico include picchi casuali su ogni tenant che si verificano a intervalli casuali. Il modello di carico si manifesta completamente solo dopo alcuni minuti. È pertanto consigliabile lasciare agire il generatore per almeno tre o quattro minuti prima di monitorare il carico.

1. In *PowerShell ISE* aprire lo script \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Premere **F5** per eseguire lo script e avviare il generatore di carico (lasciare i valori dei parametri predefiniti per il momento).

Lo script *Demo-LoadGenerator.ps1* apre un'altra sessione di PowerShell in cui viene eseguito il generatore di carico. In questa sessione il generatore di carico viene eseguito come attività in primo piano che richiama i processi in background di generazione del carico, uno per ogni tenant.

Dopo l'avvio dell'attività in primo piano, il generatore rimane in uno stato di richiamo di processo. L'attività avvia altri processi in background per i nuovi tenant di cui viene successivamente effettuato il provisioning.

Se si chiude la sessione di PowerShell, tutti i processi vengono arrestati.

Può essere opportuno riavviare la sessione del generatore di carico per usare valori di parametro diversi. In questo caso, chiudere la sessione di generazione di PowerShell ed eseguire nuovamente lo script *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Effettuare il provisioning di un nuovo tenant nel database partizionato

La distribuzione iniziale include tre tenant di esempio nel database *Tenants1*. Ora si creerà un altro tenant e si osserveranno i relativi effetti sull'applicazione distribuita. In questo passaggio si preme un tasto per creare un nuovo tenant:

1. Aprire ...\\Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1* in *PowerShell ISE*.
2. Premere **F5** (non **F8**) per eseguire lo script; lasciare i valori predefiniti per il momento.

   > [!NOTE]
   > È necessario eseguire gli script di PowerShell premendo **F5**, non **F8**, per eseguire solo una parte selezionata dello script. Il problema con **F8** è che la variabile *$PSScriptRoot* non viene valutata. Questa variabile è necessaria in molti script per l'esplorazione delle cartelle, richiamare altri script o importare moduli.

Il nuovo tenant Red Maple Racing viene aggiunto al database *Tenants1* e registrato nel catalogo. Nel browser viene visualizzato il sito degli **eventi** del nuovo tenant per la vendita di biglietti:

![Nuovo tenant](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Aggiornare la pagina dell'**hub eventi** per includere il nuovo tenant nell'elenco.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Effettuare il provisioning di un nuovo tenant in un database autonomo

Il modello multi-tenant partizionato consente di scegliere se effettuare il provisioning di un nuovo tenant in un database che ne contiene altri oppure in un database autonomo. Un tenant isolato in un database autonomo offre i vantaggi seguenti:
- Le prestazioni del database del tenant possono essere gestite senza tener conto dalle esigenze di altri tenant.
- Se necessario, è possibile eseguire un ripristino temporizzato del database perché questa operazione non influisce sugli altri tenant.

Può essere utile inserire i sottoscrittori di una versione di prova gratuita, o clienti di livello economy, nei database multi-tenant. È possibile inserire ogni tenant Premium nel proprio database dedicato. Se si creano numerosi database con un solo tenant, è possibile gestirli tutti insieme in un pool elastico per ottimizzare i costi delle risorse.

Successivamente si effettua il provisioning di un altro tenant, questa volta in un database autonomo:

1. In \\...Learning Modules\\Provision and Catalog\\*Demo-ProvisionTenants.ps1** modificare *$TenantName* su **Salix Salsa**, *$VenueType* su **dance** e *$Scenario* su **2**.

2. Premere **F5** per eseguire nuovamente lo script.
    - Premendo **F5** si effettua il provisioning del nuovo tenant in un database separato. Il database e il tenant vengono registrati nel catalogo e quindi viene visualizzata la pagina degli eventi del tenant nel browser.

   ![Pagina degli eventi di Salix Salsa](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Scorrere fino alla parte inferiore della pagina. Nel banner viene visualizzato il nome del database in cui sono archiviati i dati del tenant.

3. Aggiornare la pagina dell'**hub eventi** per includere i due nuovi tenant nell'elenco.

## <a name="explore-the-servers-and-tenant-databases"></a>Esplorare i server e i database tenant

Ora si esamineranno alcune delle risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco dei gruppi di risorse. Aprire il gruppo di risorse creato al momento della distribuzione dell'applicazione.

   ![gruppo di risorse](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Fare clic sul server **catalog-mt&lt;utente&gt;**. Il server di catalogo contiene due database denominati *tenantcatalog* e *basetenantdb*. Il database *basetenantdb* è un database modello vuoto. Viene copiato per creare un nuovo database, da usare per più tenant o per uno solo.

   ![server di catalogo](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Tornare al gruppo di risorse e selezionare il server *tenants1-mt* che ospita i database tenant.
    - Il database tenants1 è un database multi-tenant in cui sono archiviati i tre tenant originali e il primo tenant che è stato aggiunto. È configurato come database Standard da 50 DTU.
    - Il database **salixsalsa** contiene il sito di eventi di danza Salix Salsa come unico tenant. È configurato per impostazione predefinita come database Standard da 50 DTU.

   ![server di tenant](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)


## <a name="monitor-the-performance-of-the-database"></a>Monitorare le prestazioni del database

Se il generatore di carico è rimasto in esecuzione per diversi minuti, sarà disponibile una quantità sufficiente di dati di telemetria per esaminare alcune funzionalità di monitoraggio dei database integrate nel portale di Azure.

1. Passare al server **tenants1-mt&lt;utente&gt;** e fare clic su **tenants1** per visualizzare l'utilizzo delle risorse del database che include quattro tenant. Ogni tenant è sottoposto sporadicamente a un carico pesante dal generatore di carico:

   ![monitorare tenants1](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   Il grafico relativo all'utilizzo delle DTU illustra chiaramente come un database multi-tenant può supportare un carico di lavoro imprevisto su più tenant. In questo caso, il generatore di carico applica sporadicamente un carico di circa 30 DTU a ogni tenant. Questo carico equivale al 60% di utilizzo di un database da 50 DTU. Si verificano picchi superiori al 60% quando il carico viene applicato contemporaneamente a più di un tenant.

2. Individuare il server **tenants1 mt&lt;utente&gt;**  e scegliere il database **salixsalsa**. È possibile esaminare l'utilizzo delle risorse in questo database che contiene un solo tenant.

   ![database salixsalsa](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

Il generatore di carico applica un carico simile a ogni tenant, indipendentemente dal database in cui si trova. Con un solo tenant nel database **salixsalsa**, è possibile osservare che il database è riuscito a sostenere un carico decisamente superiore rispetto al database con più tenant. 

#### <a name="resource-allocations-vary-by-workload"></a>Le allocazioni delle risorse variano a seconda del carico di lavoro

Talvolta, ma non sempre, un database multi-tenant richiede più risorse di un database tenant singolo per assicurare prestazioni ottimali. L'allocazione ottimale delle risorse dipende dalle caratteristiche del carico di lavoro specifico per i tenant nel sistema.

I carichi di lavoro generati dallo script del generatore di carico hanno puramente scopo illustrativo.

## <a name="additional-resources"></a>Risorse aggiuntive

- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).

- Per informazioni sui pool elastici, vedere:
    - [I pool di database elastici consentono di gestire e ridimensionare più database SQL](sql-database-elastic-pool.md)
    - [Aumento del numero di istanze con il database SQL di Azure](sql-database-elastic-scale-introduction.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> - Come distribuire l'applicazione SaaS Wingtip Tickets per database multi-tenant.
> - Informazioni su server e database che costituiscono l'app
> - I tenant vengono mappati ai relativi dati con il *catalogo*.
> - Come effettuare il provisioning di nuovi tenant, in un database multi-tenant e in un database a tenant singolo
> - Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
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

