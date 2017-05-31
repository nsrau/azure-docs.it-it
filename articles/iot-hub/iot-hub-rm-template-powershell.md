---
title: Creare un hub IoT di Azure con un modello (PowerShell) | Documentazione Microsoft
description: Come usare un modello di Azure Resource Manager per creare un hub IoT con PowerShell.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/04/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: e7da3c6d4cfad588e8cc6850143112989ff3e481
ms.openlocfilehash: d55de6c3f49abf3ac117dcb265dd7f1bcaa05f24
ms.contentlocale: it-it
ms.lasthandoff: 05/16/2017


---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Creare un hub IoT usando un modello di Azure Resource Manager (PowerShell)
[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione
È possibile utilizzare Gestione risorse di Azure per creare e gestire hub IoT di Azure a livello di codice. In questa esercitazione viene illustrato come usare un modello di Azure Resource Manager per creare un hub IoT con PowerShell.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [modello di distribuzione classica e Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).  In questo articolo viene illustrato l'uso del modello di distribuzione Azure Resource Manager.
> 
> 

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. <br/>Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.

> [!TIP]
> Per altre informazioni su come usare gli script di PowerShell e i modelli di Azure Resource Manager per creare risorse di Azure, vedere [Using Azure PowerShell with Azure Resource Manager][lnk-powershell-arm] (Uso di Azure PowerShell con Azure Resource Manager). 
> 
> 

## <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure
In un prompt dei comandi di PowerShell, immettere il comando seguente per accedere alla sottoscrizione di Azure:

```powershell
Login-AzureRmAccount
```

Se si usano più sottoscrizioni Azure e si esegue l'accesso ad Azure, è possibile accedere a tutte le sottoscrizioni di Azure associate alle credenziali. Usare il comando seguente per elencare gli account Azure che è possibile usare:

```powershell
Get-AzureRMSubscription
```

Usare il comando seguente per selezionare la sottoscrizione che si vuole usare per eseguire i comandi per creare l'hub IoT. È possibile usare il nome o l'ID della sottoscrizione dall'output del comando precedente:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

È possibile usare i comandi seguenti per individuare dove è possibile distribuire un hub IoT e le versioni API attualmente supportate:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Creare un gruppo di risorse per contenere l'hub IoT usando il comando seguente in una delle località supportate per l'hub IoT. In questo esempio viene creato un gruppo di risorse denominato **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-an-azure-resource-manager-template-to-create-an-iot-hub"></a>Inviare un modello Azure Resource Manager per creare un hub IoT
Utilizzare un modello JSON per creare un hub IoT nel gruppo di risorse. È anche possibile usare un modello di Azure Resource Manager per apportare modifiche a un hub IoT esistente.

1. Usare un editor di testo per creare un modello di Azure Resource Manager denominato **template.json** con la definizione di risorsa seguente per creare un nuovo hub IoT standard. In questo esempio l'hub IoT viene aggiunto all'area **Stati Uniti orientali**, vengono creati due gruppi di consumer (**cg1** e **cg2**) sull'endpoint compatibile con Hub eventi e viene usata la versione **2016-02-03** dell'API. Questo modello prevede anche che il nome dell'hub IoT venga passato come un parametro denominato **hubName**. Per un elenco aggiornato delle località in cui è supportato l'hub IoT, vedere lo [Stato di Azure][lnk-status].
   
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```
2. Salvare il file del modello di Azure Resource Manager sul computer locale. Questo esempio presuppone che il file venga salvato in una cartella denominata **c:\templates**.
3. Eseguire il comando seguente per distribuire il nuovo hub IoT, passando il nome dell'hub IoT come parametro. In questo esempio, il nome dell'hub IoT è **abcmyiothub** (notare che questo nome deve essere globalmente univoco, quindi deve includere il nome o le iniziali):
   
    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
4. L'output visualizza le chiavi per l'hub IoT che è stato creato.
5. Per verificare che l'applicazione abbia aggiunto il nuovo hub IoT, è possibile visitare il [portale di Azure][lnk-azure-portal] e visualizzare l'elenco delle risorse oppure usare il cmdlet di PowerShell **Get-AzureRmResource**.

> [!NOTE]
> Questa applicazione di esempio aggiunge un hub IoT Standard S1 che viene addebitato. Al termine è possibile eliminare l'hub IoT usando il [portale di Azure][lnk-azure-portal] o il cmdlet di PowerShell **Remove-AzureRmResource**.
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Dopo aver distribuito un hub IoT usando un modello di Azure Resource Manager con PowerShell, può essere opportuno ottenere informazioni più dettagliate:

* Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].
* Per altre informazioni sulle funzionalità di Azure Resource Manager, vedere la [Panoramica di Azure Resource Manager][lnk-azure-rm-overview].

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md

