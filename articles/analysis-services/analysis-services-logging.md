---
title: Registrazione diagnostica per Azure Analysis Services | Microsoft Docs
description: Viene descritto come configurare la registrazione diagnostica delle risorse di Azure per il monitoraggio del server Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0f13f297facedceb50920c0f6afca63fe1df0b48
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375157"
---
# <a name="setup-diagnostic-logging"></a>Configurare la registrazione diagnostica

Una parte importante di qualsiasi soluzione Analysis Services è il monitoraggio delle prestazioni dei server. Con i [log delle risorse di Azure](../azure-monitor/platform/platform-logs-overview.md)è possibile monitorare e inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)ed esportarli in log di monitoraggio di [Azure](../azure-monitor/azure-monitor-log-hub.md).

![Registrazione diagnostica in archiviazione, Hub eventi o log di monitoraggio di Azure](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Cosa è registrato?

È possibile selezionare le categorie **motore**, **servizio**e **metrica** .

### <a name="engine"></a>Motore

Selezionando **motore** viene registrato tutti [XEvent](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events). Non è possibile selezionare singoli eventi. 

|Categorie XEvent |Nome evento  |
|---------|---------|
|Controllo di sicurezza    |   Controlla account di accesso      |
|Controllo di sicurezza    |   Controllo disconnessione      |
|Controllo di sicurezza    |   Avvio e arresto del server di controllo      |
|Report sullo stato di avanzamento     |   Inizio report di stato      |
|Report sullo stato di avanzamento     |   Fine del report di stato      |
|Report sullo stato di avanzamento     |   Rapporto di stato corrente      |
|Query     |  Inizio query       |
|Query     |   Fine query      |
|Comandi     |  Inizio comando       |
|Comandi     |  Fine comando       |
|Errori & avvisi     |   Errore      |
|Individuare     |   Fine individuazione      |
|Notifica     |    Notifica     |
|Sessione     |  Inizializzazione sessione       |
|Serrature    |  Deadlock       |
|Elaborazione delle query     |   Inizio query VertiPaq SE      |
|Elaborazione delle query     |   Fine query VertiPaq SE      |
|Elaborazione delle query     |   Corrispondenza della cache di query VertiPaq SE      |
|Elaborazione delle query     |   Inizio query diretta      |
|Elaborazione delle query     |  Fine query diretta       |

### <a name="service"></a>Servizio

|Nome operazione  |Si verifica quando  |
|---------|---------|
|ResumeServer     |    Riprendere un server     |
|SuspendServer    |   Sospendere un server      |
|DeleteServer     |    Eliminare un server     |
|RestartServer    |     Un utente riavvia un server tramite SSMS o PowerShell    |
|GetServerLogFiles    |    L'utente esporta il log del server tramite PowerShell     |
|ExportModel     |   L'utente esporta un modello nel portale usando Apri in Visual Studio     |

### <a name="all-metrics"></a>Tutte le metriche

La categoria metrica registra le stesse [metriche del server](analysis-services-monitor.md#server-metrics) nella tabella AzureMetrics. Se si usa la [scalabilità orizzontale](analysis-services-scale-out.md) delle query ed è necessario separare le metriche per ogni replica di lettura, usare invece la tabella AzureDiagnostics, dove **OperationName** è uguale a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurare la registrazione diagnostica

### <a name="azure-portal"></a>portale di Azure

1. In [portale di Azure](https://portal.azure.com) server > fare clic su **log di diagnostica** nel percorso di spostamento a sinistra, quindi fare clic **su attiva diagnostica**.

    ![Attivare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. In **impostazioni di diagnostica**specificare le opzioni seguenti: 

    * **Nome**. Immettere un nome per i log da creare.

    * **Archiviare in un account di archiviazione**. Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Vedere [creare un account di archiviazione](../storage/common/storage-create-storage-account.md). Seguire le istruzioni per creare un account di Gestione risorse e per utilizzo generico, quindi selezionare l'account di archiviazione tornando a questa pagina nel portale. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.
    * **Trasmettere a un hub eventi**. Per usare questa opzione, sono necessari uno spazio dei nomi dell'hub eventi esistente e un hub eventi a cui connettersi. Per altre informazioni, vedere [creare uno spazio dei nomi di hub eventi e un hub eventi usando il portale di Azure](../event-hubs/event-hubs-create.md). Tornare quindi a questa pagina del portale per selezionare lo spazio dei nomi dell'hub eventi e il nome del criterio.
    * **Invia a monitoraggio di Azure (log Analytics area di lavoro)** . Per usare questa opzione, usare un'area di lavoro esistente o [creare una nuova](../azure-monitor/learn/quick-create-workspace.md) risorsa dell'area di lavoro nel portale. Per altre informazioni sulla visualizzazione dei log, vedere [visualizzare i log nell'area di lavoro log Analytics](#view-logs-in-log-analytics-workspace) in questo articolo.

    * **Motore**. Selezionare questa opzione per registrare XEvent. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di memorizzazione per i log di diagnostica. I log vengono eliminati in Autodelete dopo la scadenza del periodo di memorizzazione.
    * **Servizio**. Selezionare questa opzione per registrare gli eventi a livello di servizio. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di memorizzazione per i log di diagnostica. I log vengono eliminati in Autodelete dopo la scadenza del periodo di memorizzazione.
    * **Metriche**. Selezionare questa opzione per archiviare i dati dettagliati nelle [metriche](analysis-services-monitor.md#server-metrics). Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di memorizzazione per i log di diagnostica. I log vengono eliminati in Autodelete dopo la scadenza del periodo di memorizzazione.

3. Fare clic su **Salva**.

    Se viene visualizzato un messaggio di errore indicante che non è stato possibile aggiornare la diagnostica per il nome dell'area di lavoro \<>. La sottoscrizione \<ID sottoscrizione > non è registrata per l'uso di Microsoft. Insights. " seguire le istruzioni per la [risoluzione dei problemi diagnostica di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per registrare l'account, quindi ripetere questa procedura.

    Se si desidera modificare il modo in cui i log di diagnostica vengono salvati in qualsiasi momento in futuro, è possibile tornare a questa pagina per modificare le impostazioni.

### <a name="powershell"></a>PowerShell

Ecco i comandi di base per iniziare. Per informazioni dettagliate sulla configurazione della registrazione in un account di archiviazione con PowerShell, vedere l'esercitazione più avanti in questo articolo.

Per abilitare le metriche e la registrazione diagnostica tramite PowerShell, usare i comandi seguenti:

- Per abilitare l'archiviazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID dell'account di archiviazione è l'ID di risorsa per l'account di archiviazione a cui si vogliono inviare i log.

- Per abilitare lo streaming dei log di diagnostica in un hub eventi, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio di Azure è una stringa con il formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Per abilitare l'invio dei log di diagnostica a un'area di lavoro Log Analytics, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro di Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="rest-api"></a>API REST

Informazioni su come [modificare le impostazioni di diagnostica usando l'API REST di monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modello di Gestione risorse

Informazioni su come [abilitare le impostazioni di diagnostica durante la creazione di risorse usando un modello di gestione risorse](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Gestire i log

I log sono in genere disponibili entro un paio di ore dalla configurazione della registrazione. È responsabilità dell'utente gestire i log nell'account di archiviazione:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando gli utenti che possono accedervi.
* Eliminare i log che non si vuole più conservare nell'account di archiviazione.
* Assicurarsi di impostare un periodo di memorizzazione per i log obsoleti che vengono eliminati dall'account di archiviazione.

## <a name="view-logs-in-log-analytics-workspace"></a>Visualizzare i log nell'area di lavoro Log Analytics

Le metriche e gli eventi del server sono integrati con XEvent nella risorsa dell'area di lavoro Log Analytics per l'analisi side-by-side. Log Analytics area di lavoro può essere configurata anche per ricevere eventi da altri servizi di Azure, offrendo una visualizzazione olistica dei dati di registrazione diagnostica nell'architettura.

Per visualizzare i dati di diagnostica, nell'area di lavoro Log Analytics aprire **log** dal menu a sinistra.

![Opzioni di ricerca log nel portale di Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Nel generatore di query espandere **LogManagement** > **AzureDiagnostics**. AzureDiagnostics include eventi del motore e del servizio. Si noti che una query viene creata in tempo reale. Il campo EventClass\_s contiene nomi xEvent, che potrebbero essere noti se è stato usato XEvent per la registrazione locale. Fare clic su **EventClass\_s** o uno dei nomi degli eventi e log Analytics area di lavoro continua a creare una query. Assicurarsi di salvare le query da riutilizzare in un secondo momento.

### <a name="example-queries"></a>Query di esempio

#### <a name="example-1"></a>Esempio 1

La query seguente restituisce le durate per ogni evento di fine/aggiornamento di query per un database modello e un server. Se si aumenta la scalabilità orizzontale, i risultati vengono suddivisi per replica perché il numero di replica è incluso in ServerName_s. Il raggruppamento per RootActivityId_g riduce il conteggio delle righe recuperato dall'API REST Diagnostica di Azure e aiuta a rimanere entro i limiti descritti in [log Analytics limiti di velocità](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName" and DatabaseName_s =~ "MyDatabaseName" ;
window
| where OperationName has "QueryEnd" or (OperationName has "CommandEnd" and EventSubclass_s == 38)
| where extract(@"([^,]*)", 1,Duration_s, typeof(long)) > 0
| extend DurationMs=extract(@"([^,]*)", 1,Duration_s, typeof(long))
| project  StartTime_t,EndTime_t,ServerName_s,OperationName,RootActivityId_g,TextData_s,DatabaseName_s,ApplicationName_s,Duration_s,EffectiveUsername_s,User_s,EventSubclass_s,DurationMs
| order by StartTime_t asc
```

#### <a name="example-2"></a>Esempio 2

La query seguente restituisce la memoria e il consumo di QPU per un server. Se si aumenta la scalabilità orizzontale, i risultati vengono suddivisi per replica perché il numero di replica è incluso in ServerName_s.

```Kusto
let window = AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where name_s == "memory_metric" or name_s == "qpu_metric"
| project ServerName_s, TimeGenerated, name_s, value_s
| summarize avg(todecimal(value_s)) by ServerName_s, name_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

#### <a name="example-3"></a>Esempio 3

La query seguente restituisce i contatori delle prestazioni del motore Analysis Services di righe lette al secondo per un server.

```Kusto
let window =  AzureDiagnostics
   | where ResourceProvider == "MICROSOFT.ANALYSISSERVICES" and Resource =~ "MyServerName";
window
| where OperationName == "LogMetric" 
| where parse_json(tostring(parse_json(perfobject_s).counters))[0].name == "Rows read/sec" 
| extend Value = tostring(parse_json(tostring(parse_json(perfobject_s).counters))[0].value) 
| project ServerName_s, TimeGenerated, Value
| summarize avg(todecimal(Value)) by ServerName_s, bin(TimeGenerated, 1m)
| order by TimeGenerated asc 
```

Sono disponibili centinaia di query che è possibile usare. Per altre informazioni sulle query, vedere [Introduzione alle query di log di monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Abilitare la registrazione tramite PowerShell

In questa esercitazione rapida viene creato un account di archiviazione nella stessa sottoscrizione e nello stesso gruppo di risorse del server Analysis Services. Si usa quindi set-AzDiagnosticSetting per attivare la registrazione diagnostica, inviando l'output al nuovo account di archiviazione.

### <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre delle risorse seguenti:

* Un server di Azure Analysis Services esistente. Per istruzioni sulla creazione di una risorsa server, vedere [creare un server in portale di Azure](analysis-services-create-server.md)o [creare un server di Azure Analysis Services usando PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>connettersi alle sottoscrizioni

Avviare una sessione di Azure PowerShell e accedere al proprio account Azure con il comando seguente:  

```powershell
Connect-AzAccount
```

Nella finestra popup del browser immettere il nome utente e la password dell'account Azure. Azure PowerShell ottiene tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se si dispone di più sottoscrizioni, potrebbe essere necessario specificare una specifica che è stata usata per creare il Azure Key Vault. Digitare quanto segue per visualizzare le sottoscrizioni per l'account:

```powershell
Get-AzSubscription
```

Per specificare quindi la sottoscrizione associata all'account Azure Analysis Services che si sta registrando, digitare:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se si dispone di più sottoscrizioni associate all'account, è importante specificare la sottoscrizione.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Creare un nuovo account di archiviazione per i log

È possibile usare un account di archiviazione esistente per i log, purché si trovi nella stessa sottoscrizione del server. Per questa esercitazione viene creato un nuovo account di archiviazione dedicato ai log Analysis Services. Per semplificare, i dettagli dell'account di archiviazione vengono archiviati in una variabile denominata **sa**.

Si usa anche lo stesso gruppo di risorse che contiene il server Analysis Services. Sostituire i valori per `awsales_resgroup`, `awsaleslogs`e `West Central US` con valori personalizzati:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificare l'account del server per i log

Impostare il nome dell'account su una variabile denominata **account**, dove resourceName è il nome dell'account.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Abilitare la registrazione

Per abilitare la registrazione, usare il cmdlet Set-AzDiagnosticSetting insieme alle variabili per il nuovo account di archiviazione, l'account del server e la categoria. Eseguire il comando seguente, impostando il flag **-Enabled** su **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

L'output dovrebbe essere simile a questo esempio:

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

Questo output conferma che la registrazione è ora abilitata per il server, salvando le informazioni nell'account di archiviazione.

È anche possibile impostare criteri di conservazione per i log in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, impostare i criteri di conservazione utilizzando il flag **-RetentionEnabled** su **$true**e impostare il parametro **-RetentionInDays** su **90**. I log anteriori a 90 giorni vengono eliminati automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [registrazione diagnostica delle risorse di Azure](../azure-monitor/platform/platform-logs-overview.md).

Vedere [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) nella Guida di PowerShell.
