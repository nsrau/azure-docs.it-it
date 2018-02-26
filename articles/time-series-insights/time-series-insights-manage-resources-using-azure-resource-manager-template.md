---
title: Gestione dell'ambiente di Azure Time Series Insights tramite i modelli di Azure Resource Manager | Microsoft Docs
description: In questo articolo viene descritto come gestire l'ambiente di Azure Time Series Insights a livello di programmazione tramite Azure Resource Manager.
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 12/08/2017
ms.openlocfilehash: b09d4a1aea56a4e306f80a1b43d519d313fd73ab
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Creare risorse di Time Series Insights tramite i modelli di Azure Resource Manager

Questo articolo descrive come creare e distribuire risorse di Time Series Insights usando i modelli di Azure Resource Manager, PowerShell e il provider di risorse di Time Series Insights.

Time Series Insights supporta le risorse seguenti:
   | Risorsa | DESCRIZIONE |
   | --- | --- |
   | Environment | Un ambiente di Time Series Insights è un raggruppamento logico di eventi che vengono letti dai gestori di eventi, archiviati e resi disponibili per la query. Per altre informazioni, vedere [Pianificare un ambiente Azure Time Series Insights](time-series-insights-environment-planning.md) |
   | Origine evento | Un'origine evento è una connessione a un gestore eventi da cui Time Series Insights legge e inserisce gli eventi nell'ambiente. Le origini eventi attualmente supportate sono hub IoT e hub eventi. |
   | Set di dati di riferimento | I set di dati di riferimento forniscono metadati sugli eventi nell'ambiente. I metadati nei set di dati di riferimento verranno aggiunto agli eventi durante l'ingresso. I set di dati di riferimento sono definiti come risorse dalle relative proprietà chiave evento. I metadati effettivi che costituiscono il set di dati di riferimento vengono caricati o modificati tramite le API del piano dati. |
   | Criterio di accesso | I criteri di accesso concedono le autorizzazioni per eseguire query sui dati, modificare i dati di riferimento nell'ambiente e condividere le prospettive e le query salvate associate all'ambiente. Per altre informazioni, vedere [Concedere l'accesso ai dati in un ambiente Time Series Insights con il portale di Azure](time-series-insights-data-access.md) |

Un modello di Resource Manager è un file JSON che definisce l'infrastruttura e la configurazione delle risorse in un gruppo di risorse. Per altre informazioni, vedere i documenti seguenti:

- [Panoramica di Azure Resource Manager - Distribuzione modelli](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

Il modello di avvio rapido [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) viene pubblicato su GitHub. Questo modello crea un ambiente Time Series Insights, un origine evento figlio configurato per usare gli eventi da un hub eventi e i criteri di accesso che concedono l'accesso ai dati dell'ambiente. Se non è specificato un hub eventi esistente, ne verrà creato uno con la distribuzione.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuire il modello di avvio rapido in locale tramite PowerShell

La procedura seguente descrive come usare PowerShell per distribuire un modello di Azure Resource Manager che crea un ambiente Time Series Insights, un origine evento figlio configurato per usare gli eventi da un hub eventi e i criteri di accesso che concedono l'accesso ai dati dell'ambiente. Se non è specificato un hub eventi esistente, ne verrà creato uno con la distribuzione.

Il flusso di lavoro è all'incirca il seguente:

1. Installare PowerShell.
1. Creare il modello e un file di parametri.
1. In PowerShell accedere all'account Azure.
1. Se non esiste, creare un nuovo gruppo di risorse.
1. Testare la distribuzione.
1. Distribuire il modello.

### <a name="install-powershell"></a>Installare PowerShell

Installare Azure PowerShell seguendo le istruzioni riportate in [Introduzione ai cmdlet di Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Creare un modello

Clone o copiare il modello [201-timeseriesinsights-environment-with-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) da GitHub.

### <a name="create-a-parameters-file"></a>Creare un file di parametri

Per creare un file di parametri, copiare il file [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "eventHubNamespaceName": {
          "value": "GEN-UNIQUE"
      },
      "eventHubName": {
          "value": "GEN-UNIQUE"
      },
      "consumerGroupName": {
          "value": "GEN-UNIQUE"
      },
      "environmentName": {
        "value": "GEN-UNIQUE"
      },
      "eventSourceName": {
        "value": "GEN-UNIQUE"
      }
  }
}
```

#### <a name="required-parameters"></a>Parametri obbligatori

   | Parametro | DESCRIZIONE |
   | --- | --- |
   | eventHubNamespaceName | Lo spazio dei nomi dello hub eventi di origine. |
   | eventHubName | Il nome dello hub eventi di origine. |
   | consumerGroupName | Il nome del gruppo di consumer che verrà usato dal servizio Time Series Insights per leggere i dati dall'hub eventi. **NOTA:** per evitare conflitti di risorse, questo gruppo di consumer deve essere dedicato al servizio Time Series Insights e non condiviso con altri lettori. |
   | environmentName | Il nome dell'ambiente. Il nome non può includere i caratteri seguenti:    '<', '>', '%', '&', ':', '\\', '?', '/' né caratteri di controllo. Sono consentiti tutti gli altri caratteri.|
   | eventSourceName | Specificare il nome della risorsa figlio dell'origine evento. Il nome non può includere i caratteri seguenti:    '<', '>', '%', '&', ':', '\\', '?', '/' né caratteri di controllo. Sono consentiti tutti gli altri caratteri. |

#### <a name="optional-parameters"></a>Parametri facoltativi

   | Parametro | DESCRIZIONE |
   | --- | --- |
   | existingEventHubResourceId | Un ID di risorsa facoltativo di un hub eventi esistente che verrà connesso all'ambiente di Time Series Insights tramite l'origine evento. **NOTA:** l'utente che distribuisce il modello deve disporre delle autorizzazioni necessarie per eseguire l'operazione listkey nell'hub eventi di origine. Se non viene superato alcun valore, verrà creato un nuovo hub eventi dal modello. |
   | environmentDisplayName | Un nome descrittivo facoltativo da visualizzare nelle interfacce degli strumenti o utente al posto del nome dell'ambiente. |
   | environmentSkuName | Il nome dello SKU. Per altre informazioni, vedere la [pagina dedicata al tariffario di Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | La capacità unità dello Sku. Per altre informazioni, vedere la [pagina dedicata al tariffario di Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | L'intervallo di tempo minimo in cui gli eventi dell'ambiente saranno disponibili per la query. Il valore deve essere specificato nel formato ISO 8601, ad esempio "P30D" per i criteri di conservazione di 30 giorni. |
   | eventSourceDisplayName | Un nome descrittivo facoltativo da visualizzare nelle interfacce degli strumenti o utente al posto del nome dell'origine evento. |
   | eventSourceTimestampPropertyName | La proprietà di evento che verrà usata come timestamp dell'origine evento. Se non è specificato un valore per timestampPropertyName o se viene specificato null o una stringa vuota, verrà usata l'ora di creazione dell'evento. |
   | eventSourceKeyName | Il nome della chiave di accesso condivisa che verrà usata dal servizio Time Series Insights per connettersi all'hub eventi. |
   | accessPolicyReaderObjectIds | Un elenco di ID oggetto degli utenti o delle applicazioni in Azure AD che devono disporre dell'accesso in lettura all'ambiente. L'objectId dell'entità servizio può essere ottenuto chiamando i cmdlet **Get-AzureRMADUser** o **Get-AzureRMADServicePrincipal**. La creazione di criteri di accesso per i gruppi di Azure AD non è ancora supportata. |
   | accessPolicyContributorObjectIds | Un elenco di ID oggetto degli utenti o delle applicazioni in Azure AD che devono disporre dell'accesso per collaboratori all'ambiente. L'objectId dell'entità servizio può essere ottenuto chiamando i cmdlet **Get-AzureRMADUser** o **Get-AzureRMADServicePrincipal**. La creazione di criteri di accesso per i gruppi di Azure AD non è ancora supportata. |

Ad esempio, per creare un ambiente verrebbe usato il file dei parametri seguente e un'origine evento che legge gli eventi da un hub eventi esistente. Crea inoltre due criteri di accesso che concedono l'accesso per i collaboratori all'ambiente.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "eventHubNamespaceName": {
            "value": "tsiTemplateTestNamespace"
        },
        "eventHubName": {
            "value": "tsiTemplateTestEventHub"
        },
        "consumerGroupName": {
            "value": "tsiTemplateTestConsumerGroup"
        },
        "environmentName": {
          "value": "tsiTemplateTestEnvironment"
        },
        "eventSourceName": {
          "value": "tsiTemplateTestEventSource"
        },
        "existingEventHubResourceId": {
          "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
        },
        "accessPolicyContributorObjectIds": {
            "value": [
                "AGUID001-0000-0000-0000-000000000000",
                "AGUID002-0000-0000-0000-000000000000"
            ]
        }
    }
  }
```

Per altre informazioni, vedere l'articolo [Parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Accedere ad Azure e impostare la sottoscrizione di Azure

Al prompt di PowerShell, eseguire il comando seguente:

```powershell
Login-AzureRmAccount
```

Il sistema chiede di accedere all'account Azure. Dopo l'accesso, eseguire il comando seguente per visualizzare le sottoscrizioni disponibili:

```powershell
Get-AzureRMSubscription
```

Questo comando restituisce un elenco delle sottoscrizioni di Azure disponibili. Scegliere una sottoscrizione per la sessione corrente eseguendo il comando seguente. Sostituire `<YourSubscriptionId>` con il GUID della sottoscrizione di Azure che si vuole usare:

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Impostare il gruppo di risorse

Se non è disponibile un gruppo di risorse esistente, creare un nuovo gruppo di risorse con il comando **New-AzureRmResourceGroup**. Specificare il nome del gruppo di risorse e la posizione che si vuole usare, Ad esempio: 

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se il nuovo gruppo di risorse è stato creato correttamente, viene visualizzato il relativo riepilogo.

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Test della distribuzione

Convalidare la distribuzione eseguendo il cmdlet `Test-AzureRmResourceGroupDeployment`. Durante il test della distribuzione, specificare esattamente gli stessi parametri di quando si esegue la distribuzione.

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

### <a name="create-the-deployment"></a>Creare la distribuzione

Per creare la nuova distribuzione, eseguire il cmdlet `New-AzureRmResourceGroupDeployment` e specificare i parametri necessari quando viene richiesto. I parametri includono il nome della distribuzione, il nome del gruppo di risorse e il percorso o l'URL del file di modello. Se il parametro **Mode** non è specificato, viene usato il valore predefinito **Incremental**. Per altre informazioni, vedere [Distribuzioni incrementali e complete](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments).

Il comando seguente richiede cinque parametri obbligatori nella finestra di PowerShell:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

Per specificare invece un file di parametri, usare il comando seguente:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

È anche possibile usare i parametri inline quando si esegue il cmdlet di distribuzione. Il comando è il seguente:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Per eseguire una distribuzione [completa](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments), impostare il parametro **Mode** su **Complete**:

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

## <a name="verify-the-deployment"></a>Verificare la distribuzione

Se le risorse vengono distribuite correttamente, nella finestra di PowerShell viene visualizzato il riepilogo della distribuzione:

```powershell
DeploymentName          : azuredeploy
ResourceGroupName       : MyDemoRG
ProvisioningState       : Succeeded
Timestamp               : 12/9/2017 01:06:54
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          eventHubNamespaceName  String               <eventHubNamespaceName>
                          eventHubName     String                     <eventHubName>
                          consumerGroupName  String                   <consumerGroupName>
                          existingEventHubResourceId  String
                          environmentName  String                     <environmentName>
                          environmentDisplayName  String
                          environmentSkuName  String                  S1
                          environmentSkuCapacity  Int                 1
                          environmentDataRetentionTime  String        P30D
                          eventSourceName  String                     <eventSourceName>
                          eventSourceDisplayName  String
                          eventSourceTimestampPropertyName  String
                          eventSourceKeyName  String                  manage
                          accessPolicyReaderObjectIds  Array          []
                          accessPolicyContributorObjectIds  Array     [
                            "AGUID001-0000-0000-0000-000000000000",
                            "AGUID002-0000-0000-0000-000000000000"
                          ]

Outputs                 :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          dataAccessFQDN   String
                          <guid>.env.timeseries.azure.com
```

## <a name="deploy-the-quickstart-template-through-the-azure-portal"></a>Distribuire il modello di avvio rapido tramite il portale di Azure

La home page del modello di avvio rapido GitHub include inoltre il **Distribuisci in Azure**. Facendo clic su di esso si apre la pagina Distribuzione personalizzata nel portale di Azure. In questa pagina, è possibile immettere o selezionare i valori per ognuno dei parametri dalle tabelle dei [parametri obbligatori](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) o dei [parametri facoltativi](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters). Dopo aver immesso le impostazioni, facendo clic sul pulsante **Acquisto** si avvierà la distribuzione del modello.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla gestione delle risorse di Time Series Insights tramite le API REST a livello di programmazione, vedere [Gestione di Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
