<properties 
	pageTitle="Creare un'app per la logica usando modelli di Gestione risorse di Azure in Servizio app di Azure | Microsoft Azure" 
	description="Usare un modello di Gestione risorse di Azure per distribuire un'app per la logica vuota per la definizione dei flussi di lavoro." 
	services="logic-apps" 
	documentationCenter="" 
	authors="MSFTMan" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="deonhe"/>

# Creare un'app per la logica usando un modello

Per creare un'app per la logica vuota da usare per definire i flussi di lavoro, è possibile ricorrere a un modello di Gestione risorse di Azure, che consente di definire le risorse da distribuire e le modalità di definizione dei parametri specificati durante l'esecuzione della distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per informazioni dettagliate sulle proprietà delle app per la logica, vedere [API di gestione del flusso di lavoro delle app per la logica](https://msdn.microsoft.com/library/azure/mt643788.aspx).

Per esempi della definizione stessa, vedere [Creare definizioni dell'app per la logica](app-service-logic-author-definitions.md).

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere [Modello di app per la logica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## Elementi distribuiti

Con questo modello si distribuisce un'app per la logica.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## Parametri

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/status/feed/"
      }
    
## Risorse da distribuire

### App per la logica

Crea l'app per la logica.

Il modello usa un valore di parametro per il nome dell'app per la logica. Imposta la località dell'app per la logica sulla stessa località del gruppo di risorse.

Questa particolare definizione viene eseguita una volta ogni ora e consente di eseguire il ping del percorso specificato nel parametro **testUri**.

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
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
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Interfaccia della riga di comando di Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=AcomDC_0803_2016-->