---
title: Registrazione diagnostica di Azure Cosmos DB | Microsoft Docs
description: Questa esercitazione offre un'introduzione alla registrazione di Azure Cosmos DB.
services: cosmos-db
documentationcenter: ''
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: ''
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: mimig
ms.openlocfilehash: f647387b4e80c36339a456b8e9a2cfade7ac8102
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2018
---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Registrazione diagnostica di Azure Cosmos DB

Dopo aver iniziato a usare uno o più database di Azure Cosmos DB, sarà possibile scegliere di monitorare come e quando viene eseguito l'accesso ai database. Questo articolo fornisce una panoramica di tutti i log disponibili nella piattaforma di Azure e quindi spiega come abilitare la registrazione diagnostica per finalità di monitoraggio, in modo da inviare log ad [Archiviazione di Azure](https://azure.microsoft.com/services/storage/), eseguirne lo streaming in [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) e/o esportali in [Log Analytics](https://azure.microsoft.com/services/log-analytics/), incluso in [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite).

## <a name="logs-available-in-azure"></a>Log disponibili in Azure

Prima di passare al monitoraggio dell'account Azure Cosmos DB, occorre chiarire alcuni concetti sulla registrazione e sul monitoraggio. Nella piattaforma di Azure sono disponibili diversi tipi di log, ovvero [Log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs), [Log di diagnostica di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), [Metriche](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics), eventi, monitoraggio di heartbeat, registri operazioni e così via. Sono disponibili moltissimi log. Per un elenco completo dei log, vedere [Azure Log Analytics](https://azure.microsoft.com/en-us/services/log-analytics/) nel portale di Azure. 

L'immagine seguente mostra i diversi tipi di log di Azure disponibili.

![Diversi tipi di log di Azure](./media/logging/azurelogging.png)

Nella figura precedente le **risorse di calcolo** rappresentano le risorse di Azure per le quali è possibile accedere al sistema operativo Guest. Ad esempio, le macchine virtuali di Azure, i set di scalabilità di Azure, il servizio contenitore di Azure e così via vengono considerati come risorse di calcolo. Le risorse di calcolo generano log attività, log di diagnostica e log applicazioni. Per altre informazioni, vedere l'articolo [Monitoraggio di Azure: risorse di calcolo](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---compute-subset).

Le **risorse non di calcolo** sono risorse in cui non è possibile accedere al sistema operativo sottostante e utilizzare direttamente la risorsa. Esempi di risorse non di calcolo sono i gruppi di sicurezza di rete, le app per la logica e così via. **Cosmos DB** è una risorsa non di calcolo. È possibile visualizzare i log delle risorse non di calcolo nel log attività oppure abilitando l'opzione Log di diagnostica nel portale. Per altre informazioni, vedere l'articolo [Monitoraggio di Azure: risorse non di calcolo](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#azure-monitor-sources---everything-else).

Il log attività registra le operazioni a livello di sottoscrizione di Cosmos DB, ad esempio ListKeys, Write DatabaseAccounts e così via. I log di diagnostica offrono un livello di registrazione più granulare e consentono di registrare DataPlaneRequests (Create, Read, Query e così via) e MongoRequests.


Questo argomento è incentrato sui log per attività di Azure, diagnostica di Azure e metriche e sulle differenze tra questi tre tipi di log. 

### <a name="azure-activity-log"></a>Azure Activity Log

Il log attività di Azure è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Il log attività segnala eventi a livello di piano di controllo per le sottoscrizioni sotto la categoria amministrativa. L'uso del log attività permette di acquisire informazioni dettagliate su qualsiasi operazione di scrittura (PUT, POST, DELETE) eseguita sulle risorse nella sottoscrizione. Consente inoltre di comprendere lo stato dell'operazione e altre proprietà specifiche. 

Il log attività è diverso dal log di diagnostica. I log attività contengono dati relativi alle operazioni su una risorsa esterna (il "piano di controllo"). Nel contesto di Azure Cosmos DB alcune operazioni a livello di piano di controllo creano raccolte, elencano chiavi, eliminano chiavi, elencano database e così via. I log di diagnostica vengono generati da una risorsa e contengono informazioni sul funzionamento di tale risorsa (il "piano dati"). Alcuni esempi di log di diagnostica del piano dati saranno operazioni di eliminazione, inserimento, lettura di feed e così via.

I log attività (operazioni a livello di piano di controllo) possono essere più dettagliati, possono includere l'indirizzo di posta elettronica completo del chiamante, l'indirizzo IP del chiamante, il nome della risorsa, il nome dell'operazione, l'ID del tenant e così via. Il log attività contiene alcune [categorie](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema) di dati. Per informazioni dettagliate sugli schemi di queste categorie, vedere [Schema degli eventi del log attività di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-activity-log-schema).  I log di diagnostica, tuttavia, possono essere restrittivi, perché i dati relativi alle informazioni personali vengono spesso rimossi. È quindi possibile che sia disponibile l'indirizzo IP del chiamante, ma senza l'ultimo ottetto.

### <a name="azure-metrics"></a>Metriche di Azure

Le [metriche di Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-metrics) includono il tipo più importante di dati di telemetria di Azure, definiti anche contatori delle prestazioni, emessi dalla maggior parte delle risorse di Azure. Le metriche consentono di visualizzare informazioni su velocità effettiva, risorse di archiviazione, coerenza, disponibilità e latenza delle risorse di Azure Cosmos DB. Per altre informazioni, vedere [Eseguire il monitoraggio e il debug con le metriche in Azure Cosmos DB](use-metrics.md).

### <a name="azure-diagnostic-logs"></a>Log di diagnostica di Azure

I log di diagnostica di Azure sono generati da una risorsa che offre dati completi e frequenti sul funzionamento della risorsa stessa. Il contenuto di questi log varia in base al tipo di risorsa. I log di diagnostica a livello di risorsa differiscono anche dal log di diagnostica a livello del sistema operativo guest. I log di diagnostica del sistema operativo guest vengono compilati da un agente in esecuzione all'interno di una macchina virtuale o di un altro tipo di risorsa supportato. I log di diagnostica a livello di risorsa non richiedono l'uso di un agente e acquisiscono i dati specifici della risorsa dalla piattaforma di Azure stessa, mentre i log di diagnostica a livello del sistema operativo guest acquisiscono i dati dal sistema operativo e dalle applicazioni in esecuzione in una macchina virtuale.

![Registrazione diagnostica in Archiviazione, hub eventi oppure Operations Management Suite mediante Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

### <a name="what-is-logged-by-azure-diagnostic-logs"></a>Informazioni registrate dai log di diagnostica di Azure

* Vengono registrate tutte le richieste back-end autenticate (TCP/REST) in tutte le API, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide. Il supporto per le richieste di API di tabella, Graph e Cassandra avviate dall'utente non è attualmente disponibile.
* Operazioni nel database stesso, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.
* Operazioni sulle chiavi di account, che includono la creazione, la modifica o l'eliminazione di queste chiavi.
* Richieste non autenticate che generano una risposta 401. Ad esempio, richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.

<a id="#turn-on"></a>
## <a name="turn-on-logging-in-the-azure-portal"></a>Abilitare la registrazione nel portale di Azure

Per abilitare la registrazione diagnostica, sono necessarie le risorse seguenti:

* Un account, un database e un contenitore Azure Cosmos DB esistente. Per istruzioni sulla creazione di queste risorse, vedere [Create a database account using the Azure portal](create-sql-api-dotnet.md#create-a-database-account) (Creare un account di database tramite il portale di Azure), [Esempi dell'interfaccia della riga di comando](cli-samples.md) o [Esempi di PowerShell](powershell-samples.md).

Per abilitare la registrazione diagnostica nel portale di Azure, seguire questa procedura:

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com) fare clic su **Log di diagnostica** nel riquadro di spostamento a sinistra e quindi su **Abilita diagnostica**.

    ![Abilitare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/logging/turn-on-portal-logging.png)

2. Nella pagina **Impostazioni di diagnostica** eseguire le operazioni seguenti: 

    * **Nome**. Immettere un nome per i log da creare.

    * **Archivia in un account di archiviazione**. Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Per creare un nuovo account di archiviazione nel portale, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md) e seguire le istruzioni per creare un account Resource Manager di uso generico. Tornare quindi a questa pagina del portale per selezionare l'account di archiviazione. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.
    * **Streaming in un hub eventi**. Per usare questa opzione, sono necessari uno spazio dei nomi esistente e un hub eventi a cui connettersi. Per creare uno spazio dei nomi dell'hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). Tornare a questa pagina del portale per selezionare lo spazio dei nomi dell'hub eventi e il nome dei criteri.
    * **Invia a Log Analytics**.     Per usare questa opzione, usare un'area di lavoro esistente o creare una nuova area di lavoro di Log Analytics seguendo la procedura per [creare una nuova area di lavoro](../log-analytics/log-analytics-quick-collect-azurevm.md#create-a-workspace) nel portale. Per altre informazioni sulla visualizzazione dei log in Log Analytics, vedere [Visualizzare i log in Log Analytics](#view-in-loganalytics).
    * **Registra DataPlaneRequests**. Selezionare questa opzione per registrare le richieste back-end dalla piattaforma distribuita sottostante di Azure Cosmos DB per gli account SQL, Graph, MongoDB, Cassandra e API di tabella. Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Registra MongoRequests**. Selezionare questa opzione per registrare le richieste avviate dall'utente da front-end di Azure Cosmos DB per la gestione di account API MongoDB.  Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.
    * **Metric Requests** (Richieste Metriche). Selezionare questa opzione per archiviare i dati dettagliati in [Metriche di Azure](../monitoring-and-diagnostics/monitoring-supported-metrics.md). Se si esegue l'archiviazione in un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica. Alla scadenza del periodo, i log verranno automaticamente eliminati.

3. Fare clic su **Save**.

    Se si riceve il messaggio di errore "Non è stato possibile aggiornare la diagnostica per \<nome area di lavoro>. La sottoscrizione \<id sottoscrizione> non è registrata per l'uso di microsoft.insights." seguire le istruzioni in [Risolvere i problemi relativi a Diagnostica di Azure](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage) per registrare l'account, quindi ripetere questa procedura.

    Se in seguito si vuole modificare la modalità di salvataggio dei log di diagnostica, è possibile tornare in qualsiasi momento in questa pagina per modificare le impostazioni di tali log per l'account.

## <a name="turn-on-logging-using-cli"></a>Abilitare la registrazione mediante interfaccia della riga di comando

Per abilitare le metriche e la registrazione diagnostica tramite l'interfaccia della riga di comando di Azure, usare i comandi seguenti:

- Per abilitare la memorizzazione dei log di diagnostica in un account di archiviazione, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   `resourceId` è il nome dell'account del database Azure Cosmos DB. `storageId` è il nome dell'account di archiviazione a cui si desidera inviare i log.

- Per abilitare la trasmissione dei log di diagnostica a un hub eventi, usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   `resourceId` è il nome dell'account del database Azure Cosmos DB. `serviceBusRuleId` è una stringa nel formato seguente:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Per consentire l'invio dei log di diagnostica all'area di lavoro di Log Analytics , usare questo comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

È possibile combinare questi parametri per abilitare più opzioni di output.

## <a name="turn-on-logging-using-powershell"></a>Abilitare la registrazione mediante PowerShell

Per abilitare la registrazione diagnostica mediante PowerShell, è necessario avere almeno la versione 1.0.1 di Azure PowerShell.

Per installare Azure PowerShell e associarlo alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

Se Azure PowerShell è già stato installato ma non si conosce la versione, dalla console di PowerShell digitare `(Get-Module azure -ListAvailable).Version`.  

### <a id="connect"></a>Connettersi alle sottoscrizioni
Avviare una sessione di Azure PowerShell e accedere all'account Azure con il comando seguente:  

```powershell
Login-AzureRmAccount
```

Nella finestra del browser a comparsa, immettere il nome utente e la password dell'account Azure. Azure PowerShell recupera tutte le sottoscrizioni associate a questo account e, per impostazione predefinita, usa la prima.

Se sono disponibili più sottoscrizioni, potrebbe essere necessario indicarne una specifica usata per creare l'insieme di credenziali delle chiavi di Azure. Digitare il comando seguente per visualizzare le sottoscrizioni relative all'account:

```powershell
Get-AzureRmSubscription
```

Per specificare quindi la sottoscrizione associata all'account del database di Azure Cosmos DB da registrare, digitare:

```powershell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Se si hanno più sottoscrizioni associate all'account, è importante specificare la sottoscrizione.
>
>

Per altre informazioni sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a id="storage"></a>Creare un nuovo account di archiviazione per i log
Anche se è possibile usare un account di archiviazione esistente per i log, in questa esercitazione si creerà un nuovo account di archiviazione dedicato ai log di Azure Cosmos DB. Per praticità, i dettagli dell'account di archiviazione verranno memorizzati in una variabile denominata **sa**.

Per rendere la gestione ancora più facile, in questa esercitazione si userà lo stesso gruppo di risorse che contiene il database di Azure Cosmos DB. Sostituire i valori per ContosoResourceGroup e contosocosmosdblogs e "North Central US" con valori personali, come applicabile:

```powershell
$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup `
-Name contosocosmosdblogs -Type Standard_LRS -Location 'North Central US'
```

> [!NOTE]
> Se si sceglie di usare un account di archiviazione esistente, questo dovrà usare la stessa sottoscrizione di Azure Cosmos DB e il modello di distribuzione Resource Manager, anziché il modello di distribuzione classica.
>
>

### <a id="identify"></a>Identificare l'account di Azure Cosmos DB per i log
Impostare il nome dell'account Azure Cosmos DB su una variabile denominata **account**, dove ResourceName è il nome dell'account Azure Cosmos DB.

```powershell
$account = Get-AzureRmResource -ResourceGroupName ContosoResourceGroup `
-ResourceName contosocosmosdb -ResourceType "Microsoft.DocumentDb/databaseAccounts"
```

### <a id="enable"></a>Abilitare la registrazione
Per abilitare la registrazione per Azure Cosmos DB, usare il cmdlet Set-AzureRmDiagnosticSetting insieme alle variabili per il nuovo account di archiviazione, l'account Azure Cosmos DB e la categoria per cui si desidera abilitare i log. Eseguire questo comando, impostando il flag **-Enabled** su **$true**:

```powershell
Set-AzureRmDiagnosticSetting  -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests
```

L'output del comando sarà simile al seguente:

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

Conferma che la registrazione è abilitata per il database. Le informazioni vengono salvate nell'account di archiviazione.

Facoltativamente è possibile impostare criteri di conservazione per i log, in modo che i log meno recenti vengano eliminati automaticamente. Ad esempio, specificare i criteri di conservazione impostando il flag **-RetentionEnabled** su **$true** e il parametro **-RetentionInDays** su **90** per fare in modo che i log che risalgono a più di 90 giorni prima vengano eliminati automaticamente.

```powershell
Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
 -StorageAccountId $sa.Id -Enabled $true -Categories DataPlaneRequests`
  -RetentionEnabled $true -RetentionInDays 90
```

### <a id="access"></a>Accedere ai log
I log di Azure Cosmos DB per la categoria **DataPlaneRequests** vengono archiviati nel contenitore **insights-logs-data-plane-requests** nell'account di archiviazione specificato. 

Creare prima una variabile per il nome contenitore. Questa variabile verrà usata nel resto della procedura dettagliata.

```powershell
    $container = 'insights-logs-dataplanerequests'
```

Per elencare tutti i BLOB in questo contenitore, digitare:

```powershell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

L'output sarà simile al seguente:

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

Poiché si può usare lo stesso account di archiviazione per raccogliere i log per più risorse, l'ID della risorsa completo nel nome del BLOB è molto utile per accedere solo ai BLOB necessari o per scaricarli. Prima di procedere, si vedrà però come scaricare tutti i BLOB.

Creare prima di tutto una cartella per scaricare i BLOB. Ad esempio: 

```powershell
New-Item -Path 'C:\Users\username\ContosoCosmosDBLogs'`
 -ItemType Directory -Force
```

Ottenere quindi un elenco di tutti i BLOB:  

```powershell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Inviare l'elenco tramite pipe con 'Get-AzureStorageBlobContent' per scaricare i BLOB nella cartella di destinazione:

```powershell
$blobs | Get-AzureStorageBlobContent `
 -Destination 'C:\Users\username\ContosoCosmosDBLogs'
```

Quando si esegue questo secondo comando, il delimitatore **/** nei nomi dei BLOB crea una struttura di cartelle completa nella cartella di destinazione. Questa struttura verrà usata per scaricare e archiviare i BLOB come file.

Per scaricare BLOB in modo selettivo, usare caratteri jolly. Ad esempio: 

* Se sono disponibili più database e si vogliono scaricare i log per un solo database, denominato CONTOSOCOSMOSDB3, usare:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/DATABASEACCOUNTS/CONTOSOCOSMOSDB3
    ```

* Se sono disponibili più gruppi di risorse e si vogliono scaricare i log per un solo gruppo di risorse, usare `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
    -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
    ```
* Per scaricare tutti i log per il mese di luglio 2017, usare `-Blob '*/year=2017/m=07/*'`:

    ```powershell
    Get-AzureStorageBlob -Container $container `
     -Context $sa.Context -Blob '*/year=2017/m=07/*'
    ```

Eseguire anche queste operazioni:

* Per eseguire una query sullo stato delle impostazioni di diagnostica per la risorsa del database, usare: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Per disabilitare la registrazione della categoria **DataPlaneRequests** per la risorsa dell'account di database: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`


Gli oggetti BLOB restituiti in ognuna di queste query vengono archiviati come testo in formato BLOB JSON, come illustrato nel codice seguente. 

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

## <a name="managing-your-logs"></a>Gestione dei log

I log di diagnostica vengono resi disponibili nell'account due ore dopo aver eseguito l'operazione di Azure Cosmos DB. La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.
* Il periodo di conservazione per le richieste dei piani dati archiviate in un account di archiviazione viene configurato nel portale quando si seleziona **Registra DataPlaneRequests**. Per modificare tale impostazione, vedere [Abilitare la registrazione nel portale di Azure](#turn-on-logging-in-the-azure-portal).


<a id="#view-in-loganalytics"></a>
## <a name="view-logs-in-log-analytics"></a>Visualizzare i log in Log Analytics

Se all'attivazione della registrazione diagnostica è stata selezionata l'opzione **Invia a Log Analytics**, i dati di diagnostica della raccolta vengono inoltrati a Log Analytics entro due ore. Ciò significa che, immediatamente dopo l'attivazione della registrazione, in Log Analytics non sono visualizzati dati. È sufficiente attendere due ore e riprovare. 

Prima di visualizzare i log, è necessario verificare se l'area di lavoro di Log Analytics è stata aggiornata per l'uso del nuovo linguaggio di query di Log Analytics. A tale scopo, aprire il [portale di Azure](https://portal.azure.com), fare clic su **Log Analytics** all'estrema sinistra e quindi selezionare il nome dell'area di lavoro come illustrato nella figura seguente. La pagina **Area di lavoro di OMS** viene visualizzata come illustrato nella figura seguente.

![Log Analytics nel portale di Azure](./media/logging/azure-portal.png)

Se nella pagina **Area di lavoro di OMS** viene visualizzato il messaggio seguente, l'area di lavoro non è stata aggiornata per l'uso del nuovo linguaggio. Per altre informazioni sull'aggiornamento al nuovo linguaggio di query, vedere [Aggiornare l'area di lavoro di Azure Log Analytics alla nuova ricerca log](../log-analytics/log-analytics-log-search-upgrade.md). 

![Notifica dell'aggiornamento di Log Analytics](./media/logging/upgrade-notification.png)

Per visualizzare i dati di diagnostica in Log Analytics, aprire la pagina Ricerca log dal menu a sinistra o nell'area Gestione della pagina, come illustrato nella figura seguente.

![Opzioni di Ricerca log nel portale di Azure](./media/logging/log-analytics-open-log-search.png)

Ora che è stata abilitata la raccolta di dati, eseguire l'esempio di ricerca log seguente, usando il nuovo linguaggio di query, per visualizzare i dieci log più recenti `AzureDiagnostics | take 10`.

![Ricerca di esempio di 10 log](./media/logging/log-analytics-query.png)

<a id="#queries"></a>
### <a name="queries"></a>Query

Ecco alcune query aggiuntive che è possibile inserire nella casella **Ricerca log** per agevolare il monitoraggio dei contenitori Azure Cosmos DB. Queste query usano il [nuovo linguaggio](../log-analytics/log-analytics-log-search-upgrade.md). 

Per informazioni sul significato dei dati restituiti da ogni ricerca log, vedere [Interpretare i log di Azure Cosmos DB](#interpret).

* Tutti i log di diagnostica da Azure Cosmos DB per il periodo di tempo specificato.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
    ```

* I dieci eventi registrati più di recente.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | take 10
    ```

* Tutte le operazioni, raggruppate per tipo.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName
    ```

* Tutte le operazioni, raggruppate per risorsa.

    ```
    AzureActivity | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Tutte le attività utente, raggruppate per risorsa. Si noti che questo è un log di attività, non un log di diagnostica.

    ```
    AzureActivity | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by Resource
    ```

* Operazioni che richiedono più di 3 millisecondi.

    ```
    AzureDiagnostics | where toint(duration_s) > 30000 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Agente che esegue le operazioni.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | summarize count() by OperationName, userAgent_s
    ```

* Il momento in cui sono state eseguite le operazioni con esecuzione prolungata.

    ```
    AzureDiagnostics | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" | project TimeGenerated , toint(duration_s)/1000 | render timechart
    ```

Per altre informazioni sull'uso del nuovo linguaggio di ricerca log, vedere [Informazioni sulle ricerche log in Log Analytics](../log-analytics/log-analytics-log-search-new.md). 

## <a id="interpret"></a>Interpretare i log

I dati di diagnostica archiviati in Archiviazione di Azure e in Log Analytics usano uno schema molto simile. 

La tabella seguente descrive il contenuto di ogni voce di log.

| Proprietà o campo di Archiviazione di Azure | Proprietà di Log Analytics | DESCRIZIONE |
| --- | --- | --- |
| time | TimeGenerated | Data e ora (UTC) in cui si è verificata l'operazione. |
| ResourceId | Risorsa | Account Azure Cosmos DB per cui vengono abilitati i log.|
| category | Categoria | Per i log di Azure Cosmos DB, DataPlaneRequests è l'unico valore disponibile. |
| operationName | OperationName | Nome dell'operazione. Questo valore può essere una delle operazioni seguenti: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| properties | n/d | Il contenuto di questo campo è descritto nelle righe seguenti. |
| activityId | activityId_g | GUID univoco per l'operazione registrata. |
| userAgent | userAgent_s | Stringa che specifica l'agente utente del client che esegue la richiesta. Il formato è {nome agente utente}/{versione}.|
| resourceType | ResourceType | Tipo di risorsa di accesso. Questo valore può essere uno dei tipi di risorsa seguenti: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction oppure Offer. |
| statusCode |statusCode_s | Stato di risposta dell'operazione. |
| requestResourceId | ResourceId | ID di risorsa relativo alla richiesta, che può puntare a databaseRid, collectionRid o documentRid a seconda dell'operazione eseguita.|
| clientIpAddress | clientIpAddress_s | Indirizzo IP del client. |
| requestCharge | requestCharge_s | Numero di unità riservate usate dall'operazione |
| collectionRid | collectionId_s | ID univoco per la raccolta.|
| duration | duration_s | Durata dell'operazione in tick. |
| requestLength | requestLength_s | Lunghezza della richiesta in byte. |
| responseLength | responseLength_s | Lunghezza della risposta in byte.|
| resourceTokenUserRid | resourceTokenUserRid_s | Non vuoto se vengono usati [token di risorsa](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione che puntano all'ID risorsa dell'utente. |

## <a name="next-steps"></a>Passaggi successivi

- Per comprendere non solo come abilitare la registrazione, ma anche le metriche e le categorie di log supportate dai vari servizi di Azure, leggere fino in fondo gli articoli [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Per informazioni sugli hub eventi, leggere gli articoli seguenti:
   - [Che cos'è Hub eventi?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Leggere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)
- Leggere [Informazioni sulle ricerche log in Log Analytics](../log-analytics/log-analytics-log-search-new.md)
