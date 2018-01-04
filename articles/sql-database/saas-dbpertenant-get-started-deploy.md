---
title: Esercitazione per database per tenant SaaS - Database SQL di Azure | Documenti Microsoft
description: Distribuire ed esplorare l'applicazione SaaS multi-tenant Wingtip Tickets, che illustra il modello di database per tenant e altri modelli SaaS usando il database SQL di Azure.
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/07/2017
ms.author: genemi
ms.openlocfilehash: 5342b5290fab9826a2b38cd7ada63a6736c77601
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Distribuire ed esplorare un'applicazione SaaS multi-tenant che usa il modello di database per tenant con il database SQL di Azure

In questa esercitazione si distribuire ed esplorare il SaaS ticket Wingtip *database per ogni tenant* applicazione (Wingtip). L'app Usa un database per ogni modello di tenant, per archiviare i dati di più tenant. L'app è progettato per illustrare le funzionalità di Database SQL di Azure che semplificano l'abilitazione di scenari di SaaS.

Cinque minuti dopo aver fatto clic sul pulsante blu **Distribuisci in Azure**, si dispone di un'applicazione SaaS multi-tenant. L'applicazione include un Database di SQL Azure in esecuzione nel cloud di Microsoft Azure. L'applicazione viene distribuita con tre tenant di esempio, ognuno con il proprio database. Tutti i database vengono distribuiti in un database SQL *pool elastico*. L'applicazione viene distribuita alla sottoscrizione di Azure. Dispone dell'accesso completo per esplorare e lavorare con i singoli componenti dell'app. Sono disponibili in c# codice sorgente dell'applicazione e gli script di gestione, il [repository GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].

In questa esercitazione si apprenderà:

> [!div class="checklist"]
> - Come distribuire l'applicazione SaaS Wingtip
> - Dove ottenere gli script di gestione e il codice sorgente dell'applicazione
> - Sul server, i pool e i database che costituiscono l'app.
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di un nuovo tenant
> - Come monitorare l'attività dei tenant nell'app

Oggetto [serie di esercitazioni correlate](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) offre a esplorare i vari modelli di progettazione e gestione SaaS. Le esercitazioni si compilano oltre la distribuzione iniziale.
Quando si utilizzano le esercitazioni, è possibile visualizzare l'implementazione di modelli diversi di SaaS esaminando gli script forniti.
Gli script viene illustrato come funzionalità del Database SQL di semplificare lo sviluppo di applicazioni SaaS.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che i prerequisiti seguenti siano completati:

* Azure PowerShell è installato. Per informazioni dettagliate, vedere [Introduzione a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-saas-application"></a>Distribuire l'applicazione SaaS Wingtip Tickets

Distribuire l'app:

1. Scegliere e ricordare i valori che necessari per i parametri seguenti:

    - **Utente**: scegliere un valore breve, ad esempio le iniziali dell'utente seguita da una cifra. Ad esempio, *af1*. Il parametro User può contenere solo lettere, cifre e trattini (senza spazi). Il primo e ultimo carattere deve essere una lettera o una cifra. È consigliabile che tutte le lettere minuscole.
    - **Gruppo di risorse**: ogni volta che si distribuisce l'applicazione di Wingtip, è necessario scegliere un diverso nome univoco per il nuovo gruppo di risorse. È consigliabile aggiungere il nome utente per un nome di base per il gruppo di risorse. Potrebbe essere un nome di gruppo di risorse di esempio *wingtip af1*. Nuovamente, è consigliabile che tutte le lettere minuscole.

2. Aprire il Database di SaaS di ticket Wingtip per ogni modello di distribuzione di Tenant nel portale di Azure, facendo clic su blu **Distribuisci in Azure** pulsante.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

3. Nel modello, immettere i valori per i parametri obbligatori:

    > [!IMPORTANT]
    > Alcune impostazioni di autenticazione e per i firewall server sono intenzionalmente non protette a scopo dimostrativo. È consigliabile che si *creare un nuovo gruppo di risorse*. Non usare gruppi di risorse, server o pool esistenti. Non usare questa applicazione, gli script o le risorse distribuite per la produzione. Eliminare questo gruppo di risorse quando non è più necessario usare l'applicazione, per interrompere la fatturazione correlata.

    - **Gruppo di risorse** : selezionare questa opzione **Crea nuovo** e fornire univoci **nome** scelto in precedenza per il gruppo di risorse. 
    - **Percorso**: selezionare un **percorso** dall'elenco a discesa.
    - **Utente** -utilizzare il valore del nome utente scelto in precedenza.

4. Distribuire l'applicazione.

    - Fare clic per accettare i termini e le condizioni.
    - Fare clic su **Acquista**.

5. Monitorare lo stato di distribuzione, fare clic su **notifiche**, ovvero l'icona di controllo del segnale acustico a destra della casella di ricerca. La distribuzione dell'app SaaS Wingtip Tickets richiede circa cinque minuti.

   ![distribuzione completata](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-wingtip-tickets-management-scripts"></a>Scaricare e sbloccare gli script di gestione di Wingtip Tickets

Durante la distribuzione dell'applicazione, scaricare il codice sorgente e gli script di gestione.

> [!IMPORTANT]
> Il contenuto eseguibile (script, DLL) può essere bloccato da Windows quando vengono scaricati da un'origine esterna e si estratti i file con estensione zip. Quando si estraggono gli script da un file con estensione zip, seguire questa procedura per sbloccare il file prima di estrarlo. L'operazione di sblocco assicura che gli script possono essere eseguiti.

1. Individuare il [repository GitHub WingtipTicketsSaaS DbPerTenant][github-wingtip-dpt].
2. Fare clic su **Clona o scarica**.
3. Fare clic su **ZIP di Download**e quindi salvare il file.
4. Fare doppio clic su di **WingtipTicketsSaaS-DbPerTenant-master.zip** file e quindi selezionare **proprietà**.
5. Nel **generale** , selezionare **Unblock**, quindi fare clic su **applica**.
6. Fare clic su **OK**.
7. Estrarre i file.

Gli script si trovano nella cartella *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules*.

## <a name="update-the-user-configuration-file-for-this-deployment"></a>Aggiornare il file di configurazione utente per la distribuzione

Prima di eseguire uno script, impostare i valori relativi al *gruppo di risorse* e all'*utente* in **UserConfig.psm1**. Impostare queste variabili sui valori usati durante la distribuzione.

1. In *PowerShell ISE* aprire ...\\Learning Modules\\*UserConfig.psm1* 
2. Aggiornare *ResourceGroupName* e *Name* con i valori specifici della distribuzione in uso (solo alle righe 10 e 11).
3. Salvare le modifiche.

Sono presenti riferimenti a questi valori in quasi ogni script.

## <a name="run-the-application"></a>Eseguire l'applicazione

L'app illustra le sedi che ospitano gli eventi. I tipi di sede includono sale concerto, jazz club e club sportivi. Nei ticket Wingtip, eventi vengono registrati come tenant. Una sede registrata come tenant consente la visualizzazione dell'elenco di eventi e la vendita dei biglietti ai clienti. Ogni sede è associata a un sito Web personalizzato in cui sono elencati i relativi eventi e dove è possibile la vendita dei biglietti.

Internamente nell'app, ogni tenant Ottiene un database SQL distribuito in un pool elastico SQL.

Una pagina centrale **Events Hub** (Hub eventi) visualizza un elenco di collegamenti ai tenant nella distribuzione.

1. Aprire la pagina *Events Hub* (Hub eventi) nel Web browser: http://events.wingtip-dpt.&lt;UTENTE&gt;.trafficmanager.net sostituendo &lt;UTENTE&gt; con il valore relativo all'utente della distribuzione:

    ![Hub eventi](media/saas-dbpertenant-get-started-deploy/events-hub.png)

2. Fare clic su **Fabrikam Jazz Club** in *Events Hub* (Hub eventi).

    ![Eventi](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)

#### <a name="azure-traffic-manager"></a>Gestione traffico di Azure

L'applicazione Wingtip utilizza [ *Azure Traffic Manager* ](../traffic-manager/traffic-manager-overview.md) per controllare la distribuzione delle richieste in ingresso. URL per accedere all'hub di eventi per un tenant deve rispettare il formato seguente:

- http://Events.Wingtip-DPT.&lt;utente&gt;.trafficmanager.net/fabrikamjazzclub

Le parti del formato precedente sono illustrate nella tabella seguente.

| Parte di URL | DESCRIZIONE |
| :------- | :---------- |
| http://Events.Wingtip-DPT | Le parti di eventi dell'app Wingtip.<br /><br />il ***-dpt*** parte distingue i *Database per ogni Tenant* implementazione di Wingtip da altre implementazioni leggermente diversi. Ad esempio, altri articoli di documentazione offrono Wingtip per *Standalong* (*-sa*), o per *multi-tenant DB*. |
| .  *&lt;UTENTE&gt;* | *AF1* in questo esempio. |
| .trafficmanager.NET/ | Gestione traffico di Azure, l'URL di base. |
| fabrikamjazzclub | Per il tenant denominato *Fabrikam Jazz Club*. |
| &nbsp; | &nbsp; |

1. Il nome del tenant viene analizzato dall'URL, per l'app di eventi.
2. Il nome del tenant viene utilizzato per creare una chiave.
3. La chiave viene utilizzata per accedere al catalogo, per ottenere il percorso del database del tenant.
    - Il catalogo viene implementato utilizzando *gestione mappa partizioni*.
4. Il *Hub eventi* utilizza metadati estesi nel catalogo per ottenere un elenco di eventi URL.

In un ambiente di produzione, in genere si crea un record DNS CNAME per [ *puntare un dominio internet della società* ](../traffic-manager/traffic-manager-point-internet-domain.md) per il profilo di gestione traffico.

## <a name="start-generating-load-on-the-tenant-databases"></a>Iniziare a generare carico sui database tenant

Dopo la distribuzione, è possibile utilizzare l'app.
Lo script di PowerShell *Demo-LoadGenerator* avvia un carico di lavoro eseguito su tutti i database tenant.
Il carico del mondo reale molte App SaaS è sporadici e imprevedibili.
Per simulare il tipo di carico, il generatore produce un carico con picchi casuale o picchi di attività per ogni tenant.
I picchi vengono eseguiti a intervalli casuale.
Sono necessari diversi minuti per il modello di carico a emergere. È consigliabile lasciare che il generatore di esecuzione per almeno tre o quattro minuti prima di monitorare il carico.

1. In *PowerShell ISE* aprire lo script \\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1*.
2. Premere **F5** per eseguire lo script e avviare il generatore di carico. (Lasciare l'impostazione predefinita i valori dei parametri per il momento.)

Non riutilizzare la stessa istanza di PowerShell ISE per diverso, ad esempio un eseguire di nuovo di *Demo LoadGenerator.ps1*. Se è necessario eseguire altri script di PowerShell, avviare un separato di PowerShell ISE.

#### <a name="rerun-with-different-parameters"></a>Eseguire di nuovo con parametri diversi

Se si desidera eseguire nuovamente il test di carico di lavoro con parametri diversi, seguire questi passaggi:

1. Arrestare *LoadGenerator.ps1*.
    - Utilizzare **Ctrl + C**, oppure fare clic su di **arrestare** pulsante.
    - L'arresto non arrestare o influiscono su tutti i processi in background incompleti sono ancora in esecuzione.

2. Riesegui *Demo LoadGenerator.ps1*.
    - Questo Riesegui interrompe innanzi tutto il qualsiasi dei processi di background che potrebbero essere ancora in esecuzione *sp_CpuLoadGenerator*.

Oppure è possibile terminare l'istanza di PowerShell ISE, che provoca l'arresto di tutti i processi in background. Avviare una nuova istanza di PowerShell ISE, quindi eseguire di nuovo *Demo LoadGenerator.ps1*.

#### <a name="monitor-the-background-jobs"></a>Monitorare i processi in background

Se si desidera controllare e monitorare i processi in background, è possibile utilizzare i cmdlet seguenti:

- Get-Job
- Processo di ricezione
- Stop-Job

#### <a name="demo-loadgeneratorps1-actions"></a>Demo LoadGenerator.ps1 azioni

*Demo LoadGenerator.ps1* simula un carico di lavoro attivo di transazioni del cliente. I passaggi seguenti viene descritta la sequenza di azioni che *Demo LoadGenerator.ps1* avvia:

1. *Demo LoadGenerator.ps1* inizia *LoadGenerator.ps1* in primo piano.
    - Entrambi i file con estensione ps1 vengono archiviati nelle cartelle *moduli Learning\\utilità\\*.

2. *LoadGenerator.ps1* scorre tutti i database tenant registrati nel catalogo.

3. Per ogni database tenant, *LoadGenerator.ps1* avvia un'esecuzione dell'istruzione Transact-SQL stored procedure denominata *sp_CpuLoadGenerator*.
    - Le esecuzioni sono in esecuzione in background, chiamando la *Invoke SqlAzureWithRetry* cmdlet.
    - *sp_CpuLoadGenerator* cicli intorno a un'istruzione SQL SELECT per una durata predefinita di 60 secondi. L'intervallo di tempo tra i problemi di SELECT varia in base ai valori di parametro.
    - Questo file con estensione SQL viene archiviato in *WingtipTenantDB\\dbo\\StoredProcedures\\*.

4. Per ogni database tenant, *LoadGenerator.ps1* inoltre avviato il *Start-Job* cmdlet.
    - *Start-Job* simula un carico di lavoro delle vendite di ticket.

5. *LoadGenerator.ps1* continua a essere eseguito, il monitoraggio per i nuovi tenant sono a provisioning.

&nbsp;

Prima di procedere alla sezione successiva, lasciare il generatore di carico in esecuzione nello stato chiamata processo.

## <a name="provision-a-new-tenant"></a>Effettuare il provisioning di un nuovo tenant

La distribuzione iniziale crea tre tenant di esempio. Ora possibile creare un altro tenant per valutare l'impatto sull'applicazione distribuita. Nell'app Wingtip è illustrato il flusso di lavoro per eseguire il provisioning di nuovi tenant di [esercitazione il provisioning e catalogo](saas-dbpertenant-provision-and-catalog.md). In questa fase, viene creato un nuovo tenant, ovvero meno di un minuto.

1. In *PowerShell ISE* aprire ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1*.
2. Premere **F5** per eseguire lo script. (Lasciare i valori predefiniti per il momento).

   > [!NOTE]
   > Molti script dell'applicazione SaaS Wingtip usano *$PSScriptRoot* per l'esplorazione delle cartelle o per chiamare funzioni in altri script. Questa variabile viene valutata solo quando viene eseguito lo script completo premendo **F5**.  Evidenziazione e l'esecuzione di una selezione con **F8** può causare errori. Eseguire gli script premendo **F5**.

Il nuovo database tenant è:

- Creazione di un pool elastico SQL.
- Inizializzato.
- Registrato nel catalogo.

Dopo il provisioning ha esito positivo, il *eventi* sito del tenant di nuovo visualizzata nel browser:

![Nuovo tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Aggiornare il *Hub eventi* per rendere il nuovo tenant vengono visualizzati nell'elenco.

## <a name="explore-the-servers-pools-and-tenant-databases"></a>Esplorare i server, i pool e i database tenant

Dopo aver avviato l'esecuzione di un carico sulla raccolta di tenant, è tempo di esaminare alcune delle risorse distribuite:

1. Nel [portale di Azure](http://portal.azure.com), passare all'elenco di istanze di SQL Server e quindi aprire il **catalogo-dpt -&lt;utente&gt;**  server.
    - Il server di catalogo contiene due database, **tenantcatalog** e **basetenantdb**, ovvero un database modello copiato per creare nuovi tenant.

   ![database](./media/saas-dbpertenant-get-started-deploy/databases.png)

2. Tornare all'elenco di istanze di SQL Server.

3. Aprire il **tenants1-dpt -&lt;utente&gt;**  server che ospita i database tenant.

4. Vedere i seguenti elementi:
    - Ogni database tenant è un database *standard elastico* in un pool standard da 50 eDTU.
    - Il *acero rosso NASCAR* database, ovvero il database tenant è stato eseguito il provisioning in precedenza.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## <a name="monitor-the-pool"></a>Monitorare il pool

Dopo aver *LoadGenerator.ps1* viene eseguito per alcuni minuti, sufficiente dati devono essere disponibili per avviare la ricerca in alcune funzionalità di monitoraggio. Queste funzionalità sono integrate in pool e i database.

Individuare il server **tenants1-dpt -&lt;utente&gt;**, fare clic su **Pool1** per visualizzare l'utilizzo per il pool di risorse. Nei grafici seguenti, il generatore di carico eseguito per un'ora.

   ![monitorare il pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

- Il primo grafico, con l'etichetta **utilizzo delle risorse**, Mostra l'utilizzo di eDTU del pool.
- Il secondo grafico viene illustrato l'utilizzo di eDTU dei primi cinque database nel pool di.

Due grafici viene illustrato che il pool elastico e il Database SQL sono particolarmente adatti per carichi di lavoro dell'applicazione SaaS.
I grafici mostrano che 4 database sono ogni bursting per quanto 40 Edtu e ancora tutti i database in sono supportati da un pool di 50 eDTU. Il pool di 50 eDTU può supportare anche pesanti carichi di lavoro.
Se ne è stato eseguito il provisioning come database autonomi, ciascuno deve essere un S2 (50 DTU) per supportare i picchi.
Il costo di 4 autonomo S2 database sarebbe il prezzo del pool di quasi 3 volte.
Nelle situazioni reali, i clienti di database SQL eseguono attualmente fino a 500 database in pool da 200 eDTU.
Per altre informazioni, vedere l'[esercitazione sul monitoraggio delle prestazioni](saas-dbpertenant-performance-monitoring.md).

## <a name="additional-resources"></a>Risorse aggiuntive

- Altre [esercitazioni basate sull'applicazione SaaS di database per tenant Wingtip Tickets](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
- Per informazioni sui pool elastici, vedere [*Informazioni sul pool elastico di Azure SQL*](sql-database-elastic-pool.md)
- Per informazioni sui processi elastici, vedere [*Gestione dei database cloud con scalabilità orizzontale*](sql-database-elastic-jobs-overview.md)
- Per informazioni sulle applicazioni SaaS multi-tenant, vedere [*Modelli di progettazione per le applicazioni SaaS multi-tenant*](saas-tenancy-app-design-patterns.md)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso:

> [!div class="checklist"]
> - Come distribuire l'applicazione SaaS Wingtip Tickets
> - Informazioni sui server, i pool e i database che costituiscono l'app
> - Come vengono mappati i tenant e i relativi dati con il *catalogo*
> - Come effettuare il provisioning di nuovi tenant
> - Come visualizzare l'utilizzo del pool per monitorare l'attività dei tenant
> - Come eliminare le risorse di esempio per interrompere la fatturazione correlata

Successivamente, provare il [esercitazione il provisioning e catalogo](saas-dbpertenant-provision-and-catalog.md).



<!-- Link references. -->

[github-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant 

