---
title: Registrazione Diganostic per Azure Analysis Services | Documenti Microsoft
description: Informazioni sull'impostazione della registrazione diagnostica per Azure Analysis Services.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 12/29/2017
ms.author: owend
ms.openlocfilehash: 02c25de980b399812676285ad3f87f60af93265f
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="setup-diagnostic-logging"></a>Configurare la registrazione diagnostica

Una parte importante di qualsiasi soluzione di Analysis Services esegue il monitoraggio delle prestazioni di server. Con [i log di diagnostica di risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md), è possibile monitorare e inviare i log per [di archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmessi a [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)e di esportarle in [Log Analitica](https://azure.microsoft.com/services/log-analytics/), fa parte del [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite). 

![Registrazione diagnostica in Archiviazione, hub eventi oppure Operations Management Suite mediante Log Analytics](./media/analysis-services-logging/aas-logging-overview.png)


## <a name="whats-logged"></a>Informazioni di connessione?

È possibile selezionare **motore**, **servizio**, e **metriche** categorie.

### <a name="engine"></a>Engine (Motore)

Selezione **motore** registra tutte le [XEvent](https://docs.microsoft.com/sql/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). È possibile selezionare singoli eventi. 

|Categorie di XEvent |Nome evento  |
|---------|---------|
|Controllo di sicurezza    |   Audit Login      |
|Controllo di sicurezza    |   Audit Logout      |
|Controllo di sicurezza    |   Audit Server Starts And Stops      |
|Report di stato     |   Lo stato di avanzamento Report Begin      |
|Report di stato     |   Progress Report End      |
|Report di stato     |   Lo stato di avanzamento corrente di Report      |
|Query     |  Inizio della query       |
|Query     |   Fine della query      |
|Comandi:     |  Inizio del comando       |
|Comandi:     |  Fine comando       |
|Errori e avvisi     |   Tipi di errore      |
|Scoprire     |   Individuazione di fine      |
|Notifica     |    Notifica     |
|sessione     |  Inizializzazione della sessione       |
|Blocchi    |  deadlock       |
|Elaborazione di query     |   Inizio Query SE VertiPaq      |
|Elaborazione di query     |   Fine della Query SE VertiPaq      |
|Elaborazione di query     |   Corrispondenza di Cache di Query SE VertiPaq      |
|Elaborazione di query     |   Inizio di DirectQuery      |
|Elaborazione di query     |  Fine di DirectQuery       |

### <a name="service"></a>Service

|Nome operazione  |Si verifica quando  |
|---------|---------|
|CreateGateway     |   Utente configura un gateway nel server      |
|ResumeServer     |    Ripresa di un server     |
|SuspendServer    |   Sospendere un server      |
|DeleteServer     |    Eliminazione di un server     |
|RestartServer    |     Riavvio di un server tramite SQL Server Management Studio o PowerShell    |
|GetServerLogFiles    |    Utente Esporta log del server tramite PowerShell     |
|ExportModel     |   Utente esporta un modello nel portale usando aperto in Visual Studio     |

### <a name="all-metrics"></a>Tutte le metriche

La categoria di metriche registra lo stesso [metriche Server](analysis-services-monitor.md#server-metrics) nella metrica.

## <a name="setup-diagnostics-logging"></a>Registrazione della diagnostica del programma di installazione

### <a name="azure-portal"></a>Portale di Azure

1. In [portale di Azure](https://portal.azure.com) > server, fare clic su **log di diagnostica** nella navigazione a sinistra e quindi fare clic su **attivare la diagnostica**.

    ![Abilitare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. In **le impostazioni di diagnostica**, eseguire le operazioni seguenti: 

    * **Nome**. Immettere un nome per i log da creare.

    * **Archivia in un account di archiviazione**. Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md). Seguire le istruzioni per creare un gestore delle risorse, account di uso generale. Tornare quindi a questa pagina del portale per selezionare l'account di archiviazione. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.
    * **Streaming in un hub eventi**. Per usare questa opzione, sono necessari uno spazio dei nomi esistente e un hub eventi a cui connettersi. Per creare uno spazio dei nomi dell'hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). Tornare a questa pagina del portale per selezionare lo spazio dei nomi dell'hub eventi e il nome dei criteri.
    * **Invia a Log Analytics**. Per usare questa opzione, usare un'area di lavoro esistente o creare una nuova area di lavoro di Log Analytics seguendo la procedura per [creare una nuova area di lavoro](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) nel portale. Per altre informazioni sulla visualizzazione dei log in Log Analytics, vedere [Visualizzare i log in Log Analytics](#view-in-loganalytics).

    * **Motore**. Selezionare questa opzione per accedere XEvent. Se si desidera archiviare per un account di archiviazione, è possibile selezionare il periodo di memorizzazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Servizio**. Selezionare questa opzione per registrare gli eventi a livello di servizio. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Metriche**. Selezionare questa opzione per archiviare i dati dettagliati in [metriche](analysis-services-monitor.md#server-metrics). Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.

3. Fare clic su **Save**.

    Se si riceve il messaggio di errore "Non è stato possibile aggiornare la diagnostica per \<nome area di lavoro>. La sottoscrizione \<id sottoscrizione> non è registrata per l'uso di microsoft.insights." seguire le istruzioni in [Risolvere i problemi relativi a Diagnostica di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per registrare l'account, quindi ripetere questa procedura.

    Se si desidera modificare la modalità ai log di diagnostica vengono salvati in un punto qualsiasi in futuro, è possibile tornare a questa pagina per modificare le impostazioni.

### <a name="powershell"></a>PowerShell

Ecco i comandi di base per iniziare a usarlo. Se si desidera della Guida dettagliata su come configurare la registrazione per un account di archiviazione usando PowerShell, vedere l'esercitazione più avanti in questo articolo.

Per abilitare le metriche e la registrazione diagnostica con PowerShell, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

- Per abilitare lo streaming dei log di diagnostica in un Hub eventi, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Per consentire l'invio dei log di diagnostica a un'area di lavoro di Log Analytics, usare questo comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="rest-api"></a>API REST

Informazioni su come [modificare le impostazioni di diagnostica utilizzando l'API REST di Azure monitoraggio](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Modello di Resource Manager

Informazioni su come [abilitare le impostazioni di diagnostica al momento della creazione di risorse utilizzando un modello di gestione risorse](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="manage-your-logs"></a>Gestire i log

I log sono in genere disponibili entro due ore di impostare la registrazione. La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.
* Assicurarsi di impostare un periodo di memorizzazione per in modo che i log precedenti vengono eliminati dall'account di archiviazione.

## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Eventi metriche e i server sono integrati con XEvent in Analitica di Log per l'analisi side-by-side. Log Analitica può anche essere configurato per ricevere eventi da altri servizi di Azure fornendo una visualizzazione olistica registrazione dei dati di diagnostica tra l'architettura.

Per visualizzare i dati di diagnostica nel Log Analitica, aprire la pagina di ricerca nei Log dal menu a sinistra o nell'area di gestione, come illustrato di seguito.

![Opzioni di Ricerca log nel portale di Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Ora che aver abilitato la raccolta di dati, in **ricerca nei Log**, fare clic su **tutti i dati raccolti**.

In **tipo**, fare clic su **AzureDiagnostics**, quindi fare clic su **applica**. AzureDiagnostics include gli eventi del motore e il servizio. Si noterà che una query Log Analitica viene creata in tempo reale. La classe di evento\_campo s contiene nomi di xEvent, che potrebbero avere un aspetto familiari se è stato usato XEvent per la registrazione in locale.

Fare clic su **EventClass\_s** o uno dei nomi degli eventi e Log Analitica continua creazione di una query. Assicurarsi di salvare le query da riutilizzare in un secondo momento.

Assicurarsi di estrazione Operations Management Suite, che fornisce un sito Web di query avanzata, creare dashboard e le funzionalità di avvisi sui dati di Log Analitica.

### <a name="queries"></a>Query

Sono disponibili centinaia di query che è possibile utilizzare. Ecco alcuni per iniziare.
Per ulteriori informazioni sull'utilizzo di nuovo linguaggio di query di ricerca nei Log, vedere [Understanding log eseguire ricerche nei Log Analitica](../log-analytics/log-analytics-log-search-new.md). 

* Query di restituire le query inviate a Azure Analysis Services che impiega più di cinque minuti (300.000 millisecondi) per il completamento.

    ```
    search * | where ( Type == "AzureDiagnostics" ) | where ( EventClass_s == "QUERY_END" ) | where toint(Duration_s) > 300000
    ```

* Identificare le repliche di scalabilità.

    ```
    search * | summarize count() by ServerName_s
    ```
    Quando si utilizza la scalabilità orizzontale, è possibile identificare le repliche di sola lettura perché ServerName\_valori di campo s dispone del numero di istanza di replica aggiunto al nome. Il campo contiene il nome di risorsa di Azure, che corrisponde al nome del server che gli utenti di visualizzare. Il campo IsQueryScaleoutReadonlyInstance_s è uguale a true per le repliche.



> [!TIP]
> Dispone di una query Log Analitica grande che si desidera condividere? Se si dispone di un account GitHub, è possibile aggiungerlo in questo articolo. Fare clic su **modificare** in alto a destra della pagina.


## <a name="tutorial---turn-on-logging-by-using-powershell"></a>Esercitazione - attiva la registrazione tramite PowerShell
In questa esercitazione rapida crei un account di archiviazione nella stessa sottoscrizione e gruppo di risorse del server di Analysis Services. È quindi possibile utilizzare Set-AzureRmDiagnosticSetting per attivare la registrazione, l'invio di output per il nuovo account di archiviazione di diagnostica.

### <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Un server di Azure Analysis Services esistente. Per istruzioni sulla creazione di una risorsa del server, vedere [creare un server nel portale Azure](analysis-services-create-server.md), o [creare un server Azure Analysis Services usando PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Connettersi alle sottoscrizioni

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:  

```powershell
Login-AzureRmAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario indicarne una specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Digitare il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzureRmSubscription
```

Quindi, per specificare la sottoscrizione associata all'account Azure Analysis Services che si esegue l'accesso, digitare:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se si dispone di più sottoscrizioni associate all'account, è importante specificare la sottoscrizione.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Creare un nuovo account di archiviazione per i log

È possibile utilizzare un account di archiviazione esistente per i log, purché sia nella stessa sottoscrizione del server. Per questa esercitazione si crea un nuovo account di archiviazione dedicato per i log di Analysis Services. Per semplificare questa operazione, si desidera archiviare i dettagli di account di archiviazione in una variabile denominata **sa**.

È inoltre possibile utilizzare il gruppo di risorse stesso di quello che contiene il server Analysis Services. Sostituire i valori per `awsales_resgroup`, `awsaleslogs`, e `West Central US` con valori personalizzati:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificare l'account del server per i log

Impostare il nome dell'account a una variabile denominata **account**, dove ResourceName è il nome dell'account.

```powershell
$account = Get-AzureRmResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione, utilizzare il cmdlet Set-AzureRmDiagnosticSetting con le variabili per il nuovo account di archiviazione, account del server e la categoria. Eseguire questo comando, impostando il flag **-Enabled** su **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

L'output dovrebbe essere simile a quanto segue:

```powershell
StorageAccountId            : 
/subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourceGroups/awsales_resgroup/providers/Microsoft.Storage/storageAccounts/awsaleslogs
ServiceBusRuleId            :
EventHubAuthorizationRuleId :
Metrics                    
    TimeGrain       : PT1M
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


Logs                       
    Category        : Engine
    Enabled         : True
    RetentionPolicy
    Enabled : False
    Days    : 0


    Category        : Service
    Enabled         : False
    RetentionPolicy
    Enabled : False
    Days    : 0


WorkspaceId                 :
Id                          : /subscriptions/a23279b5-xxxx-xxxx-xxxx-47b7c6d423ea/resourcegroups/awsales_resgroup/providers/microsoft.analysisservic
es/servers/awsales/providers/microsoft.insights/diagnosticSettings/service
Name                        : service
Type                        :
Location                    :
Tags                        :
```

Questo conferma che è ora abilitata per il server, il salvataggio delle informazioni per l'account di archiviazione.

È inoltre possibile impostare criteri di conservazione per i log in modo registri meno recenti vengono eliminati automaticamente. Ad esempio, impostare criteri di conservazione mediante **- RetentionEnabled** flag **$true**e impostare **- RetentionInDays** parametro **90**. Vengono eliminati automaticamente i log antecedenti a 90 giorni.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni, vedere [registrazione diagnostica di risorse di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).

Vedere [Set AzureRmDiagnosticSetting](https://docs.microsoft.com/powershell/module/azurerm.insights/Set-AzureRmDiagnosticSetting) nella Guida di PowerShell.