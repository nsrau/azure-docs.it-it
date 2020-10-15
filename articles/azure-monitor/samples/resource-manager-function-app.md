---
title: Esempi di modello di Resource Manager per le risorse di App per le funzioni di Azure + Application Insights
description: Modelli di Azure Resource Manager di esempio per distribuire un'app per le funzioni di Azure con una risorsa di Application Insights.
ms.subservice: application-insights
ms.topic: sample
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/06/2020
ms.openlocfilehash: 6b869f485285758336c3d58b7e298e6e002797dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927499"
---
# <a name="resource-manager-template-sample-for-creating-azure-function-apps-with-application-insights-monitoring"></a>Esempio di modello di Resource Manager per la creazione di App per le funzioni di Azure con il monitoraggio di Application Insights

Questo articolo include [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) di esempio per distribuire e configurare [risorse di Application Insights classiche](../app/create-new-resource.md) insieme a un'app per le funzioni di Azure. L'esempio include un file modello e un file di parametri con valori di esempio da fornire al modello.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="azure-function-app"></a>App per le funzioni di Azure

L'esempio seguente consente di creare un'app per le funzioni di Azure per .NET Core 3.1 in esecuzione in un piano di servizio app di Windows e una [risorsa di Application Insights classica](../app/create-new-resource.md) con il monitoraggio abilitato. 

### <a name="template-file"></a>File modello

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionId": {
            "type": "string"
        },
        "name": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "alwaysOn": {
            "type": "bool"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "kind": "functionapp",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/function-app-01",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "FUNCTIONS_EXTENSION_VERSION",
                            "value": "~3"
                        },
                        {
                            "name": "FUNCTIONS_WORKER_RUNTIME",
                            "value": "dotnet"
                        },
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/function-app-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/function-app-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "AzureWebJobsStorage",
                            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
                        }
                    ],
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "function-app-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        },
        {
            "apiVersion": "2019-06-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "tags": {},
            "sku": {
                "name": "Standard_LRS"
            },
            "properties": {
                "supportsHttpsTrafficOnly": true
            }
        }
    ]
}
```

### <a name="parameters-file"></a>File dei parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "function-app-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "WebApplication2720191003010920Plan"
    },
    "serverFarmResourceGroup": {
      "value": "Testwebapp01"
    },
    "alwaysOn": {
      "value": true
    },
    "storageAccountName": {
      "value": "storageaccountest93"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Passare ad altri esempi per Monitoraggio di Azure](resource-manager-samples.md).
* [Altre informazioni sulle risorse di Application Insights classiche](../app/create-new-resource.md).
