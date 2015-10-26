<properties 
	pageTitle="Creare un'app per la logica" 
	description="Usare un modello di Gestione risorse di Azure per distribuire un'app per la logica vuota per la definizione dei flussi di lavoro." 
	services="app-service\logic" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-logic" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/01/2015" 
	ms.author="tomfitz"/>

# Creare un'app per la logica usando un modello

Questo argomento illustra come creare un modello di Gestione risorse di Azure per creare un'app per la logica vuota da usare per definire i flussi di lavoro. Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per informazioni dettagliate sulle proprietà delle app per la logica, vedere [API di gestione del flusso di lavoro delle app per la logica](https://msdn.microsoft.com/library/azure/dn948513.aspx). Per esempi della definizione stessa, vedere [Creare definizioni dell'app per la logica](app-service-logic-author-definitions.md).

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere [Modello di app per la logica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## Elementi distribuiti

Con questo modello, verrà distribuita un'app per la logica.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## Parametri

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Risorse da distribuire

### Piano di servizio app

Crea un piano di servizio app.

Usa la stessa località del gruppo di risorse in cui viene distribuito.

    {
        "apiVersion": "2014-06-01",
        "name": "[parameters('svcPlanName')]",
        "type": "Microsoft.Web/serverfarms",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "AppServicePlan"
        },
        "properties": {
            "name": "[parameters('svcPlanName')]",
            "sku": "[parameters('sku')]",
            "workerSize": "[parameters('svcPlanSize')]",
            "numberOfWorkers": 1
        }
    }

### App per la logica

Crea l'app per la logica.

Il modello usa un valore di parametro per il nome dell'app per la logica. Imposta la località dell'app per la logica sulla stessa località del gruppo di risorse.

Questa definizione specifica verrà eseguita una volta all'ora e verrà effettuato il ping del percorso specificato nel parametro **testUri**.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "properties": {
            "sku": {
                "name": "[parameters('sku')]",
                "plan": {
                    "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
                }
            },
            "definition": {
                "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2014-12-01-preview/workflowdefinition.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {
                    "testURI": {
                        "type": "string",
                        "defaultValue": "[parameters('testUri')]"
                    }
                },
                "triggers": {
                    "recurrence": {
                        "type": "recurrence",
                        "recurrence": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                },
                "actions": {
                    "http": {
                        "type": "Http",
                        "inputs": {
                            "method": "GET",
                            "uri": "@parameters('testUri')"
                        }
                    }
                },
                "outputs": { }
            },
            "parameters": { }
        }
    }

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Interfaccia della riga di comando di Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=Oct15_HO3-->