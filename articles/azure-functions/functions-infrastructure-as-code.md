---
title: Automatizzare la distribuzione di risorse per un&quot;app per le funzioni in Funzioni di Azure | Microsoft Docs
description: Informazioni su come creare un modello di Azure Resource Manager per distribuire l&quot;app per le funzioni.
services: Functions
documtationcenter: na
author: lindydonna
manager: erikre
editor: 
tags: 
keywords: funzioni di azure, funzioni, architettura senza server, infrastruttura come codice, azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: 
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: donnam;glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 532ff423ff53567b6ce40c0ea7ec09a689cee1e7
ms.openlocfilehash: 9458b3b619649d094ddab1638e146571d9268fb0
ms.contentlocale: it-it
ms.lasthandoff: 06/05/2017


---

# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Automatizzare la distribuzione di risorse per l'app per le funzioni in Funzioni di Azure

È possibile usare un modello di Azure Resource Manager per distribuire un'app per le funzioni. Questo articolo descrive le risorse e i parametri necessari per questa operazione. A seconda dei [trigger e dei binding](functions-triggers-bindings.md) potrebbe essere necessario distribuire risorse aggiuntive nell'app per le funzioni.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Per i modelli di esempio, vedere:
- [App per le funzioni nel piano a consumo]
- [App per le funzioni nel piano di servizio app di Azure]

## <a name="required-resources"></a>Risorse necessarie

Un'app per le funzioni richiede le risorse seguenti:

* Un account di [archiviazione di Azure](../storage/index.md)
* Un piano di hosting (piano a consumo o piano di servizio app)
* Un'app per le funzioni 

### <a name="storage-account"></a>Account di archiviazione

Per un'app per le funzioni è necessario l'account di archiviazione di Azure. È necessario un account per utilizzo generico che supporti BLOB, tabelle, code e i file. Per altre informazioni, vedere i [requisiti dell'account di archiviazione per Funzioni di Azure](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

È anche necessario specificare le proprietà `AzureWebJobsStorage` e `AzureWebJobsDashboard` come impostazioni dell'app nella configurazione del sito. Il runtime di Funzioni di Azure usa la stringa di connessione `AzureWebJobsStorage` per creare code interne. La stringa di connessione `AzureWebJobsDashboard` viene usata per accedere all'archivio tabelle di Azure e abilitare la scheda **Monitoraggio** nel portale.

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
```    

### <a name="hosting-plan"></a>Piano di hosting

La definizione del piano di hosting varia a seconda che si usi un piano a consumo o un piano di servizio app. Vedere [Distribuire un'app per le funzioni nel piano a consumo](#consumption) e [Distribuire un'app per le funzioni nel piano di servizio app](#app-service-plan).

### <a name="function-app"></a>App per le funzioni

L'app per le funzioni viene definita usando una risorsa **Microsoft.Web/Site** di tipo **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>Distribuire un'app per le funzioni nel piano a consumo

È possibile eseguire un'app per le funzioni in due modalità diverse, ovvero con piano a consumo e piano di servizio app di Azure. Il piano a consumo alloca automaticamente funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce le prestazioni quando il codice non è in esecuzione. Non è quindi necessario pagare per le macchine virtuali inattive e riservare in anticipo la capacità. Per altre informazioni sui piani di hosting, vedere [Piani a consumo e piani di servizio app di Funzioni di Azure](functions-scale.md).

Per un modello di Azure Resource Manager di esempio, vedere [App per le funzioni nel piano a consumo].

### <a name="create-a-consumption-plan"></a>Creare un piano a consumo

Un piano a consumo è un tipo particolare di risorsa "serverfarm". Viene specificato usando il valore `Dynamic` per le proprietà `computeMode` e `sku`:

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

### <a name="create-a-function-app"></a>Creare un'app per le funzioni

Un piano a consumo richiede anche due impostazioni aggiuntive nella configurazione del sito: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` e `WEBSITE_CONTENTSHARE`. Queste proprietà consentono di configurare l'account di archiviazione e il percorso in cui vengono archiviati il codice dell'app per le funzioni e la configurazione.

```json
{
    "apiVersion": "2015-08-01",
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
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
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
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Distribuire un'app per le funzioni nel piano di servizio app

Nel piano di servizio app, le app per le funzioni vengono eseguite in macchine virtuali dedicate in SKU Basic, Standard e Premium, analogamente alle app Web. Per informazioni dettagliate sul funzionamento del piano di servizio app, vedere [Panoramica approfondita dei piani di servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Per un modello di Azure Resource Manager di esempio, vedere [App per le funzioni nel piano di servizio app di Azure].

### <a name="create-an-app-service-plan"></a>Creare un piano di servizio app

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

### <a name="create-a-function-app"></a>Creare un'app per le funzioni 

Dopo aver selezionato un'opzione di scalabilità, creare un'app per le funzioni. L'app è il contenitore che incorpora tutte le funzioni.

Un'app per le funzioni contiene numerose risorse figlio che possono essere usate nella distribuzione, incluse le impostazioni dell'app e le opzioni di controllo del codice sorgente. È anche possibile scegliere di rimuovere la risorsa figlio **sourcecontrols** e usare un'altra [opzione di distribuzione](functions-continuous-deployment.md).

> [!IMPORTANT]
> Per poter distribuire l'applicazione usando Azure Resource Manager, è importante comprendere come vengono distribuite le risorse in Azure. Nell'esempio seguente, le configurazioni di livello superiore vengono applicate usando **siteConfig**. È importante impostare le configurazioni a un livello superiore perché forniscono informazioni al motore di runtime e di distribuzione di Funzioni di Azure. Le informazioni di livello superiore sono necessarie prima di applicare la risorsa figlia **sourcecontrols/web**. Anche se è possibile configurare queste impostazioni nella risorsa **config/appSettings** del livello figlio, in alcuni casi l'app per le funzioni deve essere distribuita *prima* di applicare **config/appSettings**. Quando ad esempio si usano le funzioni con [app per la logica](../logic-apps/index.md), le funzioni sono una dipendenza di un'altra risorsa.

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
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Di seguito è riportato un esempio che usa HTML:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come sviluppare e configurare le Funzioni di Azure.

* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Come configurare le impostazioni dell'app per le funzioni di Azure](functions-how-to-use-azure-function-app-settings.md)
* [Create your first Azure function](functions-create-first-azure-function.md) (Creare la prima funzione di Azure)

<!-- LINKS -->

[App per le funzioni nel piano a consumo]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[App per le funzioni nel piano di servizio app di Azure]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json

