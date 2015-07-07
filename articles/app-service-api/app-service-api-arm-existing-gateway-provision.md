<properties 
	pageTitle="Distribuire un'app per le API con un gateway esistente" 
	description="Usare un modello di Gestione risorse di Azure per distribuire un'app per le API che usa un gateway esistente e piano di servizio app." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="tomfitz"/>

# Eseguire il provisioning di un'app per le API con un gateway esistente

Questo argomento illustra come creare un modello di Gestione risorse di Azure che consente di distribuire un'app per le API di Azure e un gateway esistente. Verrà illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere il [modello di app per le API con gateway esistente](https://github.com/tfitzmac/AppServiceTemplates/blob/master/existing-gateway-existing-plan-new-apiapp.json).

## Elementi distribuiti

In questo modello verrà distribuita un'app per le API associata a un piano di hosting del servizio app esistente e a un gateway esistente.

## Parametri

[AZURE.INCLUDE [app-service-api-deploy-parameters](../../includes/app-service-api-deploy-parameters.md)]

### hostingPlanId

Identificatore del piano di hosting esistente.

    "hostingPlanId": {
      "type": "string"
    }

### hostingPlanSettings

Impostazioni del piano di hosting esistente.

    "hostingPlanSettings": {
      "type": "Object",
      "defaultValue": {
        "hostingEnvironment": ""
      }
    }

## Variables

Questo modello consente di definire una variabile usata durante la distribuzione delle risorse.

    "variables": {
      "packageId": "Microsoft.ApiApp"
    }
    
Questo valore viene usato di seguito come **variables('packageId')**.

## Risorse da distribuire

### App Web per ospitare l'app per le API

Consente di creare un'app Web che ospita l'app per le API.

Si noti che **kind** è impostato su **apiApp** in modo da indicare al portale di Azure che questa app Web ospita un gateway. Il portale nasconde l'app Web nel pannello per la ricerca di app Web. L'app include un'estensione per installare il pacchetto dell'app per le API vuoto predefinito. Viene definito un collegamento tra l'app per le API e l'app Web di hosting. La sezione delle impostazioni dell'app include i valori necessari per l'hosting dell'app per le API.

    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2015-04-01",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
      "kind": "apiApp",
      "resources": [
        {
          "type": "siteextensions",
          "apiVersion": "2015-02-01",
          "name": "[variables('packageId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('apiAppName'))]"
          ],
          "properties": {
            "type": "WebRoot",
            "feed_url": "http://apiapps-preview.nuget.org/api/v2/",
            "version": "0.9.4"
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
        "serverFarmId": "[parameters('hostingPlanId')]",
        "hostingEnvironment": "[parameters('hostingPlanSettings').hostingEnvironment]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "EMA_MicroserviceId",
              "value": "[parameters('apiAppName')]"
            },
            {
              "name": "EMA_Secret",
              "value": "[parameters('apiAppSecret')]"
            },
            {
              "name": "EMA_RuntimeUrl",
              "value": "[concat('https://', parameters('gatewayName'), '.azurewebsites.net')]"
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

Consente di creare l'app per le API.

Si noti che i nomi del gateway e dell'app Web di hosting sono definiti come proprietà nell'app per le API.

    {
      "type": "Microsoft.AppService/apiapps",
      "apiVersion": "2015-03-01-preview",
      "name": "[parameters('apiAppName')]",
      "location": "[parameters('location')]",
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
        "[resourceId('Microsoft.Web/sites/siteextensions', parameters('apiAppName'), variables('packageId'))]"
      ],
      "properties": {
        "package": {
          "id": "[variables('packageId')]"
        },
        "host": {
          "resourceName": "[parameters('apiAppName')]"
        },
        "gateway": {
          "resourceName": "[parameters('gatewayName')]"
        }
      }
    }


## Comandi per eseguire la distribuzione

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json

### Interfaccia della riga di comando di Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/tfitzmac/AppServiceTemplates/master/existing-gateway-existing-plan-new-apiapp.json


 

<!---HONumber=62-->