<properties 
	pageTitle="Creare un'app per la logica con un'app per le API" 
	description="Usare un modello di Gestione risorse di Azure per distribuire un'app per la logica e un'app per le API." 
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
	ms.date="06/29/2015" 
	ms.author="tomfitz"/>

# Creare un'app per la logica e un'app per le API usando un modello

Questo argomento illustra come creare un modello di Gestione risorse di Azure per creare un'app per la logica con un'app per le API del servizio app È possibile usare le app per la logica per progettare flussi di lavoro che esprimano chiaramente il loro scopo tramite un trigger e una serie di passaggi, ciascuno dei quali richiama l'app per le API e, al contempo, gestisce in modo sicuro l'autenticazione e le procedure consigliate, ad esempio l'esecuzione durevole.

Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per informazioni dettagliate sulle proprietà delle app per la logica, vedere [API di gestione del flusso di lavoro delle app per la logica](https://msdn.microsoft.com/library/azure/dn948513.aspx). Per esempi della definizione stessa, vedere [Creare definizioni dell'app per la logica](app-service-logic-author-definitions.md).

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere [Modello di app per la logica con app per le API](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-api-app-create/azuredeploy.json).

## Elementi distribuiti

Con questo modello, verrà effettuato il provisioning di:

- App per la logica
- App per le API

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-api-app-create%2Fazuredeploy.json)

## Parametri

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### apiAppName

Nome dell'app per le API.

    "apiAppName": {
        "type": "string"
    }

### gatewayName

Nome del gateway.

    "gatewayName": {
        "type": "string"
    }

### gatewayToApiAppSecret

Segreto dell'app per le API.

    "gatewayToApiAppSecret": {
        "defaultValue": "0000000000000000000000000000000000000000000000000000000000000000",
        "type": "securestring"
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

### App Web che ospita il gateway

Crea l'app Web per ospitare il gateway.

    {
        "type": "Microsoft.Web/sites",
        "apiVersion": "2015-04-01",
        "name": "[parameters('gatewayName')]",
        "location": "[resourceGroup().location]",
        "kind": "gateway",
        "tags": {
            "displayName": "GatewayHost"
        },
        "resources": [
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/gateway",
                "dependsOn": [
                    "[resourceId('Microsoft.Web/sites',parameters('gatewayName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
                }
            }
        ],
        "dependsOn": [
            "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/',parameters('svcPlanName'))]"
        ],
        "properties": {
            "name": "[parameters('gatewayName')]",
            "gatewaySiteName": "[parameters('gatewayName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('svcPlanName'))]",
            "siteConfig": {
                "appSettings": [
                    {
                        "name": "ApiAppsGateway_EXTENSION_VERSION",
                        "value": "latest"
                    },
                    {
                        "name": "EmaStorage",
                        "value": "D:\\home\\data\\apiapps"
                    },
                    {
                        "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
                        "value": "1"
                    }
                ]
            }
        }
    }

### Gateway

Si noti che il gateway include una risorsa figlio per un token di autenticazione. Questo token viene usato dall'app per la logica per effettuare chiamate al gateway.

    {
        "type": "Microsoft.AppService/gateways",
        "apiVersion": "2015-03-01-preview",
        "name": "[parameters('gatewayName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "Gateway"
        },
        "resources": [
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/gatewaySite",
                "dependsOn": [
                    "[resourceId('Microsoft.AppService/gateways',parameters('gatewayName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Web/sites',parameters('gatewayName'))]"
                }
            },
            {
                "type": "tokens",
                "apiVersion": "2015-03-01-preview",
                "location": "[resourceGroup().location]",
                "name": "[parameters('logicAppName')]",
                "tags": {
                    "displayName": "AuthenticationToken"
                },
                "dependsOn": [
                    "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
                ]
            }
        ],
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('gatewayName'))]"
        ],
        "properties": {
            "host": {
                "resourceName": "[parameters('gatewayName')]"
            }
        }
    }

### App Web per ospitare l'app per le API

    {
        "type": "Microsoft.Web/sites",
        "apiVersion": "2015-04-01",
        "name": "[parameters('apiAppName')]",
        "location": "[resourceGroup().location]",
        "kind": "apiApp",
        "tags": {
            "displayName": "APIAppHost"
        },
        "dependsOn": [
            "[resourceId('Microsoft.AppService/gateways', parameters('gatewayName'))]"
        ],
        "resources": [
            {
                "type": "siteextensions",
                "tags": {
                    "displayName": "APIAppExtension"
                },
                "apiVersion": "2015-04-01",
                "name": "[variables('$packageId')]",
                "dependsOn": [
                    "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
                ],
                "properties": {
                    "type": "WebRoot",
                    "feed_url": "[variables('$nugetFeed')]"
                }
            },
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/apiApp",
                "dependsOn": [
                    "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
                }
            }
        ],
        "properties": {
            "name": "[parameters('apiAppName')]",
            "gatewaySiteName": "[parameters('gatewayName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', parameters('svcPlanName'))]",
            "siteConfig": {
                "appSettings": [
                    {
                        "name": "EMA_MicroserviceId",
                        "value": "[parameters('apiAppName')]"
                    },
                    {
                        "name": "EMA_Secret",
                        "value": "[parameters('gatewayToAPIappSecret')]"
                    },
                    {
                        "name": "EMA_RuntimeUrl",
                        "value": "[concat('https://', reference(resourceId('Microsoft.Web/sites', parameters('gatewayName'))).hostNames[0])]"
                    },
                    {
                        "name": "WEBSITE_START_SCM_ON_SITE_CREATION",
                        "value": "1"
                    }
                ]
            }
        }
    }

### App per le API

    {
        "type": "Microsoft.AppService/apiapps",
        "apiVersion": "2015-03-01-preview",
        "name": "[parameters('apiAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "APIApp"
        },
        "resources": [
            {
                "type": "providers/links",
                "apiVersion": "2015-01-01",
                "name": "Microsoft.Resources/apiAppSite",
                "dependsOn": [
                    "[resourceId('Microsoft.AppService/apiapps', parameters('apiAppName'))]"
                ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
                }
            }
        ],
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), 'Microsoft.ApiApp')]"
        ],
        "properties": {
            "package": {
                "id": "Microsoft.ApiApp"
            },
            "host": {
                "resourceName": "[parameters('apiAppName')]"
            },
            "gateway": {
                "resourceName": "[parameters('gatewayName')]"
            },
            "dependencies": [ ]
        }
    }

### App per la logica

Crea l'app per la logica.

Un'app per la logica richiede di specificare un nome, una località, uno SKU (che punta al piano di servizio app), una definizione e, facoltativamente, i parametri.

Si noti che l'app per la logica usa il token per chiamare il gateway.

    {
        "type": "Microsoft.Logic/workflows",
        "apiVersion": "2015-02-01-preview",
        "name": "[parameters('logicAppName')]",
        "location": "[resourceGroup().location]",
        "tags": {
            "displayName": "LogicApp"
        },
        "dependsOn": [
            "[resourceId('Microsoft.AppService/apiApps', parameters('apiAppName'))]"
        ],
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
                    "token": {
                        "defaultValue": "[reference(resourceId('Microsoft.AppService/gateways/tokens', parameters('gatewayName'), parameters('logicAppName'))).token]",
                        "type": "String",
                        "metadata": {
                            "token": {
                                "name": "token"
                            }
                        }
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
                    "getValues": {
                        "type": "ApiApp",
                        "inputs": {
                            "apiVersion": "2015-01-14",
                            "host": {
                                "id": "[concat(resourceGroup().id, '/providers/Microsoft.AppServices/apiApps/',parameters('apiAppName'))]",
                                "gateway": "[concat('https://', reference(resourceId('Microsoft.Web/sites', parameters('gatewayName'))).hostNames[0])]"
                            },
                            "operation": "Values_Get",
                            "parameters": { },
                            "authentication": {
                                "type": "Raw",
                                "scheme": "Zumo",
                                "parameter": "@parameters('token')"
                            }
                        }
                    }
                },
                "outputs": {
                    "result": {
                        "type": "array",
                        "value": "@body('readValues')"
                    }
                }
            },
            "parameters": { }
        }
    }

## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-logic-app-api-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### Interfaccia della riga di comando di Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-logic-app-api-app-create/azuredeploy.json -g ExampleDeployGroup


 

<!---HONumber=August15_HO6-->