---
title: Metriche di Archiviazione di Azure in Monitoraggio di Azure | Microsoft Docs
description: Informazioni sulle nuove metriche offerte da Monitoraggio di Azure.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 09/05/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: e9d11f7426a70d058daa75466b977e47e6e33ee8
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505771"
---
# <a name="azure-storage-metrics-in-azure-monitor"></a>Metriche di Archiviazione di Azure in Monitoraggio di Azure

Le metriche di Archiviazione di Azure consentono di analizzare le tendenze di utilizzo, tenere traccia delle richieste e diagnosticare i problemi dell'account di archiviazione.

Monitoraggio di Azure fornisce interfacce utente unificate per il monitoraggio tra diversi servizi di Azure. Per altre informazioni, vedere [Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview.md). Archiviazione di Azure integra Monitoraggio di Azure inviando dati relativi alle metriche alla piattaforma di Monitoraggio di Azure.

## <a name="access-metrics"></a>Accedere alle metriche

Monitoraggio di Azure offre molti modi per accedere alle metriche. È possibile accedervi dal [portale di Azure](https://portal.azure.com), le API di monitoraggio di Azure (REST e .NET) e le soluzioni di analisi, ad esempio hub eventi. Per altre informazioni, vedere [Metriche di Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

Le metriche sono abilitate per impostazione predefinita ed è possibile accedere agli ultimi 93 giorni di dati. Se è necessario conservare i dati per un periodo di tempo più lungo, è possibile archiviare i dati relativi alle metriche in un account di archiviazione di Azure. Questo approccio viene configurato nelle [Impostazioni di diagnostica](../../azure-monitor/platform/diagnostic-logs-overview.md) in Monitoraggio di Azure.

### <a name="access-metrics-in-the-azure-portal"></a>Accedere alle metriche nel portale di Azure

È possibile monitorare le metriche nel tempo nel portale di Azure. L'esempio seguente mostra come visualizzare la metrica **Transactions** a livello di account.

![Screenshot dell'accesso alle metriche nel portale di Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal.png)

Per le metriche che supportano le dimensioni, è possibile applicare un filtro specificando il valore di dimensione desiderato. L'esempio seguente mostra come visualizzare **Transactions** a livello di account su un'operazione specifica selezionando valori per la dimensione **API Name**.

![Screenshot dell'accesso alle metriche con dimensioni nel portale di Azure](./media/storage-metrics-in-azure-monitor/access-metrics-in-portal-with-dimension.png)

### <a name="access-metrics-with-the-rest-api"></a>Accedere alle metriche con l'API REST

Monitoraggio di Azure fornisce le [API REST](/rest/api/monitor/) per leggere definizioni e valori delle metriche. Questa sezione illustra come leggere le metriche di archiviazione. L'ID risorsa viene usato in tutte le API REST. Per altre informazioni, leggere Understanding resource ID for services in Storage (Informazioni sull'ID risorsa per i servizi in Archiviazione).

L'esempio seguente mostra come usare [ArmClient](https://github.com/projectkudu/ARMClient) nella riga di comando per semplificare i test con l'API REST.

#### <a name="list-account-level-metric-definition-with-the-rest-api"></a>Elencare le definizioni delle metriche a livello di account con l'API REST

L'esempio seguente mostra come elencare le definizioni delle metriche a livello di account:

```
# Login to Azure and enter your credentials when prompted.
> armclient login

> armclient GET /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions?api-version=2018-01-01

```

Per elencare le definizioni delle metriche per BLOB, tabelle, file o code, è necessario specificare ID risorsa diversi per ogni servizio con l'API.

La risposta contiene la definizione della metrica in formato JSON:

```Json
{
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metricdefinitions/UsedCapacity",
      "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
      "category": "Capacity",
      "name": {
        "value": "UsedCapacity",
        "localizedValue": "Used capacity"
      },
      "isDimensionRequired": false,
      "unit": "Bytes",
      "primaryAggregationType": "Average",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D"
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D"
        }
      ]
    },
    ... next metric definition
  ]
}

```

#### <a name="read-account-level-metric-values-with-the-rest-api"></a>Leggere i valori delle metriche a livello di account con l'API REST

L'esempio seguente mostra come leggere i dati relativi alle metriche a livello di account:

```
> armclient GET "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/microsoft.insights/metrics?metricnames=Availability&api-version=2018-01-01&aggregation=Average&interval=PT1H"

```

Se si vogliono leggere i valori delle metriche nell'esempio precedente per BLOB, tabelle, file o code, è necessario specificare ID risorsa diversi per ogni servizio con l'API.

La risposta seguente contiene valori di metriche in formato JSON:

```Json
{
  "cost": 0,
  "timespan": "2017-09-07T17:27:41Z/2017-09-07T18:27:41Z",
  "interval": "PT1H",
  "value": [
    {
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/providers/Microsoft.Insights/metrics/Availability",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Availability",
        "localizedValue": "Availability"
      },
      "unit": "Percent",
      "timeseries": [
        {
          "metadatavalues": [],
          "data": [
            {
              "timeStamp": "2017-09-07T17:27:00Z",
              "average": 100.0
            }
          ]
        }
      ]
    }
  ]
}

```

### <a name="access-metrics-with-the-net-sdk"></a>Accedere alle metriche con .NET SDK

Monitoraggio di Azure offre [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor/) per leggere definizioni delle metriche e i valori. Il [codice di esempio](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) illustra come usare l'SDK con parametri diversi. È necessario usare `0.18.0-preview` o versione successiva per le metriche di archiviazione. ID risorsa viene usato in .NET SDK. Per altre informazioni, leggere Understanding resource ID for services in Storage (Informazioni sull'ID risorsa per i servizi in Archiviazione).

Nell'esempio seguente viene illustrato come usare .NET SDK di monitoraggio di Azure per la lettura delle metriche di archiviazione.

#### <a name="list-account-level-metric-definition-with-the-net-sdk"></a>Elencare le definizioni delle metriche a livello di account con .NET SDK

L'esempio seguente mostra come elencare le definizioni delle metriche a livello di account:

```csharp
    public static async Task ListStorageMetricDefinition()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        // Using metrics in Azure Monitor is currently free. However, if you use additional solutions ingesting metrics data, you may be billed by these solutions. For example, you are billed by Azure Storage if you archive metrics data to an Azure Storage account. Or you are billed by Operation Management Suite (OMS) if you stream metrics data to OMS for advanced analysis.
        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;
        IEnumerable<MetricDefinition> metricDefinitions = await readOnlyClient.MetricDefinitions.ListAsync(resourceUri: resourceId, cancellationToken: new CancellationToken());

        foreach (var metricDefinition in metricDefinitions)
        {
            //Enumrate metric definition:
            //    Id
            //    ResourceId
            //    Name
            //    Unit
            //    MetricAvailabilities
            //    PrimaryAggregationType
            //    Dimensions
            //    IsDimensionRequired
        }
    }

```

Per elencare le definizioni delle metriche per BLOB, tabelle, file o code, è necessario specificare ID risorsa diversi per ogni servizio con l'API.

#### <a name="read-metric-values-with-the-net-sdk"></a>Leggere i valori delle metriche con .NET SDK

L'esempio seguente mostra come leggere i dati relativi a `UsedCapacity` a livello di account:

```csharp
    public static async Task ReadStorageMetricValue()
    {
        // Resource ID for storage account
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;

        Response = await readOnlyClient.Metrics.ListAsync(
            resourceUri: resourceId,
            timespan: timeSpan,
            interval: System.TimeSpan.FromHours(1),
            metricnames: "UsedCapacity",

            aggregation: "Average",
            resultType: ResultType.Data,
            cancellationToken: CancellationToken.None);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

Se si vogliono leggere i valori delle metriche nell'esempio precedente per BLOB, tabelle, file o code, è necessario specificare ID risorsa diversi per ogni servizio con l'API.

#### <a name="read-multi-dimensional-metric-values-with-the-net-sdk"></a>Leggere i valori delle metriche multidimensionali con .NET SDK

Per le metriche multidimensionali, è necessario definire il filtro dei metadati se si vuole leggere i dati delle metriche per un valore di dimensione specifico.

L'esempio seguente mostra come leggere i dati per una metrica con il supporto di più dimensioni:

```csharp
    public static async Task ReadStorageMetricValueTest()
    {
        // Resource ID for blob storage
        var resourceId = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default";
        var subscriptionId = "{SubscriptionID}";
        // How to identify Tenant ID, Application ID and Access Key: https://azure.microsoft.com/documentation/articles/resource-group-create-service-principal-portal/
        var tenantId = "{TenantID}";
        var applicationId = "{ApplicationID}";
        var accessKey = "{AccessKey}";

        MonitorManagementClient readOnlyClient = AuthenticateWithReadOnlyClient(tenantId, applicationId, accessKey, subscriptionId).Result;

        Microsoft.Azure.Management.Monitor.Models.Response Response;

        string startDate = DateTime.Now.AddHours(-3).ToUniversalTime().ToString("o");
        string endDate = DateTime.Now.ToUniversalTime().ToString("o");
        string timeSpan = startDate + "/" + endDate;
        // It's applicable to define meta data filter when a metric support dimension
        // More conditions can be added with the 'or' and 'and' operators, example: BlobType eq 'BlockBlob' or BlobType eq 'PageBlob'
        ODataQuery<MetadataValue> odataFilterMetrics = new ODataQuery<MetadataValue>(
            string.Format("BlobType eq '{0}'", "BlockBlob"));

        Response = readOnlyClient.Metrics.List(
                        resourceUri: resourceId,
                        timespan: timeSpan,
                        interval: System.TimeSpan.FromHours(1),
                        metricnames: "BlobCapacity",
                        odataQuery: odataFilterMetrics,
                        aggregation: "Average",
                        resultType: ResultType.Data);

        foreach (var metric in Response.Value)
        {
            //Enumrate metric value
            //    Id
            //    Name
            //    Type
            //    Unit
            //    Timeseries
            //        - Data
            //        - Metadatavalues
        }
    }

```

## <a name="understanding-resource-id-for-services-in-azure-storage"></a>Informazioni sull'ID risorsa per i servizi in Archiviazione di Azure

L'ID risorsa è un identificatore univoco di una risorsa in Azure. Quando si usa l'API REST di Monitoraggio di Azure per leggere le definizioni o i valori delle metriche, è necessario usare l'ID risorsa per la risorsa su cui si vuole eseguire l'operazione. Il formato del modello dell'ID risorsa è il seguente:

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

L'archiviazione fornisce metriche a livello di account di archiviazione e a livello di servizio con Monitoraggio di Azure. È ad esempio possibile recuperare le metriche solo per l'archiviazione BLOB. Ogni livello ha il proprio ID risorsa, che viene usato per recuperare le metriche solo per tale livello.

### <a name="resource-id-for-a-storage-account"></a>ID risorsa per un account di archiviazione

Di seguito viene mostrato il formato per specificare l'ID risorsa per un account di archiviazione.

```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}
```

### <a name="resource-id-for-the-storage-services"></a>ID risorsa per i servizi di archiviazione

Di seguito viene mostrato il formato per specificare l'ID risorsa per ogni servizio di archiviazione.

* ID risorsa del servizio BLOB
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/blobServices/default
```
* ID risorsa del servizio tabelle
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/tableServices/default
```
* ID risorsa del servizio coda
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/queueServices/default
```
* ID risorsa del servizio file
```
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}/fileServices/default
```

### <a name="resource-id-in-azure-monitor-rest-api"></a>ID risorsa nell'API REST di Monitoraggio di Azure

Di seguito viene mostrato il modello usato per la chiamata API REST di Monitoraggio di Azure.

```
GET {resourceId}/providers/microsoft.insights/metrics?{parameters}
```

## <a name="capacity-metrics"></a>Metriche della capacità
I valori delle metriche relative alla capacità vengono inviati a Monitoraggio di Azure ogni ora. I valori vengono aggiornati ogni giorno. L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. L'intervallo di tempo supportato per tutte le metriche relative alla capacità è un'ora (PT1H).

Archiviazione di Azure fornisce le metriche seguenti relative alla capacità in Monitoraggio di Azure.

### <a name="account-level"></a>Livello di account

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| UsedCapacity | Quantità di risorse di archiviazione usata dall'account di archiviazione. Per gli account di archiviazione Standard, corrisponde alla somma della capacità usata da BLOB, tabelle, file e code. Per gli account di archiviazione Premium e gli account di archiviazione BLOB corrisponde a BlobCapacity. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |

### <a name="blob-storage"></a>Archiviazione BLOB

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| BlobCapacity | Totale di risorse di archiviazione BLOB usato nell'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 <br/> Dimensione: BlobType ([definizione](#metrics-dimensions)) |
| BlobCount    | Numero di oggetti BLOB archiviati nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 <br/> Dimensione: BlobType ([definizione](#metrics-dimensions)) |
| ContainerCount    | Numero di contenitori nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| IndexCapacity     | Quantità di memoria usata dall'indice gerarchico di ADLS Gen2 <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |

### <a name="table-storage"></a>Archiviazione tabelle

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| TableCapacity | Quantità di archiviazione tabelle usata dall'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| TableCount   | Numero di tabelle nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| TableEntityCount | Numero di entità tabella nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |

### <a name="queue-storage"></a>Archiviazione code

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| QueueCapacity | Quantità di archiviazione code usata dall'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| QueueCount   | Numero di code nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| QueueMessageCount | Numero di messaggi non scaduti nella coda nell'account di archiviazione. <br/><br/>Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |

### <a name="file-storage"></a>Archiviazione file

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| FileCapacity | Quantità di archiviazione file usata dall'account di archiviazione. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| FileCount   | Numero di file nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |
| FileShareCount | Numero di condivisioni file nell'account di archiviazione. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Media <br/> Valore di esempio: 1024 |

## <a name="transaction-metrics"></a>Metriche di transazione

Le metriche di transazione vengono generate su per tutte le richieste a un account di archiviazione da Archiviazione di Azure a Monitoraggio di Azure. In caso non siano presenti attività nell'account di archiviazione, non ci saranno dati per le metriche di transazione nel periodo. Tutte le metriche di transazione sono disponibili a livello di account e di servizio (archiviazione BLOB, archiviazione tabelle, File di Azure e archiviazione code). L'intervallo di tempo definisce l'intervallo di tempo per cui vengono presentati i valori delle metriche. Gli intervalli di tempo supportati per tutte le metriche di transazioni sono PT1H e PT1M.

Archiviazione di Azure fornisce le metriche seguenti relative alle transazioni in Monitoraggio di Azure.

| Nome della metrica | DESCRIZIONE |
| ------------------- | ----------------- |
| Transazioni | Numero di richieste eseguite in un servizio di archiviazione o nell'operazione API specificata. Questo numero include le richieste con esito positivo e negativo, oltre alle richieste che hanno restituito errori. <br/><br/> Unità: Conteggio <br/> Tipo di aggregazione: Totale <br/> Dimensioni applicabili: ResponseType, GeoType, ApiName e Authentication ([definizione](#metrics-dimensions))<br/> Valore di esempio: 1024 |
| Dati in ingresso | Quantità di dati in ingresso. Questo numero include i dati in ingresso da un client esterno in Archiviazione di Azure, oltre ai dati in ingresso in Azure. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Totale <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Valore di esempio: 1024 |
| Egress | Quantità di dati in uscita. Questo numero include i dati in uscita da un client esterno verso Archiviazione di Azure, oltre ai dati in uscita entro Azure. Questo numero non rispecchia quindi dati in uscita fatturabili. <br/><br/> Unità: Byte <br/> Tipo di aggregazione: Totale <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Valore di esempio: 1024 |
| SuccessServerLatency | Tempo medio necessario per l'elaborazione di una richiesta con esito positivo da parte di Archiviazione di Azure. Questo valore non include la latenza di rete specificata in SuccessE2ELatency. <br/><br/> Unità: Millisecondi <br/> Tipo di aggregazione: Media <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Valore di esempio: 1024 |
| SuccessE2ELatency | Latenza end-to-end media di richieste con esito positivo effettuate in un servizio di archiviazione o nell'operazione API specificata. Questo valore include il tempo di elaborazione necessario in Archiviazione di Azure per leggere la richiesta, inviare la risposta e ricevere il riconoscimento della risposta. <br/><br/> Unità: Millisecondi <br/> Tipo di aggregazione: Media <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Valore di esempio: 1024 |
| Disponibilità | Percentuale della disponibilità per il servizio di archiviazione o per l'operazione API specificata. La disponibilità viene calcolata prendendo il valore totale delle richieste fatturabili e dividendolo per il numero di richieste applicabili, incluse le richieste che hanno restituito errori imprevisti. Tutti gli errori imprevisti provocano la riduzione della disponibilità per il servizio di archiviazione o per l'operazione API specificata. <br/><br/> Unità: Percentuale <br/> Tipo di aggregazione: Media <br/> Dimensioni applicabili: GeoType, ApiName e Authentication ([definizione](#metrics-dimensions)) <br/> Valore di esempio: 99,99 |

## <a name="metrics-dimensions"></a>Dimensioni delle metriche

Archiviazione di Azure supporta le dimensioni seguenti per le metriche in Monitoraggio di Azure.

| Nome della dimensione | DESCRIZIONE |
| ------------------- | ----------------- |
| BlobType | Tipo di BLOB solo per le metriche relative ai BLOB. I valori supportati sono **BlockBlob** e **PageBlob**. Il BLOB di aggiunta è incluso in BlockBlob. |
| ResponseType | Tipo di risposta della transazione. I valori disponibili includono: <br/><br/> <li>ServerOtherError: tutti gli altri errori lato server ad eccezione di quelli descritti. </li> <li> ServerBusyError: richiesta autenticata che ha restituito un codice di stato HTTP 503. </li> <li> ServerTimeoutError: richiesta autenticata con timeout che ha restituito un codice di stato HTTP 500. Il timeout si è verificato a causa di un errore del server. </li> <li> AuthorizationError: richiesta autenticata con esito negativo a causa dell'accesso non autorizzato ai dati o di un errore di autorizzazione. </li> <li> NetworkError: richiesta autenticata con esito negativo a causa di errori di rete. Questo errore si verifica in genere quando un cliente chiude prematuramente una connessione prima della scadenza del timeout. </li> <li>    ClientThrottlingError: errore di limitazione sul lato client. </li> <li> ClientTimeoutError: richiesta autenticata con timeout che ha restituito un codice di stato HTTP 500. Se il timeout di rete o il timeout della richiesta del client è impostato su un valore inferiore rispetto a quanto previsto dal servizio di archiviazione, si tratta di un timeout previsto. In caso contrario, viene segnalato come ServerTimeoutError. </li> <li> ClientOtherError: tutti gli altri errori sul lato client ad eccezione di quelli descritti. </li> <li> Success: Richiesta completata correttamente. </li> <li> SuccessWithThrottling: Richiesta completata correttamente quando un client SMB viene limitato nel primo tentativo, ma ha esito positivo dopo i tentativi.|
| GeoType | Transazione da un cluster primario o secondario. I valori disponibili includono Primary e Secondary. È applicabile all'archiviazione con ridondanza geografica e accesso in lettura durante la lettura di oggetti da un tenant secondario. |
| ApiName | Nome dell'operazione. Ad esempio:  <br/> <li>CreateContainer</li> <li>DeleteBlob</li> <li>GetBlob</li> Per i nomi di tutte le operazioni, vedere questo [documento](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| Authentication | Tipo Authentication usato nelle transazioni. I valori disponibili includono: <br/> <li>AccountKey: la transazione viene autenticata con la chiave dell'account di archiviazione.</li> <li>SAS: la transazione viene autenticata con firme di accesso condiviso.</li> <li>OAuth: la transazione viene autenticata con i token di accesso OAuth.</li> <li>Anonymous: la transazione viene richiesta in modo anonimo. Non include le richieste preliminari.</li> <li>AnonymousPreflight: la transazione è una richiesta preliminare.</li> |

Per le dimensioni che supportano metriche, è necessario specificare il valore relativo alla dimensione per visualizzare i valori delle metriche corrispondenti. Se, ad esempio, si esamina il valore **Transactions** per le risposte con esito positivo, è necessario filtrare la dimensione **ResponseType** con **Success**. In alternativa, se si esamina il valore **BlobCount** per il BLOB in blocchi, è necessario filtrare la dimensione **BlobType** con **BlockBlob**.

## <a name="service-continuity-of-legacy-metrics"></a>Continuità del servizio delle metriche legacy

Le metriche legacy sono disponibili in parallelo rispetto alle metriche gestite da Monitoraggio di Azure. Il supporto rimane invariato fino al termine del servizio relativo alle metriche legacy da parte di Archiviazione di Azure.

## <a name="faq"></a>Domande frequenti

**La nuova metrica supporta account di archiviazione classico?**

No, le nuove metriche in Monitoraggio di Azure supportano solo gli account di archiviazione di Azure Resource Manager. Se si vogliono usare le metriche per gli account di archiviazione, è necessario eseguire la migrazione all'account di archiviazione di Azure Resource Manager. Vedere [Migrate to Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-overview) (Eseguire la migrazione ad Azure Resource Manager).

**Archiviazione di Azure supporta le metriche per Managed Disks o dischi non gestiti?**

No, le metriche relative ai dischi sono supportate da Calcolo di Azure. Per informazioni più dettagliate, vedere [questo articolo](https://azure.microsoft.com/blog/per-disk-metrics-managed-disks/).

**Come eseguire il mapping ed eseguire la migrazione delle metriche classiche con la nuova metrica?**

Un mapping dettagliato tra le metriche classiche e le nuove metriche è disponibile in [Migrazione delle metriche di Archiviazione di Azure](./storage-metrics-migration.md).

## <a name="next-steps"></a>Passaggi successivi

* [Monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-overview.md)
