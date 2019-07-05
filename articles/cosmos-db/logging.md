---
title: Registrazione diagnostica in Azure Cosmos DB
description: Informazioni sui diversi modi in cui è possibile registrare e monitorare i dati archiviati in Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 41fa5a859e738c2bb70e4885aa856f247e922492
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448988"
---
# <a name="diagnostic-logging-in-azure-cosmos-db"></a>Registrazione diagnostica in Azure Cosmos DB 

Dopo avere iniziato a usare uno o più database di Azure Cosmos DB, sarà possibile scegliere di monitorare come e quando viene eseguito l'accesso ai database. Questo articolo fornisce una panoramica dei log disponibili nella piattaforma di Azure. Descrive come abilitare la registrazione diagnostica per il monitoraggio per inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), come trasmettere log a [hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)e su come esportare log in [logdimonitoraggiodiAzure](https://azure.microsoft.com/services/log-analytics/).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="logs-available-in-azure"></a>Log disponibili in Azure

Prima di parlare del monitoraggio dell'account Azure Cosmos DB, occorre chiarire alcuni concetti sulla registrazione e sul monitoraggio. Nella piattaforma di Azure sono disponibili diversi tipi di log, ovvero [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [metriche di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), eventi, monitoraggio di heartbeat, log delle operazioni e così via. Sono disponibili moltissimi log. È possibile visualizzare l'elenco completo dei log in [monitoraggio di Azure registra](https://azure.microsoft.com/services/log-analytics/) nel portale di Azure. 

L'immagine seguente mostra il tipo di log di Azure sono disponibili diversi:

![Diversi tipi di log di Azure](./media/logging/azurelogging.png)

Nella figura le **risorse di calcolo** rappresentano le risorse di Azure per le quali è possibile accedere al sistema operativo Guest di Microsoft. Ad esempio, le macchine virtuali di Azure, i set di scalabilità di macchine virtuali, il servizio Azure Container e così via sono considerati risorse di calcolo. Le risorse di calcolo generano log attività, log di diagnostica e registri applicazioni. Per altre informazioni, vedere l'articolo [Sources of monitoring data in Azure](../azure-monitor/platform/data-sources.md) (Origini dei dati di monitoraggio in Azure).

Le **risorse non di calcolo** sono risorse in cui non è possibile accedere al sistema operativo sottostante e usare direttamente la risorsa. Esempi di risorse non di calcolo sono i gruppi di sicurezza di rete, le app per la logica e così via. Azure Cosmos DB è una risorsa non di calcolo. È possibile visualizzare i log delle risorse non di calcolo nel log attività oppure abilitare l'opzione Log di diagnostica nel portale. Per altre informazioni, vedere l'articolo [Origini dei dati di monitoraggio](../azure-monitor/platform/data-sources.md).

Il log attività registra le operazioni a livello di sottoscrizione di Azure Cosmos DB. Vengono registrate operazioni come ListKeys, Write DatabaseAccounts e altro ancora. I log di diagnostica offrono un livello di registrazione più granulare e consentono di registrare DataPlaneRequests (Create, Read, Query e così via) e MongoRequests.


Questo articolo è incentrato sui log attività di Azure, i log di diagnostica di Azure e le metriche di Azure e sulle differenze tra questi tre tipi di log. 

### <a name="azure-activity-log"></a>Azure Activity Log

Il log attività di Azure è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Il log attività segnala eventi a livello di piano di controllo per le sottoscrizioni sotto la categoria amministrativa. È possibile usare il log attività per acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. 

Il log attività è diverso dal log di diagnostica. Il log attività contiene dati relativi alle operazioni su una risorsa esterna (il _piano di controllo_). Nel contesto di Azure Cosmos DB le operazioni a livello di piano di controllo includono crea raccolte, elenca chiavi, elimina chiavi, elenca database e così via. I log di diagnostica vengono generati da una risorsa e contengono informazioni sul funzionamento di tale risorsa (il _piano dati_). Alcuni esempi di operazioni del piano dati nel log di diagnostica sono Delete, Insert e ReadFeed.

I log attività (operazioni a livello di piano di controllo) possono essere più dettagliati e possono includere l'indirizzo di posta elettronica completo del chiamante, l'indirizzo IP del chiamante, il nome della risorsa, il nome dell'operazione, l'ID del tenant e così via. Il log attività contiene alcune [categorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) di dati. Per informazioni dettagliate sugli schemi di queste categorie, vedere [Schema degli eventi del log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema). I log di diagnostica, tuttavia, possono essere restrittivi, perché i dati personali vengono spesso rimossi da tali log. È possibile che sia disponibile l'indirizzo IP del chiamante, ma senza l'ultimo ottetto.

### <a name="azure-metrics"></a>Metriche di Azure

Le [metriche di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) includono il tipo più importante di dati di telemetria di Azure, definiti anche _contatori delle prestazioni_, che vengono emessi dalla maggior parte delle risorse di Azure. Le metriche consentono di visualizzare informazioni su velocità effettiva, risorse di archiviazione, coerenza, disponibilità e latenza delle risorse di Azure Cosmos DB. Per altre informazioni, vedere [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure

I log di diagnostica di Azure sono generati da una risorsa e offrono dati completi e frequenti sul funzionamento della risorsa stessa. Questi log vengono acquisiti per ogni richiesta. Il contenuto di questi log varia in base al tipo di risorsa. I log di diagnostica a livello di risorsa differiscono anche dal log di diagnostica a livello del sistema operativo guest. I log di diagnostica del sistema operativo guest sono raccolti da un agente in esecuzione all'interno di una macchina virtuale o di un altro tipo di risorsa supportato. I log di diagnostica a livello di risorsa non richiedono l'uso di un agente e acquisiscono i dati specifici della risorsa dalla piattaforma di Azure stessa. I log di diagnostica a livello del sistema operativo guest acquisiscono i dati dal sistema operativo e dalle applicazioni in esecuzione in una macchina virtuale.

![Registrazione diagnostica in Archiviazione, Hub eventi o log di Monitoraggio di Azure](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Informazioni registrate dai log di diagnostica di Azure

* Vengono registrate tutte le richieste back-end autenticate (TCP/REST) in tutte le API, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide. Il supporto per le richieste di API di tabella, Graph e Cassandra avviate dall'utente non è attualmente disponibile.
* Operazioni nel database stesso, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.
* Operazioni sulle chiavi di account, che includono la creazione, la modifica o l'eliminazione delle chiavi.
* Richieste non autenticate che generano una risposta 401. Ad esempio, richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Abilitare la registrazione nel portale di Azure

Usare la procedura seguente per abilitare la registrazione diagnostica nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com). 

1. Accedere all'account Azure Cosmos. Aprire il **le impostazioni di diagnostica** riquadro, quindi selezionare **Aggiungi impostazione di diagnostica** opzione.

    ![Abilitare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/logging/turn-on-portal-logging.png)

1. Nel **le impostazioni di diagnostica** pagina, compilare il modulo con i dettagli seguenti: 

    * **Nome**: Immettere un nome per i log da creare.

    * È possibile archiviare i log per i servizi seguenti:

      * **Archivia in un account di archiviazione**: Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Per creare un nuovo account di archiviazione nel portale, vedere [creare un account di archiviazione](../storage/common/storage-create-storage-account.md) articolo. Quindi, tornare al riquadro di impostazioni di diagnostica di Azure Cosmos Db nel portale per selezionare l'account di archiviazione. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.

      * **Streaming in un hub eventi** : per usare questa opzione, sono necessari uno spazio dei nomi di Hub eventi e un hub eventi a cui connettersi. Per creare uno spazio dei nomi di Hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). Quindi, tornare a questa pagina nel portale per selezionare il nome dello spazio dei nomi e i criteri di Hub eventi.

      * **Invia a Log Analytics**: per usare questa opzione, usare un'area di lavoro esistente o creare una nuova area di lavoro Log Analytics seguendo la procedura per [creare una nuova area di lavoro](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace) nel portale. 

   * È possibile registrare i dati seguenti:

      * **DataPlaneRequests**: Selezionare questa opzione per registrare le richieste di back-end per tutte le API che include gli account SQL, Graph, MongoDB, Cassandra e API Table di Azure Cosmos DB. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati. I dati JSON seguenti sono un esempio di output dei dettagli registrato usando DataPlaneRequests. Le proprietà di chiave da notare sono: Requestcharge, statusCode, clientIPaddress e partitionID:

       ```
       { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
       ```

      * **MongoRequests**: Selezionare questa opzione per registrare le richieste avviate dall'utente dal front-end per elaborare le richieste all'API di Azure Cosmos DB per MongoDB. Le richieste di MongoDB verranno visualizzato in MongoRequests nonché DataPlaneRequests. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati. I dati JSON seguenti sono un esempio di output dei dettagli registrato usando MongoRequests. Le proprietà di chiave da notare sono: Requestcharge, opCode:

       ```
       { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
       ```

      * **QueryRuntimeStatistics**: Selezionare questa opzione per registrare il testo della query che è stato eseguito.  I dati JSON seguenti sono un esempio di output dei dettagli registrato usando QueryRuntimeStatistics:

       ```
       { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
       ```

      * **Metrica Richieste**: selezionare questa opzione per archiviare i dati dettagliati nelle [metriche di Azure](../azure-monitor/platform/metrics-supported.md). Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.

3. Selezionare **Salva**.

    Se si riceve il messaggio di errore "Non è stato possibile aggiornare la diagnostica per \<nome area di lavoro>. La sottoscrizione \<ID sottoscrizione> non è registrata per l'uso di microsoft.insights", seguire le istruzioni in [Risolvere i problemi relativi a Diagnostica di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per registrare l'account, quindi ripetere questa procedura.

    Se in seguito si vuole modificare la modalità di salvataggio dei log di diagnostica, tornare a questa pagina per modificare le impostazioni di tali log per l'account.

## <a name="turn-on-logging-by-using-azure-cli"></a>Abilitare la registrazione tramite l'interfaccia della riga di comando di Azure

Per abilitare le metriche e la registrazione diagnostica con l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name DiagStorage --resource <resourceId> --storage-account <storageAccountName> --logs '[{"category": "QueryRuntimeStatistics", "enabled": true, "retentionPolicy": {"enabled": true, "days": 0}}]'
   ```

   `resource` è il nome dell'account del database Azure Cosmos DB. La risorsa è nel formato "/subscriptions/`<subscriptionId>`ResourceGroups`<resource_group_name>`/providers/Microsoft.DocumentDB/databaseAccounts/ < Azure_Cosmos_account_name >" il `storage-account` è il nome dell'account di archiviazione a cui si Se si desidera inviare i log. È possibile registrare altri log aggiornando i valori di parametro di categoria "MongoRequests" o "DataPlaneRequests". 

- Per abilitare la trasmissione dei log di diagnostica a un hub eventi, usare questo comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --event-hub-rule <eventHubRuleID> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

   `resource` è il nome dell'account del database Azure Cosmos DB. Il `event-hub-rule` è l'ID regola dell'hub eventi. 

- Per consentire l'invio dei log di diagnostica a un'area di lavoro Log Analytics, usare questo comando:

   ```azurecli-interactive
   az monitor diagnostic-settings create --name cdbdiagsett --resourceId <resourceId> --workspace <resource id of the log analytics workspace> --logs '[{"category":"QueryRuntimeStatistics","enabled":true,"retentionPolicy":{"days":6,"enabled":true}}]'
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

## <a name="turn-on-logging-by-using-powershell"></a>Abilitare la registrazione tramite PowerShell

Per abilitare la registrazione diagnostica mediante PowerShell, è necessario avere almeno la versione 1.0.1 di Azure PowerShell.

Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

Se Azure PowerShell è già stato installato ma non si conosce la versione, dalla console di PowerShell digitare `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Connettersi alle sottoscrizioni
Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:  

```powershell
Connect-AzAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se si dispone di più di una sottoscrizione, potrebbe essere necessario indicare la sottoscrizione specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Per visualizzare le sottoscrizioni relative all'account, digitare il comando seguente:

```powershell
Get-AzSubscription
```

Per specificare quindi la sottoscrizione associata all'account Azure Cosmos DB da registrare, digitare il comando seguente:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se si dispone di più di una sottoscrizione associata all'account, è importante specificare la sottoscrizione da usare.
>
>

Per altre informazioni su come configurare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Creare un nuovo account di archiviazione per i log
Anche se è possibile usare un account di archiviazione esistente per i log, in questa esercitazione si creerà un nuovo account di archiviazione dedicato ai log di Azure Cosmos DB. Per praticità, i dettagli dell'account di archiviazione verranno memorizzati in una variabile denominata **sa**.

Per rendere la gestione ancora più facile, in questa esercitazione si userà lo stesso gruppo di risorse che contiene il database di Azure Cosmos DB. Sostituire i valori per i parametri **ContosoResourceGroup**, **contosocosmosdblogs** e **North Central US**, come applicabile:

```powershell
$sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se si sceglie di usare un account di archiviazione esistente, questo dovrà usare la stessa sottoscrizione di Azure Cosmos DB. L'account dovrà anche usare il modello di distribuzione Resource Manager, anziché il modello di distribuzione classica.
>
>

### <a id="identify"></a>Identificare l'account di Azure Cosmos DB per i log
Impostare il nome dell'account Azure Cosmos DB su una variabile denominata **account**, dove **ResourceName** è il nome dell'account Azure Cosmos DB.

```powershell
$account = Get-AzResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Abilitare la registrazione
Per abilitare la registrazione per Azure Cosmos DB, usare il cmdlet `Set-AzDiagnosticSetting` insieme alle variabili per il nuovo account di archiviazione, l'account Azure Cosmos DB e la categoria da abilitare per la registrazione. Eseguire questo comando e impostare il flag **-Enabled** su **$true**:

```powershell
Set-AzDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

L'output del comando sarà simile all'esempio seguente:

```powershell
    StorageAccountId            : /subscriptions/<subscription-ID>/resourceGroups/ContosoResourceGroup/providers`
    /Microsoft.Storage/storageAccounts/contosocosmosdblogs
    ServiceBusRuleId            :
    EventHubAuthorizationRuleId :
    Metrics
        TimeGrain       : PT1M
        Enabled         : False
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    Logs
        Category        : DataPlaneRequests
        Enabled         : True
        RetentionPolicy
        Enabled : False
        Days    : 0
    
    WorkspaceId                 :
    Id                          : /subscriptions/<subscription-ID>/resourcegroups/ContosoResourceGroup/providers`
    /microsoft.documentdb/databaseaccounts/contosocosmosdb/providers/microsoft.insights/diagnosticSettings/service
    Name                        : service
    Type                        :
    Location                    :
    Tags                        :
```

L'output del comando conferma che ora la registrazione è abilitata per il database e le informazioni verranno salvate nell'account di archiviazione.

Facoltativamente, è anche possibile impostare criteri di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, impostare i criteri di conservazione con il flag **-RetentionEnabled** impostato su **$true**. Impostare il parametro **-RetentionInDays** su **90** in modo che i log antecedenti a 90 giorni vengano eliminati automaticamente.

```powershell
Set-AzDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Accedere ai log
I log di Azure Cosmos DB per il **DataPlaneRequests** categoria vengono archiviati nel **insights-log-dataplanerequests** contenitore nell'account di archiviazione fornito dall'utente. 

Creare prima una variabile per il nome contenitore. Questa variabile verrà usata in tutta la procedura dettagliata.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Per elencare tutti i BLOB in questo contenitore, digitare:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

L'output del comando sarà simile all'esempio seguente:

```powershell
ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob
BlobType          : BlockBlob
Length            : 10510193
ContentType       : application/octet-stream
LastModified      : 9/28/2017 7:49:04 PM +00:00
SnapshotTime      :
ContinuationToken:
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.`
                    LazyAzureStorageContext
Name              : resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS`
/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/CONTOSOCOSMOSDB/y=2017/m=09/d=28/h=19/m=00/PT1H.json
```

Come si vede dall'output, i BLOB seguono una convenzione di denominazione: `resourceId=/SUBSCRIPTIONS/<subscription-ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<Database Account Name>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

I valori di data e ora sono nel formato UTC.

Poiché si può usare lo stesso account di archiviazione per raccogliere i log per più risorse, è possibile usare l'ID della risorsa completo nel nome del BLOB per accedere ai BLOB specifici necessari e scaricarli. Prima di procedere, verrà illustrato come scaricare tutti i BLOB.

Creare prima di tutto una cartella per scaricare i BLOB. Ad esempio:

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Ottenere quindi un elenco di tutti i BLOB:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Inviare l'elenco tramite pipe con il comando `Get-AzStorageBlobContent` per scaricare i BLOB nella cartella di destinazione:

```powershell
$blobs | Get-AzStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando si esegue questo secondo comando, il delimitatore **/** nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Questa struttura verrà usata per scaricare e archiviare i BLOB come file.

Per scaricare BLOB in modo selettivo, usare caratteri jolly. Ad esempio:

* Se sono disponibili più database e si vogliono scaricare i log per un solo database denominato **CONTOSOCOSMOSDB3**, usare il comando:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se sono disponibili più gruppi di risorse e si vogliono scaricare i log per un solo gruppo di risorse, usare il comando `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Per scaricare tutti i log per il mese di luglio 2017, usare il comando `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

È anche possibile eseguire i comandi seguenti:

* Per eseguire una query sullo stato delle impostazioni di diagnostica per la risorsa del database, usare il comando `Get-AzDiagnosticSetting -ResourceId $account.ResourceId`.
* Per disabilitare la registrazione della categoria **DataPlaneRequests** per la risorsa dell'account di database, usare il comando `Set-AzDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`.


Gli oggetti BLOB restituiti in ognuna di queste query vengono archiviati come testo e formattati come BLOB JSON, come illustrato nel codice seguente:

```json
{
    "records":
    [
        {
           "time": "Fri, 23 Jun 2017 19:29:50.266 GMT",
           "resourceId": "contosocosmosdb",
           "category": "DataPlaneRequests",
           "operationName": "Query",
           "resourceType": "Database",
           "properties": {"activityId": "05fcf607-6f64-48fe-81a5-f13ac13dd1eb",`
           "userAgent": "documentdb-dotnet-sdk/1.12.0 Host/64-bit MicrosoftWindowsNT/6.2.9200.0 AzureSearchIndexer/1.0.0",`
           "resourceType": "Database","statusCode": "200","documentResourceId": "",`
           "clientIpAddress": "13.92.241.0","requestCharge": "2.260","collectionRid": "",`
           "duration": "9250","requestLength": "72","responseLength": "209", "resourceTokenUserRid": ""}
        }
    ]
}
```

Per informazioni sui dati in ogni BLOB JSON, vedere [Interpretare i log di Azure Cosmos DB](#interpret).

## <a name="manage-your-logs"></a>Gestire i log

I log di diagnostica vengono resi disponibili nell'account due ore dopo avere eseguito l'operazione di Azure Cosmos DB. La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log e limitare l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.
* Il periodo di conservazione per le richieste dei piani dati che vengono archiviate in un account di archiviazione viene configurato nel portale quando si seleziona l'impostazione **Registra DataPlaneRequests**. Per modificare tale impostazione, vedere [Abilitare la registrazione nel portale di Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-azure-monitor-logs"></a>Visualizzare i log di Monitoraggio di Azure

Se è stata selezionata la **Invia a Log Analitica** opzione quando è attivata la registrazione diagnostica, diagnostica dei dati dal contenitore viene inoltrati ai log di monitoraggio di Azure entro due ore. Quando esaminano i log di monitoraggio di Azure immediatamente dopo l'attivazione della registrazione, si non sono visualizzati dati. È sufficiente attendere due ore e riprovare. 

Prima di visualizzare i log, controllare e determinare se l'area di lavoro di Log Analitica è stato aggiornato per usare il nuovo linguaggio di query Kusto. Per verificare, aprire il [portale di Azure](https://portal.azure.com), selezionare **aree di lavoro di Log Analitica** all'estrema sinistra, quindi selezionare il nome dell'area di lavoro come illustrato nella figura seguente. Viene visualizzata la pagina **area di lavoro Log Analytics**:

![Log di monitoraggio di Azure nel portale di Azure](./media/logging/azure-portal.png)

>[!NOTE]
>Le aree di lavoro OMS sono ora denominate aree di lavoro di Log Analytics.  

Se nella pagina **area di lavoro Log Analytics** viene visualizzato il messaggio seguente, l'area di lavoro non è stata aggiornata per l'uso del nuovo linguaggio. Per altre informazioni su come eseguire l'aggiornamento al nuovo linguaggio di query, vedere [Aggiornare l'area di lavoro Azure Log Analytics alla nuova ricerca log](../log-analytics/log-analytics-log-search-upgrade.md). 

![Messaggio dell'aggiornamento di log di monitoraggio di Azure](./media/logging/upgrade-notification.png)

Per visualizzare i dati di diagnostica nel log di monitoraggio di Azure, aprire il **ricerca Log** pagina dal menu a sinistra o nella **gestione** area della pagina, come illustrato nell'immagine seguente:

![Opzioni di Ricerca log nel portale di Azure](./media/logging/log-analytics-open-log-search.png)

Ora che è stata abilitata la raccolta di dati, eseguire l'esempio di ricerca log seguente usando il nuovo linguaggio di query per visualizzare i 10 log più recenti `AzureDiagnostics | take 10`.

![Esempio di ricerca log dei 10 log più recenti](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Query

Ecco alcune query aggiuntive che è possibile inserire nella casella **Ricerca log** per agevolare il monitoraggio dei contenitori Azure Cosmos DB. Queste query usano il [nuovo linguaggio](../log-analytics/log-analytics-log-search-upgrade.md). 

Per informazioni sul significato dei dati restituiti da ogni ricerca log, vedere [Interpretare i log di Azure Cosmos DB](#interpret).

* Per eseguire una query per tutti i log di diagnostica da Azure Cosmos DB per un periodo di tempo specificato:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* Per eseguire una query per i 10 eventi registrati più di recente:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Per eseguire una query per tutte le operazioni, raggruppate per tipo:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Per eseguire una query per tutte le operazioni, raggruppate per **Risorsa**:

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Per eseguire una query per tutte le attività utente, raggruppate per risorsa:

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```
    > [!NOTE]
    > Questo comando è per un log di attività, non per un log di diagnostica.

* Per eseguire una query per le operazioni che richiedono più di 3 millisecondi:

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Per eseguire una query per l'agente che esegue le operazioni:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Per eseguire una query per il momento in cui sono state eseguite le operazioni con esecuzione prolungata:

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Per altre informazioni su come usare il nuovo linguaggio di ricerca Log, vedere [informazioni sulle ricerche log in Monitoraggio di Azure log](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretare i log

I dati di diagnostica archiviati in log di monitoraggio di Azure e archiviazione di Azure usano uno schema analogo. 

La tabella seguente descrive il contenuto di ogni voce di log.

| Proprietà o campo di Archiviazione di Azure | Monitoraggio di Azure registra proprietà | Descrizione |
| --- | --- | --- |
| **time** | **TimeGenerated** | Data e ora (UTC) in cui si è verificata l'operazione. |
| **resourceId** | **Risorsa** | Account Azure Cosmos DB per cui vengono abilitati i log.|
| **category** | **Categoria** | Per i log di Azure Cosmos DB, **DataPlaneRequests** è l'unico valore disponibile. |
| **operationName** | **OperationName** | Nome dell'operazione. Questo valore può corrispondere a una delle operazioni seguenti: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| **properties** | n/d | Il contenuto di questo campo è descritto nelle righe seguenti. |
| **activityId** | **activityId_g** | GUID univoco per l'operazione registrata. |
| **userAgent** | **userAgent_s** | Stringa che specifica l'agente utente del client che esegue la richiesta. Il formato è {nome agente utente}/{versione}.|
| **requestResourceType** | **requestResourceType_s** | Tipo di risorsa di accesso. Questo valore può corrispondere a uno dei tipi di risorse seguenti: Database, Container, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction o Offer. |
| **statusCode** | **statusCode_s** | Stato di risposta dell'operazione. |
| **requestResourceId** | **ResourceId** | ID risorsa relativo alla richiesta. Il valore può puntare a databaseRid, collectionRid o documentRid a seconda dell'operazione eseguita.|
| **clientIpAddress** | **clientIpAddress_s** | Indirizzo IP del client. |
| **requestCharge** | **requestCharge_s** | Numero di unità riservate usate dall'operazione. |
| **collectionRid** | **collectionId_s** | ID univoco per la raccolta.|
| **duration** | **duration_s** | Durata dell'operazione in tick. |
| **requestLength** | **requestLength_s** | Lunghezza della richiesta in byte. |
| **responseLength** | **responseLength_s** | Lunghezza della risposta in byte.|
| **resourceTokenUserRid** | **resourceTokenUserRid_s** | Questo valore è non vuoto quando vengono usati [token di risorsa](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione. Il valore punta all'ID risorsa dell'utente. |

## <a name="next-steps"></a>Passaggi successivi

- Per comprendere come abilitare la registrazione, nonché le metriche e le categorie di log che sono supportate dai vari servizi di Azure, leggere gli articoli [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Panoramica dei log di diagnostica di Azure](../azure-monitor/platform/diagnostic-logs-overview.md).
- Per informazioni sugli hub eventi, leggere gli articoli seguenti:
   - [Che cos'è l'hub di eventi di Azure?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leggere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](../storage/blobs/storage-quickstart-blobs-dotnet.md#download-blobs).
- Lettura [informazioni sulle ricerche log in Monitoraggio di Azure log](../log-analytics/log-analytics-log-search-new.md).
