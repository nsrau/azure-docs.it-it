---
title: Creare un'app per la logica usando un modello di Azure | Microsoft Docs
description: Usare un modello di Azure Resource Manager per distribuire un'app per la logica per la definizione dei flussi di lavoro.
services: logic-apps
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2016
ms.author: LADocs; mandia
ms.openlocfilehash: 161adeacd6da2b15225c8a4ddae171e19e539967
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-logic-app-using-a-template"></a>Creare un'app per la logica usando un modello
I modelli rappresentano un modo rapido per usare diversi connettori nell'app per la logica. Le app per la logica comprendono i modelli di Azure Resource Manager utili per creare un'app per la logica che può essere utilizzata per definire i flussi di lavoro aziendali. Questo consente di definire le risorse da distribuire e le modalità di definizione dei parametri specificati durante l'esecuzione della distribuzione. È possibile usare questo modello per il proprio scenario aziendale o personalizzarlo in base alle esigenze.

Per informazioni dettagliate sulle proprietà delle app per la logica, vedere [API di gestione del flusso di lavoro delle app per la logica](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Per esempi della definizione stessa, vedere [Creare definizioni dell'app per la logica](logic-apps-author-definitions.md). 

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../azure-resource-manager/resource-group-authoring-templates.md).

Per il modello completo, vedere [Modello di app per la logica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-deploy"></a>Cosa viene distribuito
Con questo modello si distribuisce un'app per la logica.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:  

[![Distribuzione in Azure](media/logic-apps-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri
[!INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri
     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }

## <a name="resources-to-deploy"></a>Risorse da distribuire
### <a name="logic-app"></a>App per la logica
Crea l'app per la logica.

Il modello usa un valore di parametro per il nome dell'app per la logica. Imposta la località dell'app per la logica sulla stessa località del gruppo di risorse. 

Questa particolare definizione viene eseguita una volta ogni ora e consente di eseguire il ping del percorso specificato nel parametro **testUri** . 

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


## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup



