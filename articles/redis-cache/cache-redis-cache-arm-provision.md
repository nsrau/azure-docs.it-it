---
title: Eseguire il provisioning di una Cache Redis | Microsoft Docs
description: Utilizzare il modello di Gestione risorse di Azure per distribuire una Cache Redis di Azure.
services: app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b953b3e2d7518a4bf7af05ee08d33dec3d75005b


---
# <a name="create-a-redis-cache-using-a-template"></a>Creare una Cache Redis utilizzando un modello
Questo argomento illustra come creare un modello di Azure Resource Manager che consente di distribuire un'istanza di Cache Redis di Azure. La cache è utilizzabile con un account di archiviazione esistente per mantenere i dati di diagnostica. Verrà anche illustrato come definire le risorse da distribuire e i parametri specificati quando viene eseguita la distribuzione. È possibile usare questo modello per le proprie distribuzioni o personalizzarlo in base alle esigenze.

Le impostazioni di diagnostica sono attualmente condivise da tutte le cache nella stessa area di una sottoscrizione. L'aggiornamento di una cache nell'area ha effetto su tutte le altre cache presenti nell'area.

Per altre informazioni sulla creazione dei modelli, vedere [Creazione di modelli di Gestione risorse di Azure](../resource-group-authoring-templates.md).

Per il modello completo, vedere il [modello di Cache Redis](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Sono disponibili modelli di Resource Manager per il nuovo [livello Premium](cache-premium-tier-intro.md) . 
> 
> * [Creare una Cache Redis Premium con il clustering](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Creare una Cache Redis Premium con persistenza dei dati](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Creare una Cache Redis Premium con rete virtuale e clustering facoltativo](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> Per verificare gli ultimi modelli, vedere [Modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/) e cercare `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Elementi distribuiti
In questo modello, verrà distribuita una Cache Redis di Azure che utilizza un account di archiviazione esistente per dati di diagnostica.

Per eseguire automaticamente la distribuzione, fare clic sul pulsante seguente:

[![Distribuzione in Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parametri
Gestione risorse di Azure permette di definire i parametri per i valori da specificare durante la distribuzione del modello. Il modello include una sezione denominata Parametri che contiene tutti i valori dei parametri.
È necessario definire un parametro per i valori che variano in base al progetto distribuito o all'ambiente in cui viene distribuito il progetto. Non definire i parametri per i valori che rimangono invariati. Ogni valore di parametro nel modello viene usato per definire le risorse distribuite. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Percorso dell'istanza di Cache Redis. Per prestazioni ottimali, usare lo stesso percorso dell'app da usare con la cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
Nome dell'account di archiviazione esistente da utilizzare per le diagnostiche. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Valore booleano che indica se è consentito l'accesso tramite le porte non SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Valore che indica se la diagnostica è abilitata. Utilizzare ON o OFF.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Risorse da distribuire
### <a name="redis-cache"></a>Cache Redis
Crea la Cache Redis di Azure.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Comandi per eseguire la distribuzione
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup





<!--HONumber=Nov16_HO3-->


