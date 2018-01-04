---
title: Come gestire l'ambiente di Azure Insights serie di tempo utilizzando i modelli di gestione risorse di Azure | Documenti Microsoft
description: In questo articolo viene descritto come gestire l'ambiente di Azure Insights serie di tempo livello di programmazione tramite Gestione risorse di Azure.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Creare risorse Insights serie ora utilizzando i modelli di gestione risorse di Azure

In questo articolo viene descritto come creare e distribuire le risorse Insights serie ora utilizzando i modelli di gestione risorse di Azure, PowerShell e il provider di risorse Insights serie di tempo.

Approfondimenti serie ora supporta le seguenti risorse:
   | Risorsa | DESCRIZIONE |
   | --- | --- |
   | Environment | Un ambiente di tempo serie Insights è un raggruppamento logico di eventi che sono leggervi gestori di eventi, archiviati e reso disponibile per la query. Per ulteriori informazioni vedere [pianificazione dell'ambiente di Azure Insights serie di tempo](time-series-insights-environment-planning.md) |
   | Origine evento | Un'origine eventi è una connessione a un gestore eventi da cui ora serie Insights legge e inserisce gli eventi nell'ambiente. Le origini eventi attualmente supportati sono IoT Hub e Hub eventi. |
   | Set di dati di riferimento | Set di dati di riferimento di fornire metadati sugli eventi nell'ambiente. Metadati nei set di dati di riferimento verranno aggiunto agli eventi durante in ingresso. Set di dati di riferimento sono definiti come risorse delle proprietà chiave evento. I metadati effettivi che costituiscono il set di dati di riferimento viene caricato o modificato tramite il piano dati API. |
   | Criteri di accesso | I criteri di accesso concedono le autorizzazioni per eseguire query di dati, modificare i dati di riferimento nell'ambiente e condividere query salvate e alle prospettive associate all'ambiente. Per ulteriori informazioni vedere [concedere l'accesso ai dati in un ambiente di Insights serie ora tramite il portale di Azure](time-series-insights-data-access.md) |

Un modello di gestione risorse è un file JSON che definisce l'infrastruttura e la configurazione delle risorse in un gruppo di risorse. Per ulteriori informazioni, vedere i seguenti documenti:

- [Panoramica di gestione risorse Azure - distribuzione modello](../azure-resource-manager/resource-group-overview.md#template-deployment)
- [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)

Il [201-timeseriesinsights-ambiente-con-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) delle Guide rapide modello viene pubblicato su GitHub. Questo modello crea un ambiente di Insights di serie di tempo, un'origine evento figlio configurato per utilizzare gli eventi da un Hub eventi e criteri che concedono l'accesso ai dati dell'ambiente di accesso. Se non è specificato un Hub di eventi esistente, con la distribuzione verrà creato uno.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Distribuire il modello di avvio rapido in locale tramite PowerShell

La procedura seguente viene descritto come utilizzare PowerShell per distribuire un modello di gestione risorse di Azure che crea un ambiente di Insights di serie di tempo, un'origine evento figlio configurato per utilizzare gli eventi da un Hub eventi e i criteri che concedono l'accesso di accesso di dati dell'ambiente. Se non è specificato un Hub di eventi esistente, con la distribuzione verrà creato uno.

Il flusso di lavoro è all'incirca il seguente:

1. Installare PowerShell.
1. Creare il modello e un file di parametro.
1. In PowerShell accedere all'account Azure.
1. Se non esiste, creare un nuovo gruppo di risorse.
1. Testare la distribuzione.
1. Distribuire il modello.

### <a name="install-powershell"></a>Installare PowerShell

Installare Azure PowerShell seguendo le istruzioni riportate in [Introduzione ai cmdlet di Azure PowerShell](/powershell/azure/get-started-azureps).

### <a name="create-a-template"></a>Creare un modello

Clone o copia di [201-timeseriesinsights-ambiente-con-eventhub](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) modello da GitHub.

### <a name="create-a-parameters-file"></a>Creare un file di parametri

Per creare un file dei parametri, copiare il [201-timeseriesinsights-ambiente-con-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) file.

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
   | eventHubNamespaceName | Lo spazio dei nomi dell'hub di eventi di origine. |
   | eventHubName | Il nome dell'hub di eventi di origine. |
   | consumerGroupName | Il nome del gruppo di consumer che verrà utilizzato dal servizio di Insights di serie di tempo per leggere i dati dall'hub di eventi. **Nota:** per evitare conflitti di risorse, questo gruppo di consumer deve essere dedicato per il servizio ora serie Insights e non condiviso con altri utenti. |
   | EnvironmentName | Il nome dell'ambiente. Non può includere il nome: ' <', ' >', '%', '&', ': ','\\','?', '/' e i caratteri di controllo. Tutti gli altri caratteri sono consentiti.|
   | eventSourceName | Il nome della risorsa figlio origine evento. Non può includere il nome: ' <', ' >', '%', '&', ': ','\\','?', '/' e i caratteri di controllo. Tutti gli altri caratteri sono consentiti. |

#### <a name="optional-parameters"></a>Parametri facoltativi

   | Parametro | DESCRIZIONE |
   | --- | --- |
   | existingEventHubResourceId | Un ID di risorsa facoltativo di un Hub di eventi esistente che verrà connesso all'ambiente di tempo serie Insights tramite l'origine evento. **Nota:** l'utente a cui il modello di distribuzione deve disporre dei privilegi necessari per eseguire l'operazione di elenco chiavi nell'Hub di eventi. Se viene passato alcun valore, un nuovo hub eventi verrà creato dal modello. |
   | environmentDisplayName | Un nome descrittivo facoltativo da visualizzare nelle interfacce utente o strumenti anziché il nome dell'ambiente. |
   | environmentSkuName | Il nome della sku. Per ulteriori informazioni, vedere il [dettagli prezzi di tempo serie Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
   | environmentSkuCapacity | La capacità di unità della Sku. Per ulteriori informazioni, vedere il [dettagli prezzi di tempo serie Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
   | environmentDataRetentionTime | L'intervallo di tempo minimo eventi dell'ambiente sarà disponibili per query. Il valore deve essere specificato nel formato ISO 8601, ad esempio "P30D" per un criterio di conservazione di 30 giorni. |
   | eventSourceDisplayName | Un nome descrittivo facoltativo da visualizzare nelle interfacce utente o strumenti anziché il nome dell'origine evento. |
   | eventSourceTimestampPropertyName | La proprietà di evento che verrà utilizzata come timestamp dell'origine evento. Se non è specificato un valore per timestampPropertyName o se viene specificato null o stringa vuota, verrà utilizzato l'ora di creazione di eventi. |
   | eventSourceKeyName | Il nome della chiave di accesso condiviso che il servizio ora serie Insights userà per connettersi all'hub eventi. |
   | accessPolicyReaderObjectIds | Un elenco di ID degli utenti o applicazioni in Azure AD che devono avere accesso in lettura per l'ambiente di oggetto. L'objectId dell'entità servizio può essere ottenuto chiamando il **Get AzureRMADUser** o **Get AzureRMADServicePrincipal** cmdlet. Creazione di un criterio di accesso per gruppi di Azure AD non è ancora supportata. |
   | accessPolicyContributorObjectIds | Un elenco di ID degli utenti o applicazioni in Azure AD che devono avere accesso collaboratore all'ambiente di oggetto. L'objectId dell'entità servizio può essere ottenuto chiamando il **Get AzureRMADUser** o **Get AzureRMADServicePrincipal** cmdlet. Creazione di un criterio di accesso per gruppi di Azure AD non è ancora supportata. |

Ad esempio, file dei parametri seguenti sarebbe possibile creare un ambiente e un'origine evento che legge gli eventi da un hub di eventi esistente. Crea inoltre due criteri di accesso che concedono l'accesso per i collaboratori all'ambiente.

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

Se non è una risorsa esistente gruppo, creare un nuovo gruppo di risorse con il **New AzureRmResourceGroup** comando. Specificare il nome del gruppo di risorse e la posizione che si vuole usare, Ad esempio: 

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

Home page di avvio rapido del modello su GitHub include inoltre un **Distribuisci in Azure** pulsante. Viene visualizzato una pagina personalizzata distribuzione nel portale di Azure. In questa pagina, è possibile immettere o selezionare i valori per ognuno dei parametri dal [parametri obbligatori](time-series-insights-manage-resources-using-azure-resource-manager-template.md#required-parameters) o [parametri facoltativi](time-series-insights-manage-resources-using-azure-resource-manager-template.md#optional-parameters) tabelle. Dopo aver immesso le impostazioni, fare clic su di **acquisto** pulsante verrà avviata la distribuzione del modello.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/>
</a>

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla gestione di risorse Insights serie ora usando le API REST a livello di codice, vedere [gestione del tempo serie Insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
