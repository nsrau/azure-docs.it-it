---
title: Esempi di modello di Resource Manager per le risorse di Servizio app di Azure + Application Insights
description: Modelli di Azure Resource Manager di esempio per distribuire un servizio app di Azure con una risorsa di Application Insights.
ms.subservice: application-insights
ms.topic: sample
ms.custom: devx-track-dotnet
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/06/2020
ms.openlocfilehash: 966dc0650d445a2181dc6a7d6ea1c57f223fd6f9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934243"
---
# <a name="resource-manager-template-samples-for-creating-azure-app-services-web-apps-with-application-insights-monitoring"></a>Esempio di modello di Resource Manager per la creazione di app Web di Servizi app di Azure con il monitoraggio di Application Insights

Questo articolo include [modelli di Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) di esempio per distribuire e configurare [risorse di Application Insights classiche](../app/create-new-resource.md) insieme a un'app Web di Servizi app di Azure. Ogni esempio include un file modello e un file di parametri con valori di esempio da fornire al modello.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]

## <a name="net-core-runtime"></a>Runtime di .NET Core

L'esempio seguente consente di creare un'app Web del Servizio app di Azure di base con il runtime di .NET Core e una [risorsa di Application Insights classica](../app/create-new-resource.md) con il monitoraggio abilitato. 

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
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "errorLink": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        },
                        {
                            "name": "ANCM_ADDITIONAL_ERROR_PAGE_LINK",
                            "value": "[parameters('errorLink')]"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
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
    "currentStack": {
      "value": "dotnetcore"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "errorLink": {
      "value": "https://web-app-name-01.scm.azurewebsites.net/detectors?type=tools&name=eventviewer"
    }
  }
}

```

## <a name="aspnet-runtime"></a>Runtime ASP.NET

L'esempio seguente consente di creare un'app Web del Servizio app di Azure di base con il runtime ASP.NET e una [risorsa di Application Insights classica](../app/create-new-resource.md) con il monitoraggio abilitato. 

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
        "currentStack": {
            "type": "string"
        },
        "phpVersion": {
            "type": "string"
        },
        "netFrameworkVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "metadata": [
                        {
                            "name": "CURRENT_STACK",
                            "value": "[parameters('currentStack')]"
                        }
                    ],
                    "phpVersion": "[parameters('phpVersion')]",
                    "netFrameworkVersion": "[parameters('netFrameworkVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": true
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": {},
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
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
      "value": "web-app-name-01"
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
    "currentStack": {
      "value": "dotnet"
    },
    "phpVersion": {
      "value": "OFF"
    },
    "netFrameworkVersion": {
      "value": "v4.0"
    }
  }
}
```

## <a name="nodejs-runtime-linux"></a>Runtime Node.js (Linux)

L'esempio seguente consente di creare un'app Web Linux del Servizio app di Azure di base con il runtime Node.js e una [risorsa di Application Insights classica](../app/create-new-resource.md) con il monitoraggio abilitato. 

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
        "linuxFxVersion": {
            "type": "string"
        }
    },
    "resources": [
        {
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "location": "[parameters('location')]",
            "tags": {},
            "dependsOn": [
                "microsoft.insights/components/web-app-name-01"
            ],
            "properties": {
                "name": "[parameters('name')]",
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/web-app-name-01', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        },
                        {
                            "name": "XDT_MicrosoftApplicationInsights_Mode",
                            "value": "default"
                        }
                    ],
                    "linuxFxVersion": "[parameters('linuxFxVersion')]",
                    "alwaysOn": "[parameters('alwaysOn')]"
                },
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "clientAffinityEnabled": false
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "web-app-name-01",
            "type": "microsoft.insights/components",
            "location": "centralus",
            "tags": null,
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ]
}
```

### <a name="parameter-file"></a>File di parametri

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "subscriptionId": {
      "value": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    },
    "name": {
      "value": "web-app-name-01"
    },
    "location": {
      "value": "Central US"
    },
    "hostingPlanName": {
      "value": "App-planTest01-916e"
    },
    "serverFarmResourceGroup": {
      "value": "app_resource_group_custom"
    },
    "alwaysOn": {
      "value": true
    },
    "linuxFxVersion": {
      "value": "NODE|12-lts"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* [Passare ad altri esempi per Monitoraggio di Azure](resource-manager-samples.md).
* [Altre informazioni sulle risorse di Application Insights classiche](../app/create-new-resource.md).
