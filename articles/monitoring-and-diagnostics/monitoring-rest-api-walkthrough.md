---
title: Procedura dettagliata sull'API REST di monitoraggio di Azure | Microsoft Docs
description: Come autenticare le richieste e usare l'API REST di monitoraggio di Azure per recuperare le definizioni e i valori delle metriche disponibili.
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.openlocfilehash: 357a63c65a4f6864dca259aad8a76f83681cd501
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2017
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Procedura dettagliata sull'API REST di monitoraggio di Azure
In questo articolo viene illustrato come eseguire l'autenticazione in modo che il codice possa usare le [informazioni di riferimento sulle API REST di monitoraggio di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

L'API di monitoraggio di Azure consente di recuperare in modo programmatico le definizioni delle metriche predefinite disponibili, la granularità e i valori delle metriche. I dati possono essere salvati in un archivio dati separato come il database SQL di Azure, Azure Cosmos DB o Azure Data Lake. Da qui, se necessario, è possibile svolgere ulteriori analisi.

Oltre a essere compatibile con vari punti dati delle metriche, l'API di monitoraggio consente di elencare le regole di avviso, di visualizzare i registri delle attività e molto altro ancora. Per un elenco completo delle operazioni disponibili, vedere le [informazioni di riferimento sulle API REST di monitoraggio di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autenticazione delle richieste di monitoraggio di Azure
Innanzitutto è necessario autenticare la richiesta.

Tutte le attività eseguite sull'API di monitoraggio di Azure usano il modello di autenticazione Azure Resource Manager. Di conseguenza, tutte le richieste devono essere autenticate con Azure Active Directory (Azure AD). Un approccio per autenticare l'applicazione client consiste nel creare un'entità servizio di Azure AD e recuperare il token di autenticazione (JTW). Il seguente script di esempio mostra la creazione di un'entità servizio di Azure AD tramite PowerShell. Per una procedura più dettagliata, fare riferimento alla documentazione sull' [uso di Azure PowerShell per creare un'entità servizio per accedere alle risorse](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). È possibile anche [creare un'entità di sicurezza tramite il portale di Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Per eseguire query nell'API di monitoraggio di Azure, l'applicazione client deve effettuare l'autenticazione usando l'entità servizio creata in precedenza. Nell'esempio seguente di script di PowerShell viene illustrato un approccio in cui viene usato [Active Directory Authentication Library](../active-directory/active-directory-authentication-libraries.md) (ADAL) per ottenere il token di autenticazione JTW. Il token JTW viene trasmesso come parte di un parametro di autorizzazione HTTP nelle richieste all'API di Monitoraggio di Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)

$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Dopo l'autenticazione, è possibile eseguire le query con l'API REST di monitoraggio di Azure. Esistono due query utili:

1. Elencare le definizioni delle metriche per una risorsa
2. Recuperare i valori delle metriche

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperare le definizioni delle metriche (API multidimensionale)

Usare l'[API REST relativa alle definizione delle metriche del Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) per accedere all'elenco di metriche disponibili per un servizio.

**Metodo**: GET

**URI richiesta**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Ad esempio, per recuperare le definizioni delle metriche per un account di archiviazione di Azure, la richiesta avrebbe il seguente aspetto:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Per recuperare le definizioni delle metriche mediante l'API REST delle metriche di monitoraggio di Azure, usare "2017-05-01-anteprima" come versione dell'API.
>
>

Il corpo della risposta JSON risultante dovrebbe essere simile all'esempio seguente: (si noti che la seconda metrica dispone di dimensioni)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
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
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Recuperare i valori delle dimensioni (API multidimensionale)
Una volta stabilite le definizioni delle metriche disponibili, potrebbero essere presenti alcune metriche con dimensioni. Prima di eseguire una query relativa alla metrica, si potrebbe voler individuare l'intervallo di valori di una dimensione. In base a questi valori della dimensione è quindi possibile scegliere di filtrare o di segmentare le metriche in base ai valori della dimensione durante l'esecuzione della query relativa alle metriche. Usare il 'valore' del nome della metrica (non ' localizedValue') per tutte le richieste di filtro (ad esempio, per recuperare i punti dati delle metriche 'CpuTime' e 'Requests'). Se non è specificato alcun filtro, viene restituita la metrica predefinita.

> [!NOTE]
> Per recuperare i valori della dimensione mediante l'API REST di monitoraggio di Azure, usare "2017-05-01-anteprima" come versione dell'API.
>
>

**Metodo**: GET

**URI richiesta**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&resultType=metadata&api-version=*{apiVersion}*

Ad esempio, per recuperare l'elenco dei valori potenziali della "dimensione nome API" per la metrica "Transazioni" in un intervallo di tempo specificato, la richiesta sarebbe:

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
Il corpo della risposta JSON risultante dovrebbe essere simile all'esempio seguente:

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Recuperare i valori delle metriche (API multidimensionale)
Una volta che le definizioni delle metriche disponibili e i possibili valori della dimensione sono noti, è possibile recuperare i relativi valori delle metriche. Usare il nome "value" della metrica (non "localizedValue") per tutte le richieste di filtro. Se non viene specificato alcun filtro della dimensione, viene restituita la metrica di rollup aggregata.

> [!NOTE]
> Per recuperare i valori delle metriche multidimensionali mediante l'API REST di monitoraggio di Azure, usare "2017-05-01-anteprima" come versione dell'API.
>
>

**Metodo**: GET

**URI richiesta**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&timespan=*{starttime/endtime}*&$filter=*{filter}*&interval=*{timeGrain}*&aggregation=*{aggreation}*&api-version=*{apiVersion}*

Ad esempio, per recuperare i valori della metrica di archiviazione "Transazioni" durante un intervallo di 5 minuti, per tutte le transazioni per il nome dell'API "GetBlobProperties", la richiesta sarebbe la seguente:

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
Il corpo della risposta JSON risultante dovrebbe essere simile all'esempio seguente:

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>Recuperare le definizioni delle metriche
Usare l'[API REST relativa alle definizione delle metriche del Monitoraggio di Azure](https://msdn.microsoft.com/library/mt743621.aspx) per accedere all'elenco di metriche disponibili per un servizio.

**Metodo**: GET

**URI richiesta**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Ad esempio, per recuperare le definizioni delle metriche per un'app per la logica di Azure, la richiesta avrebbe il seguente aspetto:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Per recuperare le definizioni delle metriche mediante l'API REST di monitoraggio di Azure, usare "2016-03-01" come versione dell'API.
>
>

Il corpo della risposta JSON risultante dovrebbe essere simile all'esempio seguente:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Per ulteriori informazioni, vedere la documentazione [Elencare le definizioni delle metriche per una risorsa nell'API REST di monitoraggio di Azure](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperare i valori delle metriche
Una volta che le definizioni delle metriche disponibili sono note, è possibile recuperare i relativi valori. Usare il 'valore' del nome della metrica (non ' localizedValue') per tutte le richieste di filtro (ad esempio, per recuperare i punti dati delle metriche 'CpuTime' e 'Requests'). Se non è specificato alcun filtro, viene restituita la metrica predefinita.

> [!NOTE]
> Per recuperare i valori delle metriche mediante l'API REST di monitoraggio di Azure, usare "2016-09-01" come versione dell'API.
>
>

**Metodo**: GET

**URI richiesta**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&api-version=*{apiVersion}*

Ad esempio, per recuperare i dati della metrica RunsSucceeded per la finestra temporale specificata e per un intervallo di tempo di 1 ora, la richiesta sarà come mostrato di seguito:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

Il corpo della risposta JSON risultante dovrebbe essere simile all'esempio seguente:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Per recuperare più punti dati o di aggregazione, aggiungere i nomi delle definizioni delle metriche e i tipi di aggregazione al filtro, come illustrato nell'esempio seguente:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
Il corpo della risposta JSON risultante dovrebbe essere simile all'esempio seguente:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Usare ARMClient
Un approccio aggiuntivo consiste nell'usare [ARMClient](https://github.com/projectkudu/armclient) sul proprio computer Windows. ARMClient gestisce automaticamente l'autenticazione di Azure AD (e il token JTW risultante). Di seguito viene illustrato l'uso di ARMClient per recuperare i dati della metrica:

1. Installare [Chocolatey](https://chocolatey.org/) e [ARMClient](https://github.com/projectkudu/armclient).
2. In una finestra del terminale digitare *armclient.exe login*. Viene richiesto di accedere ad Azure.
3. Digitare *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Digitare *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Ad esempio, per poter recuperare le definizioni della metrica per un'app per la logica specifica, eseguire il comando seguente:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Recuperare l'ID risorsa
L'uso dell'API REST può aiutare davvero a comprendere le definizioni delle metriche disponibili, la granularità e i valori correlati. Tali informazioni sono utili quando si usa [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Per il codice precedente, l'ID risorsa da usare è il percorso completo verso la risorsa Azure desiderata. Ad esempio, per eseguire una query su un'App Web di Azure, l'ID risorsa potrebbe essere:

*/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Web/sites/{site-name}/*

Il seguente elenco contiene alcuni esempi di formati di ID risorsa per le varie risorse di Azure:

* **Hub IoT** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Pool elastico di SQL** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **Database SQL (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Bus di servizio** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Set di scalabilità della macchina virtuale** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Hub eventi** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Esistono approcci alternativi per recuperare l'ID risorsa, tra cui l'uso di Esplora risorse di Azure, la visualizzazione della risorsa desiderata nel portale di Azure e tramite PowerShell o l'interfaccia della riga di comando di Azure.

### <a name="azure-resource-explorer"></a>Esplora risorse di Azure
Per trovare l'ID risorsa per la risorsa desiderata, un approccio utile consiste nell'usare lo strumento [Esplora risorse di Azure](https://resources.azure.com) . Individuare la risorsa desiderata e quindi esaminare l'ID indicato, come illustrato nella schermata seguente:

![Alt "Esplora risorse di Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portale di Azure
L'ID risorsa può anche essere ottenuto dal portale di Azure. A tale scopo, accedere alla risorsa desiderata e scegliere Proprietà. L'ID risorsa viene visualizzato nella sezione Proprietà, come illustrato nella schermata seguente:

![Alt "ID risorsa visualizzato nel pannello Proprietà del portale di Azure"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
L'ID risorsa può essere recuperato anche tramite i cmdlet di Azure PowerShell. Ad esempio, per ottenere l'ID risorsa per un'app per la logica di Azure, eseguire Get-AzureLogicApp cmdlet, come illustrato nell'esempio seguente:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

Il risultato dovrebbe essere simile all'esempio seguente:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Per recuperare l'ID di risorsa per un account di archiviazione di Azure usando l'interfaccia della riga di comando di Azure, eseguire il comando "az storage account show", come illustrato nell'esempio seguente:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

Il risultato dovrebbe essere simile all'esempio seguente:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Le app per la logica di Azure non sono ancora disponibili tramite l'interfaccia della riga di comando di Azure, pertanto un account di archiviazione di Azure è illustrato nell'esempio precedente.
>
>

## <a name="retrieve-activity-log-data"></a>Recuperare i dati del registro attività
Oltre alle definizioni delle metriche e ai valori correlati, è anche possibile usare l'API REST di monitoraggio di Azure per recuperare interessanti informazioni aggiuntive relative alle risorse di Azure. Ad esempio, è possibile eseguire query sui dati del [registro attività](https://msdn.microsoft.com/library/azure/dn931934.aspx) . Nell'esempio seguente viene illustrato l'uso dell'API REST di monitoraggio di Azure per eseguire query sui dati del registro attività all'interno di un intervallo di date specifico per una sottoscrizione di Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Passaggi successivi
* Esaminare la [panoramica sul monitoraggio](monitoring-overview.md).
* Visualizzare le [metriche supportate con il monitoraggio di Azure](monitoring-supported-metrics.md).
* Esaminare le [informazioni di riferimento sulle API REST di monitoraggio di Microsoft Azure](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Esaminare [Azure Management Library](https://msdn.microsoft.com/library/azure/mt417623.aspx).
