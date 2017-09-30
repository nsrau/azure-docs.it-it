---
title: Registrazione diagnostica di Azure Cosmos DB | Microsoft Docs
description: Questa esercitazione offre un'introduzione alla registrazione di Azure Cosmos DB.
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
tags: azure-resource-manager
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 34c46fb282ad154225f5ee8ef544bc8da1c50016
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="azure-cosmos-db-diagnostic-logging"></a>Registrazione diagnostica di Azure Cosmos DB

Dopo aver iniziato a usare uno o più database di Azure Cosmos DB, sarà possibile scegliere di monitorare come e quando viene eseguito l'accesso ai database. La registrazione diagnostica in Azure Cosmos DB consente di eseguire questo monitoraggio. Abilitando la registrazione diagnostica, è possibile inviare log ad [Archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli agli [hub eventi di Azure](https://azure.microsoft.com/en-us/services/event-hubs/) e/o esportarli in un'area di lavoro [Operations Management Suite](https://www.microsoft.com/cloud-platform/operations-management-suite) mediante [Log Analytics](https://azure.microsoft.com/services/log-analytics/).

![Registrazione diagnostica in Archiviazione, hub eventi oppure Operations Management Suite mediante Log Analytics](./media/logging/azure-cosmos-db-logging-overview.png)

Usare questa esercitazione per un'introduzione alla registrazione di Azure Cosmos DB tramite il portale di Azure, l'interfaccia della riga di comando o PowerShell.

## <a name="what-is-logged"></a>Che cosa viene registrato?

* Vengono registrate tutte le richieste API REST DocumentDB (SQL) autenticate, incluse le richieste non riuscite a causa di autorizzazioni di accesso, errori di sistema o richieste non valide. Il supporto per le API MongoDB, Graph e di tabella non è attualmente disponibile.
* Operazioni nel database stesso, incluse le operazioni CRUD su tutti i documenti, i contenitori e i database.
* Operazioni sulle chiavi di account, che includono la creazione, la modifica o l'eliminazione di queste chiavi.
* Richieste non autenticate che generano una risposta 401. Ad esempio, richieste che non hanno un token di connessione, hanno un formato non valido, sono scadute o hanno un token non valido.

## <a name="prerequisites"></a>Prerequisiti
Per completare l'esercitazione, sono necessarie le risorse seguenti:

* Un account, un database e un contenitore Azure Cosmos DB esistente. Per istruzioni sulla creazione di queste risorse, vedere [Create a database account using the Azure portal](create-documentdb-dotnet.md#create-a-database-account) (Creare un account di database tramite il portale di Azure), [Esempi dell'interfaccia della riga di comando](cli-samples.md) o [Esempi di PowerShell](powershell-samples.md).


## <a name="turn-on-logging-in-the-azure-portal"></a>Abilitare la registrazione nel portale di Azure

1. Nell'account Azure Cosmos DB nel [portale di Azure](https://portal.azure.com) fare clic su **Log di diagnostica** nel riquadro di spostamento a sinistra e quindi su **Abilita diagnostica**.

    ![Abilitare la registrazione diagnostica per Azure Cosmos DB nel portale di Azure](./media/logging/turn-on-portal-logging.png)

2. Nella pagina **Impostazioni di diagnostica** eseguire le operazioni seguenti: 

    * **Nome**. Immettere un nome per i log da creare.

    * **Archivia in un account di archiviazione**. Per usare questa opzione, è necessario un account di archiviazione esistente a cui connettersi. Per creare un nuovo account di archiviazione nel portale, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md) e seguire le istruzioni per creare un account Resource Manager di uso generico. Tornare quindi a questa pagina del portale per selezionare l'account di archiviazione. Potrebbero essere necessari alcuni minuti per visualizzare gli account di archiviazione appena creati nel menu a discesa.
    * **Streaming in un hub eventi**. Per usare questa opzione, sono necessari uno spazio dei nomi esistente e un hub eventi a cui connettersi. Per creare uno spazio dei nomi dell'hub eventi, vedere [Creare uno spazio dei nomi di Hub eventi e un hub eventi usando il Portale di Azure](../event-hubs/event-hubs-create.md). Tornare a questa pagina del portale per selezionare lo spazio dei nomi dell'hub eventi e il nome dei criteri.
    * **Invia a Log Analytics**. Per questa opzione, usare un'area di lavoro esistente o creare una nuova area di lavoro di Operations Management Suite seguendo le istruzioni visualizzate nel portale.
    * **Registra DataPlaneRequests**. In fase di memorizzazione di un account di archiviazione, è possibile selezionare il periodo di conservazione per i log di diagnostica selezionando **DataPlaneRequests** e scegliendo il numero di giorni di conservazione dei log. Alla scadenza del periodo, i log verranno automaticamente eliminati. 

3. Fare clic su **Salva**.

    È possibile tornare a questa pagina in qualsiasi momento per modificare le impostazioni del log di diagnostica per l'account.

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

Per abilitare la registrazione mediante PowerShell, è necessario avere almeno la versione 1.0.1 di Azure PowerShell.

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

Per scaricare BLOB in modo selettivo, usare caratteri jolly. ad esempio:

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

A questo punto si può iniziare a osservare il contenuto dei log. Prima di continuare, ci sono altri due parametri di `Get-AzureRmDiagnosticSetting` che può essere necessario conoscere:

* Per eseguire una query sullo stato delle impostazioni di diagnostica per la risorsa del database, usare: `Get-AzureRmDiagnosticSetting -ResourceId $account.ResourceId`
* Per disabilitare la registrazione della categoria **DataPlaneRequests** per la risorsa dell'account di database: `Set-AzureRmDiagnosticSetting -ResourceId $account.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories DataPlaneRequests`

## <a id="interpret"></a>Interpretare i log di Azure Cosmos DB
I singoli BLOB vengono archiviati come testo, formattati come BLOB JSON. Questo JSON è un esempio di voce di registro:

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


La tabella seguente elenca i nomi dei campi e le descrizioni.

| Nome campo | Descrizione |
| --- | --- |
| time |Data e ora (UTC) in cui si è verificata l'operazione. |
| resourceId |Account Azure Cosmos DB per cui vengono abilitati i log.|
| category |Per i log di Azure Cosmos DB, DataPlaneRequests è l'unico valore disponibile. |
| operationName |Nome dell'operazione. Questo valore può essere una delle operazioni seguenti: Create, Update, Read, ReadFeed, Delete, Replace, Execute, SqlQuery, Query, JSQuery, Head, HeadFeed o Upsert.   |
| properties |Il contenuto di questo campo è descritto nella tabella seguente. |

La tabella seguente elenca i campi registrati all'interno del campo delle proprietà.

| Nome del campo delle proprietà | Descrizione |
| --- | --- |
| activityId | GUID univoco per l'operazione registrata. |
| userAgent |Stringa che specifica l'agente utente del client che esegue la richiesta. Il formato è {nome agente utente}/{versione}.|
| resourceType | Tipo di risorsa di accesso. Questo valore può essere uno dei tipi di risorsa seguenti: Database, Collection, Document, Attachment, User, Permission, StoredProcedure, Trigger, UserDefinedFunction oppure Offer. |
| statusCode |Stato di risposta dell'operazione. |
| requestResourceId | ID di risorsa relativo alla richiesta, che può puntare a databaseRid, collectionRid o documentRid a seconda dell'operazione eseguita.|
| clientIpAddress |Indirizzo IP del client. |
| requestCharge | Numero di unità riservate usate dall'operazione |
| collectionRid | ID univoco per la raccolta.|
| duration | Durata dell'operazione in tick. |
| requestLength |Lunghezza della richiesta in byte. |
| responseLength | Lunghezza della risposta in byte.|
| resourceTokenUserRid | Non vuoto se vengono usati [token di risorsa](https://docs.microsoft.com/en-us/azure/cosmos-db/secure-access-to-data#resource-tokens) per l'autenticazione che puntano all'ID risorsa dell'utente. |

## <a name="managing-your-logs"></a>Gestione dei log

I log vengono resi disponibili nell'account due ore dopo aver eseguito l'operazione di Azure Cosmos DB. La gestione dei log nell'account di archiviazione è compito dell'utente:

* Usare i metodi di controllo di accesso standard di Azure per proteggere i log limitando l'accesso agli utenti specificati.
* Eliminare i log che non è più necessario mantenere nell'account di archiviazione.
* Il periodo di conservazione per le richieste dei piani dati archiviate in un account di archiviazione viene configurato nel portale quando si seleziona **Registra DataPlaneRequests**. Per modificare tale impostazione, vedere [Abilitare la registrazione nel portale di Azure](#turn-on-logging-in-the-azure-portal).

## <a name="next-steps"></a>Passaggi successivi

- Per comprendere non solo come abilitare la registrazione, ma anche le metriche e le categorie di log supportate dai vari servizi di Azure, leggere fino in fondo gli articoli [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Panoramica dei log di diagnostica di Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).
- Per informazioni sugli hub eventi, leggere gli articoli seguenti:
   - [Cosa sono gli hub eventi di Azure](../event-hubs/event-hubs-what-is-event-hubs.md)?
   - [Introduzione all'Hub eventi](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Vedere [Scaricare le metriche e i log di diagnostica da Archiviazione di Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md#download-blobs)

