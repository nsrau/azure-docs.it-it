---
title: Automatizzare la distribuzione di risorse per l&quot;app Funzioni di Azure | Microsoft Docs
description: Informazioni su come creare un modello di Azure Resource Manager per distribuire le app Funzioni di Azure.
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
ms.sourcegitcommit: 360abaa575e473e18e55d0784730f4bd5635f3eb
ms.openlocfilehash: 979537bfe6b0e14a9208871fc9862661d2fb2e6c


---

# <a name="automate-resource-deployment-for-your-azure-functions-app"></a>Automatizzare la distribuzione di risorse per l'app Funzioni di Azure

È possibile usare un modello di Azure Resource Manager per distribuire un'app Funzioni di Azure. Informazioni su come definire le risorse di base necessarie per un'app Funzioni di Azure e i parametri che vengono specificati durante la distribuzione. A seconda dei [trigger e delle associazioni](functions-triggers-bindings.md) nell'app Funzioni, per una corretta configurazione dell'infrastruttura come codice dell'applicazione, potrebbe essere necessario distribuire risorse aggiuntive.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Per esempi di modelli completi, vedere [Create a Consumption plan-based Azure Functions app](https://github.com/Azure/azure-quickstart-templates/blob/052db5feeba11f85d57f170d8202123511f72044/101-function-app-create-dynamic/azuredeploy.json) (Creazione di un'app Funzioni di Azure in base al piano a consumo) e [Create an App Service plan-based Azure Functions app](https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json) (Creazione di un'app Funzioni di Azure in base a un piano di servizio app).

## <a name="required-resources"></a>Risorse necessarie

È possibile usare gli esempi in questo articolo per creare un'app Funzioni di Azure di base. Per l'app sono necessarie queste risorse:

* Account di [archiviazione di Azure](../storage/index.md)
* Piano di hosting (piano a consumo o piano di servizio app di Azure)
* App per le funzioni (`type`: **Microsoft.Web/Site**, `kind`: **functionapp**)

## <a name="parameters"></a>Parametri

È possibile usare Azure Resource Manager per definire i parametri per i valori da specificare durante la distribuzione del modello. La sezione **Parametri** del modello presenta tutti i valori del parametro. Definire i parametri per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto.

Le [variabili](../azure-resource-manager/resource-group-authoring-templates.md#variables) sono utili per i valori che non cambiano in base alla singola distribuzione e per parametri che richiedono una trasformazione prima di essere usati in un modello (ad esempio per superare le regole di convalida).

Durante la definizione dei parametri, usare il campo **allowedValues** per specificare i valori che l'utente può fornire durante la distribuzione. Usare il campo **defaultValue** per assegnare un valore al parametro, se non viene specificato alcun valore durante la distribuzione.

Il modello di Azure Resource Manager richiede i valori dei parametri seguenti.

### <a name="appname"></a>appName

Il nome dell'app Funzioni di Azure che si desidera creare.

```json
"appName": {
    "type": "string"
}
```

### <a name="location"></a>location

Posizione in cui distribuire l'app Funzioni.

> [!NOTE]
> Usare il parametro **defaultValue** per ereditare la posizione del gruppo di risorse oppure nel caso in cui non venga specificato un valore per il parametro durante una distribuzione di PowerShell o dell'interfaccia della riga di comando. Se si distribuisce l'app dal Portale di Azure, selezionare un valore nella casella di riepilogo a discesa del parametro **allowedValues**.

> [!TIP]
> Per un elenco aggiornato delle aree in cui è possibile usare Funzioni di Azure, vedere la pagina [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).

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
        "description": "Source code repository branch"
      }
    }
```

### <a name="sourcecodemanualintegration-optional"></a>sourceCodeManualIntegration (facoltativo)

```json
"sourceCodeManualIntegration": {
    "type": "bool",
    "defaultValue": false,
    "metadata": {
        "description": "Use 'true' if you are deploying from the base repo. Use 'false' if you are deploying from your own fork. If you use 'false', make sure that you have Administrator rights in the repo. If you get an error, manually add GitHub integration to another web app, to associate a GitHub access token with your Azure subscription."
    }
}
```

## <a name="variables"></a>Variabili

I modelli di Azure Resource Manager utilizzano le variabili per incorporare i parametri, pertanto è possibile utilizzare più impostazioni specifiche nel modello.

Nell'esempio seguente, per soddisfare i [requisiti di denominazione](../storage/storage-create-storage-account.md#create-a-storage-account) dell'account di archiviazione di Azure, utilizziamo le variabili per applicare le [funzioni del modello di Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md) per convertire il valore inserito **appName** in lettere minuscole.

```json
"variables": {
    "lowerSiteName": "[toLower(parameters('appName'))]",
    "storageAccountName": "[concat(variables('lowerSiteName'))]"
}
```

## <a name="resources-to-deploy"></a>Risorse da distribuire

### <a name="storage-account"></a>Account di archiviazione

L'account di archiviazione di Azure è necessario per l'app Funzioni di Azure.

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

### <a name="hosting-plan-consumption-vs-app-service"></a>Piano di hosting: piano a consumo o piano di Servizio app

In alcuni scenari, è possibile che la piattaforma debba ridimensionare le funzioni su richiesta, questa viene definita anche scalabilità completamente gestita (con un piano di hosting a consumo). In alternativa, per le funzioni è possibile scegliere la scalabilità gestita dall'utente. Nella scalabilità gestita dall'utente, le funzioni vengono eseguite 24 ore su 24, 7 giorni su 7 su un hardware dedicato (con un piano di hosting del servizio app). È possibile impostare manualmente o automaticamente il numero di istanze. La scelta del piano di hosting potrebbe essere basata sulle funzionalità disponibili nel piano o sull'architettura per costo. Per ulteriori informazioni sui piani di hosting, vedere [Scaling Azure Functions](functions-scale.md) (Scalabilità delle funzioni di Azure).

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

### <a name="functions-app-a-site"></a>App Funzioni (un sito)

Dopo aver selezionato un'opzione di scalabilità, creare un'app Funzioni. L'app è il contenitore che incorpora tutte le funzioni.

Un'app Funzioni contiene numerose risorse figlie che è possibile usare nella distribuzione, incluse le impostazioni app e le opzioni di controllo del codice sorgente. È inoltre possibile scegliere di rimuovere la risorsa figlia **sourcecontrols** e usare un'altra [opzione di distribuzione](functions-continuous-deployment.md).

> [!IMPORTANT]
> Per poter creare una configurazione di infrastruttura come codice efficace per l'applicazione usando Azure Resource Manager, è importante comprendere come vengono distribuite le risorse in Azure. Nell'esempio seguente, le configurazioni di livello superiore vengono applicate usando **siteConfig**. È importante impostare le configurazioni a un livello superiore in quanto forniscono informazioni al motore di runtime e di distribuzione di Funzioni di Azure. Le informazioni di livello superiore sono necessarie prima di applicare la risorsa figlia **sourcecontrols/web**. Sebbene sia possibile configurare queste impostazioni nella risorsa del livello figlio **config/appSettings**, in alcuni scenari, l'app Funzioni e le funzioni devono essere distribuite *prima* di applicare **config/appSettings**. In questi casi, ad esempio, nelle [app per la logica](../logic-apps/index.md), le funzioni sono una dipendenza di un'altra risorsa.

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
> Questo modello usa il valore dell'impostazione app [Progetto](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) che imposta la directory di base in cui il motore di distribuzione delle funzioni (Kudu) cercherà il codice distribuibile. Nel repository le funzioni sono in una sottocartella della cartella **src**. Pertanto, nell'esempio precedente abbiamo impostato il valore di impostazione dell'app su `src`. Se le funzioni sono nella radice del repository o se non si sta distribuendo dal controllo del codice sorgente, è possibile rimuovere questo valore di impostazione dell'app.

## <a name="deploy-your-template"></a>Distribuire il modello

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Interfaccia della riga di comando di Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Portale di Azure](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [API REST](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Pulsante Deploy to Azure per la distribuzione in Azure

Sostituire ```<url-encoded-path-to-azuredeploy-json>``` con una versione [con codifica URL](https://www.bing.com/search?q=url+encode) del percorso non elaborato del file `azuredeploy.json` in GitHub.

#### <a name="markdown"></a>Markdown

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

#### <a name="html"></a>HTML

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come sviluppare e configurare le Funzioni di Azure.

* [Guida di riferimento per gli sviluppatori a Funzioni di Azure](functions-reference.md)
* [Come configurare le impostazioni dell'app per le funzioni di Azure](functions-how-to-use-azure-function-app-settings.md)
* [Create your first Azure function](functions-create-first-azure-function.md) (Creare la prima funzione di Azure)



<!--HONumber=Feb17_HO1-->


