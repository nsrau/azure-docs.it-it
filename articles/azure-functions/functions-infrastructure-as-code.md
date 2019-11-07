---
title: Automatizzare la distribuzione di risorse per un'app per le funzioni in Funzioni di Azure | Microsoft Docs
description: Informazioni su come creare un modello di Azure Resource Manager per distribuire l'app per le funzioni.
author: ggailey777
manager: gwallace
keywords: funzioni di azure, funzioni, architettura senza server, infrastruttura come codice, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 8435aab65d26627de26fb8b5ad0510fcd7c57c33
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575945"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizzare la distribuzione di risorse per l'app per le funzioni in Funzioni di Azure

È possibile usare un modello di Azure Resource Manager per distribuire un'app per le funzioni. Questo articolo descrive le risorse e i parametri necessari per questa operazione. A seconda dei [trigger e dei binding](functions-triggers-bindings.md) potrebbe essere necessario distribuire risorse aggiuntive nell'app per le funzioni.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Per i modelli di esempio, vedere:
- [App per le funzioni in un piano a consumo]
- [App per le funzioni in un piano di servizio app di Azure]

## <a name="required-resources"></a>Risorse necessarie

Una distribuzione di funzioni di Azure è in genere costituita da queste risorse:

| Gruppi                                                                           | Requisito | Guida di riferimento a sintassi e proprietà                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Un'app per le funzioni                                                                     | Obbligatorio    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Un account di [archiviazione di Azure](../storage/index.yml)                                   | Obbligatorio    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Componente [Application Insights](../azure-monitor/app/app-insights-overview.md) | Facoltativo    | [Microsoft. Insights/Components](/azure/templates/microsoft.insights/components)         |   |
| [Piano di hosting](./functions-scale.md)                                             | Facoltativo<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> Un piano di hosting è necessario solo quando si sceglie di eseguire l'app per le funzioni in un [piano Premium](./functions-premium-plan.md) (in anteprima) o in un [piano di servizio app](../app-service/overview-hosting-plans.md).

> [!TIP]
> Sebbene non sia obbligatorio, è consigliabile configurare Application Insights per l'app.

<a name="storage"></a>
### <a name="storage-account"></a>Account di archiviazione

Per un'app per le funzioni è necessario l'account di archiviazione di Azure. È necessario un account per utilizzo generico che supporti BLOB, tabelle, code e i file. Per altre informazioni, vedere i [requisiti dell'account di archiviazione per Funzioni di Azure](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

È anche necessario specificare la proprietà `AzureWebJobsStorage` come impostazione dell'app nella configurazione del sito. Se l'app per le funzioni non usa Application Insights per il monitoraggio, è necessario specificare anche `AzureWebJobsDashboard` come impostazione dell'app.

Il runtime di Funzioni di Azure usa la stringa di connessione `AzureWebJobsStorage` per creare code interne.  Quando Application Insights non è abilitato, il runtime usa la stringa di connessione `AzureWebJobsDashboard` per accedere all'archivio tabelle di Azure e fornire dati per la scheda **Monitoraggio** nel portale.

Queste proprietà sono specificate nella raccolta `appSettings` dell'oggetto `siteConfig`:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Application Insights è consigliato per il monitoraggio delle app per le funzioni. La risorsa Application Insights viene definita con il tipo **Microsoft. Insights/Components** e il tipo **Web**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Inoltre, la chiave di strumentazione deve essere fornita all'app per le funzioni usando l'impostazione dell'applicazione `APPINSIGHTS_INSTRUMENTATIONKEY`. Questa proprietà viene specificata nella raccolta `appSettings` nell'oggetto `siteConfig`:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Piano di hosting

La definizione del piano di hosting varia e può essere una delle seguenti:
* [Piano a consumo](#consumption) (impostazione predefinita)
* [Piano Premium](#premium) (in anteprima)
* [Piano di servizio app](#app-service-plan)

### <a name="function-app"></a>App per le funzioni

La risorsa dell'app per le funzioni viene definita usando una risorsa di tipo **Microsoft. Web/sites** e Kind **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Se si definisce in modo esplicito un piano di hosting, è necessario un elemento aggiuntivo nella matrice dependsOn: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Un'app per le funzioni deve includere le impostazioni dell'applicazione seguenti:

| Nome impostazione                 | Descrizione                                                                               | Valori di esempio                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Una stringa di connessione a un account di archiviazione che il runtime di funzioni per l'accodamento interno | Vedere l' [account di archiviazione](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Versione del runtime di funzioni di Azure                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Lo stack del linguaggio da usare per le funzioni in questa app                                   | `dotnet`, `node`, `java` o `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Necessaria solo se si usa lo stack del linguaggio `node`, specifica la versione da usare              | `10.14.1`                             |

Queste proprietà vengono specificate nella raccolta `appSettings` nella proprietà `siteConfig`:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>Distribuisci nel piano a consumo

Il piano a consumo alloca automaticamente funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce le prestazioni quando il codice non è in esecuzione. Non è necessario pagare per le macchine virtuali inattive e non è necessario riservare in anticipo la capacità. Per altre informazioni, vedere [Ridimensionamento e hosting di Funzioni di Azure](functions-scale.md#consumption-plan).

Per un modello di Azure Resource Manager di esempio, vedere [App per le funzioni in un piano a consumo].

### <a name="create-a-consumption-plan"></a>Creare un piano a consumo

Non è necessario definire un piano a consumo. Una verrà creata o selezionata automaticamente in base all'area quando si crea la risorsa dell'app per le funzioni.

Il piano a consumo è un tipo speciale di risorsa "server farm". Per Windows, è possibile specificarla usando il valore `Dynamic` per le proprietà `computeMode` e `sku`:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> Non è possibile definire in modo esplicito il piano a consumo per Linux. Verrà creata automaticamente.

Se si definisce in modo esplicito il piano a consumo, sarà necessario impostare la proprietà `serverFarmId` nell'app in modo che punti all'ID risorsa del piano. È necessario assicurarsi che l'app per le funzioni disponga di un'impostazione `dependsOn` anche per il piano.

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

#### <a name="windows"></a>Windows

In Windows un piano a consumo richiede due impostazioni aggiuntive nella configurazione del sito: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Queste proprietà consentono di configurare l'account di archiviazione e il percorso in cui vengono archiviati il codice dell'app per le funzioni e la configurazione.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

In Linux l'app per le funzioni deve avere la proprietà `kind` impostata su `functionapp,linux` ed è necessario impostare la proprietà `reserved` su `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>Distribuisci nel piano Premium

Il piano Premium offre la stessa scalabilità del piano a consumo, ma include risorse dedicate e funzionalità aggiuntive. Per altre informazioni, vedere [piano Premium di funzioni di Azure](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Creare un piano Premium

Un piano Premium è un tipo speciale di risorsa "server farm". È possibile specificarlo utilizzando `EP1`, `EP2` o `EP3` per il valore della proprietà `sku`.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

Un'app per le funzioni in un piano Premium deve avere la proprietà `serverFarmId` impostata sull'ID risorsa del piano creato in precedenza. Inoltre, un piano Premium richiede due impostazioni aggiuntive nella configurazione del sito: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Queste proprietà consentono di configurare l'account di archiviazione e il percorso in cui vengono archiviati il codice dell'app per le funzioni e la configurazione.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>Distribuisci nel piano di servizio app

Nel piano di servizio app, le app per le funzioni vengono eseguite in macchine virtuali dedicate in SKU Basic, Standard e Premium, analogamente alle app Web. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/overview-hosting-plans.md).

Per un modello di Azure Resource Manager di esempio, vedere [App per le funzioni in un piano di servizio app di Azure].

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

Un piano di servizio app è definito da una risorsa "server farm".

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Per eseguire l'app in Linux, è necessario impostare anche il `kind` su `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Creare un'app per le funzioni 

Un'app per le funzioni in un piano di servizio app deve avere la proprietà `serverFarmId` impostata sull'ID risorsa del piano creato in precedenza.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

Le app Linux devono includere anche una proprietà `linuxFxVersion` in `siteConfig`. Se si distribuisce semplicemente il codice, il valore per questo è determinato dallo stack di runtime desiderato:

| Stack            | Valore di esempio                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Se si [distribuisce un'immagine del contenitore personalizzata](./functions-create-function-linux-custom-image.md), è necessario specificarla con `linuxFxVersion` e includere la configurazione che consente il pull dell'immagine, come nel [app Web per contenitori](/azure/app-service/containers). Impostare anche `WEBSITES_ENABLE_APP_SERVICE_STORAGE` su `false`, poiché il contenuto dell'app viene fornito nel contenitore stesso:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Personalizzazione di una distribuzione

Un'app per le funzioni contiene numerose risorse figlio che possono essere usate nella distribuzione, incluse le impostazioni dell'app e le opzioni di controllo del codice sorgente. È anche possibile scegliere di rimuovere la risorsa figlio **sourcecontrols** e usare un'altra [opzione di distribuzione](functions-continuous-deployment.md).

> [!IMPORTANT]
> Per poter distribuire l'applicazione usando Azure Resource Manager, è importante comprendere come vengono distribuite le risorse in Azure. Nell'esempio seguente, le configurazioni di livello superiore vengono applicate usando **siteConfig**. È importante impostare le configurazioni a un livello superiore perché forniscono informazioni al motore di runtime e di distribuzione di Funzioni di Azure. Le informazioni di livello superiore sono necessarie prima di applicare la risorsa figlia **sourcecontrols/web**. Anche se è possibile configurare queste impostazioni nella risorsa **config/appSettings** del livello figlio, in alcuni casi l'app per le funzioni deve essere distribuita *prima* di applicare **config/appSettings**. Quando ad esempio si usano le funzioni con [app per la logica](../logic-apps/index.yml), le funzioni sono una dipendenza di un'altra risorsa.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Questo modello usa il valore dell'impostazione app [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file), che imposta la directory di base in cui il motore di distribuzione di Funzioni di Azure (Kudu) cercherà il codice distribuibile. Nel repository le funzioni sono in una sottocartella della cartella **src**. Pertanto, nell'esempio precedente abbiamo impostato il valore di impostazione dell'app su `src`. Se le funzioni sono nella radice del repository o se non si sta distribuendo dal controllo del codice sorgente, è possibile rimuovere questo valore di impostazione dell'app.

## <a name="deploy-your-template"></a>Distribuire il modello

Il modello può essere distribuito in uno dei modi seguenti:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Pulsante Deploy to Azure per la distribuzione in Azure

Sostituire ```<url-encoded-path-to-azuredeploy-json>``` con una versione [con codifica URL](https://www.bing.com/search?q=url+encode) del percorso non elaborato del file `azuredeploy.json` in GitHub.

Di seguito è riportato un esempio che usa la sintassi markdown:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Di seguito è riportato un esempio che usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Distribuire tramite PowerShell

I comandi di PowerShell seguenti creano un gruppo di risorse e distribuiscono un modello che crea un'app per le funzioni con le risorse necessarie. Per eseguire localmente, è necessario aver installato [Azure PowerShell](/powershell/azure/install-az-ps) . Eseguire [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) per accedere.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

Per testare questa distribuzione, è possibile usare un [modello come questo](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) che crea un'app per le funzioni in Windows in un piano a consumo. Sostituire `<function-app-name>` con un nome univoco per l'app per le funzioni.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come sviluppare e configurare le Funzioni di Azure.

* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Come configurare le impostazioni dell'app per le funzioni di Azure](functions-how-to-use-azure-function-app-settings.md)
* [Create your first Azure function](functions-create-first-azure-function.md) (Creare la prima funzione di Azure)

<!-- LINKS -->

[App per le funzioni in un piano a consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[App per le funzioni in un piano di servizio app di Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
