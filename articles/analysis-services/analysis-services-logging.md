---
title: Registrazione diagnostica per Azure Analysis Services | Microsoft Docs
description: Informazioni sulla configurazione della registrazione diagnostica per Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: a9684042a76c9c906a75334c319b4ca8ee0b727b
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72298618"
---
# <a name="setup-diagnostic-logging"></a>Configurare la registrazione diagnostica

Un aspetto importante di qualsiasi soluzione di Analysis Services è costituito dal monitoraggio delle prestazioni dei server. I [log di diagnostica delle risorse di Azure](../azure-monitor/platform/resource-logs-overview.md) consentono di monitorare e inviare log ad [Archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli ad [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) ed esportarli in [log di Monitoraggio di Azure](../azure-monitor/azure-monitor-log-hub.md).

![Registrazione diagnostica in Archiviazione, Hub eventi o log di Monitoraggio di Azure](./media/analysis-services-logging/aas-logging-overview.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="whats-logged"></a>Informazioni registrate

È possibile selezionare le categorie **Engine** (Motore), **Service** (Servizio) e **Metrics** (Metriche).

### <a name="engine"></a>Engine (Motore)

Se si seleziona la categoria **Engine** (Motore) vengono registrati nel log tutti gli [eventi estesi](https://docs.microsoft.com/analysis-services/instances/monitor-analysis-services-with-sql-server-extended-events) (XEvent). Non è possibile selezionare eventi singoli. 

|Categorie di eventi estesi |Nome evento  |
|---------|---------|
|Controllo di sicurezza    |   Connessione di controllo      |
|Controllo di sicurezza    |   Disconnessione di controllo      |
|Controllo di sicurezza    |   Inizi e arresti server di controllo      |
|Report di stato     |   Inizio report di stato      |
|Report di stato     |   Fine report di stato      |
|Report di stato     |   Report di stato corrente      |
|Query     |  Inizio query       |
|Query     |   Fine query      |
|Comandi:     |  Inizio comando       |
|Comandi:     |  Fine comando       |
|Errori e avvisi     |   Errore      |
|Scoprire     |   Fine individuazione      |
|Notification     |    Notification     |
|sessione     |  Inizializzazione sessione       |
|Blocchi    |  Deadlock       |
|Elaborazione di query     |   Inizio query SE VertiPaq      |
|Elaborazione di query     |   Fine query SE VertiPaq      |
|Elaborazione di query     |   Corrispondenza cache query SE VertiPaq      |
|Elaborazione di query     |   Inizio query diretta      |
|Elaborazione di query     |  Fine query diretta       |

### <a name="service"></a>Service

|Nome operazione  |Ambito  |
|---------|---------|
|ResumeServer     |    Ripresa di un server     |
|SuspendServer    |   Sospensione di un server      |
|DeleteServer     |    Eliminazione di un server     |
|RestartServer    |     Riavvio di un server tramite SQL Server Management Studio o PowerShell    |
|GetServerLogFiles    |    Esportazione di un log del server tramite PowerShell     |
|ExportModel     |   Esportazione di un modello nel portale tramite Apri in Visual Studio     |

### <a name="all-metrics"></a>Tutte le metriche

La categoria metrica registra le stesse [metriche del server](analysis-services-monitor.md#server-metrics) nella tabella AzureMetrics. Se si usa la [scalabilità orizzontale](analysis-services-scale-out.md) delle query ed è necessario separare le metriche per ogni replica di lettura, usare invece la tabella AzureDiagnostics, dove **OperationName** è uguale a **LogMetric**.

## <a name="setup-diagnostics-logging"></a>Configurare la registrazione diagnostica

### <a name="azure-portal"></a>Portale di Azure

1. Nel [portale di Azure](https://portal.azure.com) > server fare clic su **Log di diagnostica** nel riquadro di sinistra e quindi fare clic su **Abilita diagnostica**.

    ![Abilitare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/analysis-services-logging/aas-logging-turn-on-diagnostics.png)

2. In **Impostazioni di diagnostica** specificare le opzioni seguenti: 

    * **Nome**. Immettere un nome per i log da creare.

    * **Archivia in un account di archiviazione**. Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md). Seguire le istruzioni per creare un account di Resource Manager di uso generale, quindi selezionare l'account di archiviazione ritornando in questa pagina del portale. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.
    * **Streaming in un hub eventi**. Per usare questa opzione, sono necessari uno spazio dei nomi esistente e un hub eventi a cui connettersi. Per altre informazioni, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). Tornare a questa pagina del portale per selezionare lo spazio dei nomi dell'hub eventi e il nome dei criteri.
    * **Send to Azure Monitor (Log Analytics workspace)** (Invia a Monitoraggio di Azure - area di lavoro Log Analytics). Per usare questa opzione, usare un'area di lavoro esistente o [creare una nuova area di lavoro](../azure-monitor/learn/quick-create-workspace.md) nel portale. Per altre informazioni sulla visualizzazione dei log, vedere [Visualizzare i log nell'area di lavoro Log Analytics](#view-logs-in-log-analytics-workspace) in questo articolo.

    * **Engine** (Motore). Selezionare questa opzione per registrare gli eventi estesi. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Servizio**. Selezionare questa opzione per registrare gli eventi a livello di servizio. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Metrics** (Metriche). Selezionare questa opzione per archiviare informazioni dettagliate in [Metrics](analysis-services-monitor.md#server-metrics) (Metriche). Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.

3. Fare clic su **Salva**.

    Se si riceve il messaggio di errore "Non è stato possibile aggiornare la diagnostica per \<nome area di lavoro>. La sottoscrizione \<id sottoscrizione> non è registrata per l'uso di microsoft.insights." seguire le istruzioni in [Risolvere i problemi relativi a Diagnostica di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per registrare l'account, quindi ripetere questa procedura.

    Se in seguito si vuole modificare la modalità di salvataggio dei log di diagnostica, è possibile tornare in questa pagina e modificare le impostazioni.

### <a name="powershell"></a>PowerShell

Ecco i comandi di base per iniziare. Se si desiderano istruzioni dettagliate su come configurare la registrazione per un account di archiviazione usando PowerShell, vedere l'esercitazione più avanti in questo articolo.

Per abilitare le metriche e la registrazione diagnostica con PowerShell, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   L'ID account di archiviazione è l'ID risorsa per l'account di archiviazione a cui devono essere inviati i log.

- Per abilitare lo streaming dei log di diagnostica in un Hub eventi, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   L'ID regola del bus di servizio di Azure è una stringa nel formato seguente:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Per consentire l'invio dei log di diagnostica a un'area di lavoro Log Analytics, usare questo comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- È possibile ottenere l'ID risorsa dell'area di lavoro Log Analytics usando il comando seguente:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

### <a name="rest-api"></a>API REST

Informazioni su come [modificare le impostazioni di diagnostica usando l'API REST di Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/). 

### <a name="resource-manager-template"></a>Modello di Resource Manager

Informazioni su come [abilitare le impostazioni di diagnostica durante la creazione di risorse con un modello di Resource Manager](../azure-monitor/platform/diagnostic-settings-template.md). 

## <a name="manage-your-logs"></a>Gestire i log

I log sono in genere disponibili entro due ore dall'impostazione della registrazione. La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.
* Assicurarsi di impostare un periodo di conservazione dopo il quale i log obsoleti vengono eliminati dall'account di archiviazione.

## <a name="view-logs-in-log-analytics-workspace"></a>Visualizzare i log nell'area di lavoro Log Analytics

Le metriche e gli eventi del server sono integrati con gli eventi estesi nell'area personale di Log Analytics per consentirne l'analisi affiancata. L'area di lavoro Log Analytics può anche essere configurata in modo da ricevere gli eventi da altri servizi di Azure e offrire una vista olistica dei dati della registrazione diagnostica in tutta l'architettura.

Per visualizzare i dati di diagnostica, nell'area di lavoro Log Analytics aprire **Log** nel menu a sinistra.

![Opzioni di Ricerca log nel portale di Azure](./media/analysis-services-logging/aas-logging-open-log-search.png)

Nel generatore di query espandere **LogManagement** > **AzureDiagnostics**. AzureDiagnostics include eventi del motore e del servizio. Viene immediatamente creata una query. Il campo EventClass\_s contiene nomi di eventi estesi, che possono apparire familiari a chi ha usato gli eventi estesi per la registrazione locale. Fare clic su **EventClass\_s** o su uno dei nomi di evento per consentire all'area di lavoro Log Analytics di proseguire con la creazione della query. Assicurarsi di salvare le query per un successivo riutilizzo.

### <a name="example-queries"></a>Query di esempio

#### <a name="example-1"></a>Esempio 1

La query seguente restituisce le durate per ogni evento di fine/aggiornamento di query per un database modello e un server. Se la scalabilità orizzontale è aumentata, i risultati vengono suddivisi in base alla replica perché il numero di replica è incluso in ServerName_s. Il raggruppamento per RootActivityId_g riduce il conteggio delle righe recuperato dall'API REST di Diagnostica di Azure e aiuta a rimanere entro i limiti descritti in [log Analytics limiti di velocità](https://dev.loganalytics.io/documentation/Using-the-API/Limits).

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

La query seguente restituisce la memoria e il consumo di QPU per un server. Se la scalabilità orizzontale è aumentata, i risultati vengono suddivisi in base alla replica perché il numero di replica è incluso in ServerName_s.

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

Sono disponibili centinaia di query. Per altre informazioni sulle query, vedere [Introduzione alle query di log in Monitoraggio di Azure](../azure-monitor/log-query/get-started-queries.md).


## <a name="turn-on-logging-by-using-powershell"></a>Abilitare la registrazione tramite PowerShell

In questa esercitazione rapida vengono creati un account di archiviazione nella stessa sottoscrizione e un gruppo di risorse come server di Analysis Services. Si usa quindi set-AzDiagnosticSetting per attivare la registrazione diagnostica, inviando l'output al nuovo account di archiviazione.

### <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Un server di Azure Analysis Services esistente. Per istruzioni sulla creazione di una risorsa del server, vedere [Creare un server nel portale di Azure](analysis-services-create-server.md) o [Creare un server Azure Analysis Services tramite PowerShell](analysis-services-create-powershell.md).

### <a name="aconnect-to-your-subscriptions"></a></a>Connettersi alle sottoscrizioni

Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:  

```powershell
Connect-AzAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario indicarne una specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Digitare il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzSubscription
```

Per specificare quindi la sottoscrizione associata all'account di Azure Analysis Services per la registrazione, digitare:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se più sottoscrizioni sono associate all'account, è importante specificarne una.
>
>

### <a name="create-a-new-storage-account-for-your-logs"></a>Creare un nuovo account di archiviazione per i log

È possibile usare un account di archiviazione esistente per i log, purché sia nella stessa sottoscrizione del server. Per questa esercitazione si crea un nuovo account di archiviazione dedicato ai log di Analysis Services. Per praticità, i dettagli dell'account di archiviazione vengono memorizzati in una variabile denominata **sa**.

Viene usato anche lo stesso gruppo di risorse che contiene il server Analysis Services. Sostituire i valori di `awsales_resgroup`, `awsaleslogs` e `West Central US` con valori di propria scelta:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName awsales_resgroup `
-Name awsaleslogs -Type Standard_LRS -Location 'West Central US'
```

### <a name="identify-the-server-account-for-your-logs"></a>Identificare l'account del server per i log

Impostare il nome dell'account su una variabile denominata **account**, dove ResourceName è il nome dell'account.

```powershell
$account = Get-AzResource -ResourceGroupName awsales_resgroup `
-ResourceName awsales -ResourceType "Microsoft.AnalysisServices/servers"
```

### <a name="enable-logging"></a>Abilitazione della registrazione

Per abilitare la registrazione, usare il cmdlet Set-AzDiagnosticSetting insieme alle variabili per il nuovo account di archiviazione, l'account del server e la categoria. Eseguire questo comando, impostando il flag **-Enabled** su **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories Engine
```

L'output dovrebbe essere simile all'esempio seguente:

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

Questo output conferma che la registrazione è abilitata per il server. Le informazioni vengono salvate nell'account di archiviazione.

È possibile anche impostare criteri di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. È, ad esempio, possibile impostare i criteri di conservazione usando il flag **-RetentionEnabled** impostato su **$true** e impostare il parametro **-RetentionInDays** su **90**. I log antecedenti a 90 giorni vengono eliminati automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories Engine`
  -RetentionEnabled $true -RetentionInDays 90
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [Registrazione diagnostica delle risorse di Azure](../azure-monitor/platform/resource-logs-overview.md).

Vedere [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) nella Guida di PowerShell.
