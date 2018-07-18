---
title: Esercitazione sul modello SaaS di un database per ogni tenant - Database SQL di Azure | Microsoft Docs
description: Distribuire ed esplorare l'applicazione SaaS multi-tenant Wingtip Tickets, che illustra il modello di database per tenant e altri modelli SaaS usando il database SQL di Azure.
keywords: esercitazione database SQL
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 95d4fc1886e16785b6de8f3a395b218b66d193ff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645362"
---
# <a name="deploy-and-explore-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-sql-database"></a>Distribuire ed esplorare un'app SaaS multi-tenant che usa il modello di database per tenant con il database SQL

Questa esercitazione illustra come distribuire ed esplorare l'applicazione SaaS di database per tenant Wingtip Tickets (Wingtip). L'app usa un modello di database per tenant per archiviare i dati di più tenant. L'app è stata progettata per presentare le funzionalità del database SQL di Azure che semplificano l'abilitazione degli scenari SaaS.

Cinque minuti dopo la selezione di **Distribuisci in Azure**, è disponibile un'applicazione SaaS multi-tenant. L'app include un database SQL che viene eseguito nel cloud. L'app viene distribuita con tre tenant di esempio, ognuno con il proprio database. Tutti i database vengono distribuiti in un pool elastico SQL. L'app viene distribuita nella sottoscrizione di Azure. Si ottiene l'accesso completo per l'esplorazione e l'utilizzo dei singoli componenti dell'app. Gli script di gestione e il codice sorgente in C# dell'applicazione sono disponibili nel [repository WingtipTicketsSaaS-DbPerTenant di GitHub][github-wingtip-dpt].

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> - Come distribuire l'applicazione SaaS Wingtip
> - Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> - Informazioni sui server, i pool e i database che costituiscono l'app
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di un nuovo tenant
> - Come monitorare l'attività dei tenant nell'app

Una [serie di esercitazioni correlate](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) consente di esplorare i vari modelli di progettazione e gestione SaaS. Gli argomenti trattati nelle esercitazioni non si limitano a questa distribuzione iniziale. Nel corso delle esercitazioni è possibile esaminare gli script disponibili per vedere come vengono implementati i diversi modelli SaaS. Gli script illustrano come le funzionalità del database SQL semplificano lo sviluppo di applicazioni SaaS.

## <a name="prerequisites"></a>prerequisiti

Per completare questa esercitazione, verificare di aver installato Azure PowerShell. Per altre informazioni, vedere [Get started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) (Introduzione ad Azure PowerShell).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuire l'applicazione SaaS Wingtip Tickets

#### <a name="plan-the-names"></a>Pianificare i nomi

Nei passaggi di questa sezione si specifica un valore utente che viene usato per verificare che i nomi delle risorse siano globalmente univoci. È inoltre possibile specificare un nome per il gruppo di risorse che contiene tutte le risorse create da una distribuzione dell'app. Per un utente di nome Ann Finley si consiglia quanto segue:

- **Utente**: *af1* è costituito dalle iniziali di Ann Finley seguite da una cifra. Se si distribuisce l'app una seconda volta, usare un valore diverso, ad esempio af2.
- **Gruppo di risorse**: *wingtip-dpt-af1* indica che si tratta dell'app di database per tenant. Aggiungere il nome utente af1 per correlare il nome del gruppo di risorse con i nomi delle risorse in esso contenute.

Scegliere ora i nomi e annotarli. 

#### <a name="steps"></a>Passaggi

1. Per aprire il modello di distribuzione dell'applicazione SaaS di database per tenant Wingtip Tickets nel portale di Azure, fare clic sul pulsante **Distribuisci in Azure**.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

2. Nel modello immettere i valori per i parametri obbligatori.

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. È consigliabile creare un nuovo gruppo di risorse. Non usare gruppi di risorse, server o pool esistenti. Non usare questa applicazione, gli script o eventuali risorse distribuite per la produzione. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione per interrompere la fatturazione correlata.

    - **Gruppo di risorse**: selezionare **Crea nuovo** e specificare il nome univoco scelto in precedenza per il gruppo di risorse. 
    - **Percorso**: selezionare un percorso dall'elenco a discesa.
    - **Utente**: usare il valore del nome utente scelto in precedenza.

3. Distribuire l'applicazione.

    a. Selezionare l'opzione per accettare i termini e le condizioni.

    b. Selezionare **Acquisto**.

4. Per monitorare lo stato di distribuzione, selezionare **Notifiche** (l'icona a forma di campanella a destra della casella di ricerca). La distribuzione dell'app SaaS Wingtip Tickets richiede circa cinque minuti.

   ![Distribuzione completata](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Scaricare e sbloccare gli script di gestione di Wingtip Tickets

Durante la distribuzione dell'applicazione, scaricare il codice sorgente e gli script di gestione.

> [!IMPORTANT]
> I contenuti eseguibili (script e DLL) possono essere bloccati da Windows quando si scaricano e si estraggono i file con estensione zip provenienti da un'origine esterna. Seguire i passaggi per sbloccare il file con estensione zip prima di estrarre gli script. L'operazione di sblocco consente di verificare che gli script possono essere eseguiti.

1. Passare al [repository WingtipTicketsSaaS-DbPerTenant di GitHub][github-wingtip-dpt].
2. Selezionare **Clona o scarica**.
3. Selezionare **Download ZIP** (Scarica ZIP) e salvare il file.
4. Fare clic con il pulsante destro del mouse sul file **WingtipTicketsSaaS-DbPerTenant-master.zip** e quindi scegliere **Proprietà**.
5. Nella scheda **Generale** selezionare **Annulla blocco** > **Applica**.
6. Selezionare **OK** ed estrarre i file.

Gli script si trovano nella cartella ...\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aggiornare il file di configurazione utente per la distribuzione

Prima di eseguire uno script, aggiornare il gruppo di risorse e i valori utente nel file UserConfig. Impostare queste variabili sui valori usati durante la distribuzione.

1. In PowerShell ISE aprire ...\\Learning Modules\\**UserConfig.psm1** 
2. Aggiornare **ResourceGroupName** e **Name** con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
3. Salvare le modifiche.

Sono presenti riferimenti a questi valori in quasi ogni script.

## <a name="run-the-application"></a>Eseguire l'applicazione

L'app illustra le sedi che ospitano gli eventi. I tipi di sede includono sale concerto, jazz club e club sportivi. Nell'app Wingtip Tickets le sedi sono registrate come tenant. Una sede registrata come tenant consente la visualizzazione dell'elenco di eventi e la vendita dei biglietti ai clienti. Ogni sede è associata a un sito Web personalizzato in cui sono elencati i relativi eventi e dove è possibile la vendita dei biglietti.

All'interno dell'app ogni tenant ha un database SQL distribuito in un pool elastico SQL.

Una pagina centrale **Events Hub** (Hub eventi) visualizza un elenco di collegamenti ai tenant nella distribuzione.

1. Usare l'URL per aprire l'hub eventi nel Web browser: http://events.wingtip-dpt.&lt;utente&gt;.trafficmanager.net. Sostituire &lt;utente&gt; con il valore relativo all'utente della distribuzione.

    ![Hub eventi](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Selezionare **Fabrikam Jazz Club** nella pagina Events Hub.

    ![Eventi](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Gestione traffico di Azure

L'applicazione Wingtip usa [*Gestione traffico di Azure*](../traffic-manager/traffic-manager-overview.md) per controllare la distribuzione delle richieste in ingresso. L'URL per accedere alla pagina di eventi per un tenant specifico usa il formato seguente:

- http://events.wingtip-dpt.&lt;utente&gt;.trafficmanager.net/fabrikamjazzclub

    Le parti del formato precedente sono illustrate nella tabella seguente.

    | Parte URL        | DESCRIZIONE       |
    | :-------------- | :---------------- |
    | http://events.wingtip-dpt | Le parti degli eventi dell'app Wingtip.<br /><br /> *-dpt* distingue l'implementazione di *database per tenant* dell'app Wingtip Tickets dalle altre implementazioni, ad esempio quelle di app per tenant *autonome* (*-sa*) o di *database multi-tenant* (*-mt*). |
    | .*&lt;user&gt;* | *af1* nell'esempio. |
    | .trafficmanager.net/ | Gestione traffico, URL di base. |
    | fabrikamjazzclub | Identifica il tenant denominato Fabrikam Jazz Club. |
    | &nbsp; | &nbsp; |

* Il nome del tenant viene analizzato nell'URL dall'app degli eventi.
* Il nome del tenant viene usato per creare una chiave.
* La chiave viene usata per accedere al catalogo e ottenere la posizione del database del tenant.
    - Il catalogo viene implementato tramite la *gestione di mappe partizioni*.
* La pagina Events Hub usa i metadati estesi nel catalogo per creare gli URL della pagina con l'elenco degli eventi per ogni tenant.

In un ambiente di produzione si crea in genere un record CNAME DNS per [*configurare un dominio Internet aziendale in modo che faccia riferimento*](../traffic-manager/traffic-manager-point-internet-domain.md) al nome DNS di Gestione traffico.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Al termine della distribuzione, è possibile usare l'app.

Lo script di PowerShell *Demo-LoadGenerator* avvia un carico di lavoro che viene eseguito su tutti i database tenant. Il carico del mondo reale in molte app SaaS è generalmente sporadico e imprevedibile. Per simulare il tipo di carico, il generatore produce un carico con picchi casuali o picchi di attività per ogni tenant. I picchi vengono eseguiti a intervalli casuali. Il modello di carico si manifesta completamente solo dopo alcuni minuti. Lasciare agire il generatore per almeno tre o quattro minuti prima di monitorare il carico.

1. In PowerShell ISE aprire ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* script.
2. Premere F5 per eseguire lo script e avviare il generatore di carico. Per il momento lasciare invariati i valori predefiniti dei parametri.
3. Accedere al proprio account Azure e, se necessario, selezionare la sottoscrizione che si vuole usare.

Lo script del generatore di carico avvia un processo in background per ogni database nel catalogo e quindi si arresta. Se si esegue nuovamente lo script del generatore di carico, questo arresta tutti i processi in esecuzione in background prima di avviarne di nuovi.

#### <a name="monitor-the-background-jobs"></a>Monitorare i processi in background

Per controllare e monitorare i processi in background, usare i cmdlet seguenti:

- `Get-Job`
- `Receive-Job`
- `Stop-Job`

#### <a name="demo-loadgeneratorps1-actions"></a>Azioni di Demo-LoadGenerator.ps1

*Demo-LoadGenerator.ps1* simula un carico di lavoro attivo delle transazioni dei clienti. I passaggi seguenti descrivono la sequenza di azioni avviate da *Demo LoadGenerator.ps1*:

1. *Demo-LoadGenerator.ps1* avvia *LoadGenerator.ps1* in primo piano.

    - Entrambi i file con estensione ps1 vengono archiviati nelle cartelle Learning Modules\\Utilities\\.

2. *LoadGenerator.ps1* esegue un ciclo di tutti i database tenant nel catalogo.

3. *LoadGenerator.ps1* avvia un processo di PowerShell in background per ogni database tenant:

    - Per impostazione predefinita, i processi in background vengono eseguiti per 120 minuti.
    - Ogni processo comporta un carico sulla CPU in un database tenant eseguendo *sp_CpuLoadGenerator*. L'intensità e la durata del carico variano a seconda del valore `$DemoScenario`. 
    - *sp_CpuLoadGenerator* esegue un ciclo per un'istruzione SQL SELECT che causa un carico elevato della CPU. L'intervallo di tempo tra i problemi creati dall'istruzione SELECT varia in base ai valori di parametro per creare un carico della CPU controllabile. I livelli di carico e gli intervalli sono casuali per simulare carichi più realistici.
    - Questo file con estensione sql viene archiviato in *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Se `$OneTime = $false`, il generatore di carico avvia i processi in background e quindi prosegue l'esecuzione. Ogni 10 secondi, esegue il monitoraggio di eventuali nuovi tenant di cui viene effettuato il provisioning. Se si imposta `$OneTime = $true`, il generatore di carico avvia i processi in background e quindi arresta l'esecuzione in primo piano. Per questa esercitazione lasciare `$OneTime = $false`.

  Usare CTRL+C o CTRL+INTERR per arrestare o riavviare il generatore di carico. 

  Se si lascia il generatore di carico in esecuzione in primo piano, usare un'altra istanza di PowerShell ISE per eseguire altri script di PowerShell.

&nbsp;

Prima di continuare con la sezione successiva, lasciare il generatore di carico in esecuzione nello stato di richiamo del processo.

## <a name="provision-a-new-tenant"></a>Effettuare il provisioning di un nuovo tenant

La distribuzione iniziale crea tre tenant di esempio. Si creerà ora un altro tenant per verificarne l'impatto sull'applicazione distribuita. Nell'app Wingtip Tickets, il flusso di lavoro per effettuare il provisioning di nuovi tenant è illustrato nell'[esercitazione su provisioning e catalogazione](saas-dbpertenant-provision-and-catalog.md). In questa fase si crea un nuovo tenant in meno di un minuto.

1. Aprire una nuova finestra di PowerShell ISE.
2. Aprire ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
3. Per eseguire lo script, premere F5. Per il momento lasciare invariati i valori predefiniti.

   > [!NOTE]
   > Molti script dell'applicazione SaaS Wingtip usano *$PSScriptRoot* per esplorare le cartelle per chiamare funzioni in altri script. Questa variabile viene valutata solo quando viene eseguito lo script completo premendo F5. Se si evidenzia e si esegue una selezione con F8 possono verificarsi errori. Per eseguire gli script, premere F5.

Per il nuovo database tenant sono state eseguite le operazioni seguenti:

- Creazione in un pool elastico SQL.
- Inizializzazione.
- Registrazione nel catalogo.

Al termine del provisioning, nel browser viene visualizzato il sito degli *eventi* del nuovo tenant.

![Nuovo tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aggiornare la pagina Events Hub per includere il nuovo tenant nell'elenco.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Esplorare i server, i pool e i database tenant

Dopo aver avviato l'esecuzione di un carico sulla raccolta di tenant, è possibile esaminare alcune delle risorse distribuite.

1. Nel [portale di Azure](http://portal.azure.com) passare all'elenco di server SQL. Aprire quindi il server **catalog-dpt-&lt;UTENTE&gt;**.
    - Il server di catalogo contiene due database, **tenantcatalog** e **basetenantdb**, un database modello che viene copiato per creare nuovi tenant.

   ![Database](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Tornare all'elenco di istanze di SQL Server.

3. Aprire il server **tenants1-dpt-&lt;UTENTE&gt;** che include i database tenant.

4. Vedere gli elementi seguenti:

    - Ogni database tenant è un database **standard elastico** in un pool standard da 50 eDTU.
    - Il database Red Maple Racing è il database tenant di cui è stato effettuato il provisioning in precedenza.

   ![Server con i database](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorare il pool

Dopo che *LoadGenerator.ps1* viene eseguito per alcuni minuti, dovrebbe essere disponibile una quantità sufficiente di dati per iniziare a esaminare alcune funzionalità di monitoraggio. Queste funzionalità sono integrate nei pool e nei database.

Passare al server **tenants1-dpt-&lt;utente&gt;** e selezionare **Pool1** per visualizzare l'utilizzo di risorse per il pool. Nei grafici seguenti il generatore di carico viene eseguito per un'ora.

   ![Monitorare il pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Il primo grafico, etichettato come **Utilizzo delle risorse**, mostra l'utilizzo eDTU del pool.
- Il secondo grafico mostra l'utilizzo eDTU dei cinque database più attivi nel pool.

I due grafici illustrano che i pool elastici e il database SQL sono adatti ai carichi di lavoro di applicazioni SaaS non prevedibili. I grafici mostrano che quattro database, ognuno dei quali con picchi di utilizzo fino a 40 eDTU, vengono supportati senza problemi da un pool da 50 eDTU. Il pool da 50 eDTU può supportare anche carichi di lavoro più pesanti. Se è stato effettuato il provisioning dei database come database autonomi, per supportare i picchi ogni database deve essere un S2 (50 DTU). Il costo di quattro database S2 autonomi corrisponde circa al triplo del prezzo del pool. Nelle situazioni reali, i clienti di database SQL eseguono fino a 500 database in pool da 200 eDTU. Per altre informazioni, vedere l'[esercitazione sul monitoraggio delle prestazioni](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- Per altre informazioni, vedere altre [esercitazioni basate sull'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- Per informazioni sui pool elastici, vedere [Definizione di pool elastici SQL di Azure](sql-database-elastic-pool.md).
- Per informazioni sui processi elastici, vedere [Gestione dei database cloud con scalabilità orizzontale](sql-database-elastic-jobs-overview.md).
- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [Modelli di progettazione per le applicazioni SaaS multi-tenant](saas-tenancy-app-design-patterns.md).


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> - Come distribuire l'applicazione SaaS Wingtip Tickets
> - Informazioni sui server, i pool e i database che costituiscono l'app
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di nuovi tenant
> - Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> - Come eliminare le risorse di esempio per interrompere la fatturazione correlata

Successivamente, eseguire l'[esercitazione su provisioning e catalogazione](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

