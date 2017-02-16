---
title: Automatizzare la distribuzione di risorse per Funzioni di Azure | Documentazione Microsoft
description: Informazioni su come creare un modello di Azure Resource Manager per distribuire le app per le funzioni in Microsoft Azure.
services: Functions
documtationcenter: na
author: mattchenderson
manager: erikre
editor: 
tags: 
keywords: funzioni di azure, funzioni, architettura senza server, infrastruttura come codice, azure resource manager
ms.assetid: 
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/23/2017
ms.author: cfowler;glenga
translationtype: Human Translation
ms.sourcegitcommit: d11ef8865d21dfe4e56bcb6fa08ce58c53f3d309
ms.openlocfilehash: 7c55a1d34df71c2c958f42f43810fbce7bd74e5d


---

# <a name="automate-the-deployment-of-resources-for-your-azure-functions-app"></a>Automatizzare la distribuzione di risorse per l'app per le funzioni di Azure

In questo argomento si apprenderà a creare un modello di Azure Resource Manager che consente di distribuire un'app per le funzioni. Verranno inoltre definite le risorse di base necessarie per una funzione di Azure e i parametri che vengono specificati quando viene eseguita la distribuzione. A seconda dei [trigger e delle associazioni](functions-triggers-bindings.md) usati nella funzione, potrebbe essere necessario distribuire risorse aggiuntive per includere l'intera applicazione come infrastruttura come codice.

Per altre informazioni sulla creazione di modelli, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

Per esempi di modelli completi, vedere [Create consumption-based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) (Creare una funzione di Azure in base al consumo) e/o [Create an App Service Plan based Azure Function](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json) (Creare una funzione di Azure in base a un piano di servizio app)

## <a name="what-is-deployed"></a>Elementi distribuiti

Con gli esempi seguenti, verrà creata un'app per le funzioni di Azure di base. Le risorse necessarie per un'app per le funzioni sono le seguenti:

* Account di [archiviazione di Azure](../storage/index.md)
* Piano di hosting (piano a consumo o piano di servizio app)
* App per le funzioni (risorsa Microsoft.Web/Site di tipologia **functionapp**)

## <a name="parameters"></a>Parametri

Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri. È necessario definire i parametri per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto.

Le [variabili](../azure-resource-manager/resource-group-authoring-templates.md#variables) sono utili per i valori che non cambiano in base alla singola distribuzione o per parametri che richiedono una trasformazione prima di essere usati in un modello, ad esempio per passare le regole di convalida.

Durante la definizione dei parametri, usare il campo **allowedValues** per specificare i valori che l'utente può fornire durante la distribuzione. Usare il campo **defaultValue** per assegnare un valore al parametro, se non viene specificato alcun valore durante la distribuzione.

Di seguito verranno descritti i parametri per il modello.

### <a name="appname"></a>appName

Nome della funzione da creare.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Località in cui verrà distribuita l'app per le funzioni.

> [!NOTE]
> Il parametro **defaultValue** viene usato per ereditare la località del gruppo di risorse oppure nel caso in cui non venga specificato un valore per il parametro durante una distribuzione di PowerShell o dell'interfaccia della riga di comando. Se la distribuzione viene eseguita dal portale, è disponibile una casella a discesa per selezionare un valore dall'elenco **allowedValues**.

> [!TIP]
> Per un elenco aggiornato delle aree in cui è disponibile il servizio Funzioni di Azure, vedere la pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

```json
"location": {
    "type": "string",
    "allowedValues": [
        "Brazil South",
        "East US",
        "East US 2",
        "Central US",
        "North Central US",
        "South Central US",
        "West US",
        "West US 2"
    ],
    "defaultValue": "[resourceGroup().location]"
}
```

### <a name="sourcecoderepositoryurl-optional"></a>sourceCodeRepositoryURL (facoltativo)

```json
"sourceCodeRepositoryURL": {
    "type": "string",
    "defaultValue": "",
    "metadata": {
    "description": "Source code repository URL"
}
```

### <a name="sourcecodebranch-optional"></a>sourceCodeBranch (facoltativo)

```json
    "sourceCodeBranch": {
      "type": "string",
      "defaultValue": "master",
      "metadata": {
        "description": "Sourcecode Repo branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (facoltativo)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo, 'false' if you are deploying from your own fork. If you're using 'false', make sure you have admin permissions to the repo. If you get an error, you should add GitHub integration to another web app manually, so that you get a GitHub access token associated with your Azure Subscription."
    }
}
```

## <a name="variables"></a>Variabili

Oltre ai parametri, i modelli di Azure Resource Manager includono anche dei tipi di variabili che possono incorporare parametri per creare impostazioni più specifiche da usare nel modello.

L'esempio seguente mostra come vengono usate le variabili per applicare le [funzioni del modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) per accettare il valore di appName specificato e convertirlo in lettere minuscole per garantire che vengano soddisfatti i [requisiti di denominazione](../storage/storage-create-storage-account.md#create-a-storage-account) per gli account di archiviazione di Azure.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Risorse da distribuire

### <a name="storage-account"></a>Account di archiviazione

L'account di archiviazione di Azure è una risorsa necessaria in Funzioni di Azure.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[variables('storageLocation')]",
    "properties": {
        "accountType": "[variables('storageAccountType')]"
    }
}
```

### <a name="hosting-plan-consumption-vs-app-service-plan"></a>Piano di hosting: piano a consumo o piano di servizio app

In determinati scenari di creazione delle funzioni, potrebbe risultare preferibile che la scalabilità delle funzioni sia completamente gestita, ovvero che le funzioni vengano ridimensionate su richiesta dalla piattaforma (a consumo). In alternativa, è possibile scegliere la scalabilità gestita dall'utente che prevede che le funzioni vengano eseguite 24 ore su 24, 7 giorni su 7 in hardware dedicato (piano di servizio app) in cui il numero di istanze possa essere configurato manualmente o automaticamente. La scelta di uno o dell'altro piano può essere basata sulle funzionalità disponibili nel piano o su una decisione relativa all'architettura in base al costo. Per altre informazioni sui piani di hosting disponibili, vedere l'articolo [Scalabilità di Funzioni di Azure](functions-scale.md).

#### <a name="consumption-plan"></a>Piano a consumo

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

#### <a name="app-service-plan"></a>Piano di servizio app

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="function-app-site"></a>App per le funzioni (sito)

Dopo aver selezionato l'opzione di ridimensionamento, si passerà alla creazione del contenitore che includerà tutte le funzioni, anche noto come app per le funzioni.

Un'app per le funzioni contiene numerose risorse figlio utili, ad esempio **Impostazioni app** e **Opzioni di controllo del codice sorgente**. È anche possibile scegliere di rimuovere la risorsa figlio **sourcecontrols** e sostituirla con un'altra [opzione di distribuzione](functions-continuous-deployment.md).

> [!IMPORTANT]
> È importante comprendere come vengono distribuite le risorse in Azure per poter creare una configurazione di infrastruttura come codice efficace per l'applicazione quando si usa Azure Resource Manager. In questo esempio si noterà che vengono applicate configurazioni di livello superiore tramite **siteConfig**. È importante impostare queste configurazioni a un livello superiore perché trasmettono il significato al motore di distribuzione e al runtime di Funzioni di Azure che vengono eseguiti prima dell'applicazione della risorsa figlio **sourcecontrols/web**. Sebbene sia possibile configurare queste impostazioni nella risorsa **config/appSettings** di livello figlio, in alcuni scenari sarà necessario distribuire l'app per le funzioni e le funzioni *prima* di applicare **config/appsettings**, poiché le funzioni sono una dipendenza di un'altra risorsa, ad esempio un'[app per la logica](../logic-apps/index.md).

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
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
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
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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
> Questo modello usa l'impostazione app [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) che imposta la directory di base in cui il motore di distribuzione delle funzioni (Kudu) cercherà il codice distribuibile. In questo esempio il valore è stato impostato su `src` perché le funzioni costituiscono un elemento figlio della cartella `src` nel repository GitHub. Se le funzioni usate si trovano direttamente nella radice del repository o la distribuzione non viene effettuata dal controllo del codice sorgente, è possibile rimuovere questa impostazione app.

## <a name="deploying-your-template"></a>Distribuzione del modello

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfaccia della riga di comando](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portale](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Pulsante Distribuisci in Azure

Sostituire ```<url-encoded-path-to-azuredeploy-json>``` con una versione [codificata in URL](https://www.bing.com/search?q=url+encode) del percorso non elaborato del file `azuredeploy.json` in GitHub.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso a distribuire un'app per le funzioni dal codice, vedere le risorse seguenti per altre informazioni su come sviluppare e configurare Funzioni di Azure:

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Come configurare le impostazioni dell'app per le funzioni di Azure](functions-how-to-use-azure-function-app-settings.md)
* [Creare la prima funzione di Azure](functions-create-first-azure-function.md)


<!--HONumber=Jan17_HO4-->


