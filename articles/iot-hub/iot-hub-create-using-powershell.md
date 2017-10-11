---
title: Creare un hub IoT di Azure con un cmdlet di PowerShell | Microsoft Docs
description: Procedura per usare un cmdlet di PowerShell per creare un hub IoT.
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 02227adeb8a9a7463506efa44ddc2977f8aae65a
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Creare un hub IoT usando il cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile usare i cmdlet di Azure PowerShell per creare e gestire hub IoT di Azure. In questa esercitazione viene illustrato come creare un hub IoT con PowerShell.

> [!NOTE]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Azure Resource Manager e classico](../azure-resource-manager/resource-manager-deployment-model.md). In questo articolo viene illustrato l'uso del modello di distribuzione Azure Resource Manager.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. <br/>Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Cmdlet di Azure PowerShell][lnk-powershell-install].

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

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Per la distribuzione di un hub IoT è necessario un gruppo di risorse. È possibile usare un gruppo di risorse esistente o crearne uno nuovo.

È possibile usare il comando seguente per individuare le località in cui è possibile distribuire un hub IoT:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Per creare un gruppo di risorse per l'hub IoT in una delle località supportate per l'hub IoT usare il comando seguente. In questo esempio viene creato un gruppo di risorse denominato **MyIoTRG1** nell'area degli **Stati Uniti orientali**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Per creare un hub IoT nel gruppo di risorse creato nel passaggio precedente, usare il comando seguente. In questo esempio viene creato un hub **S1** denominato **MyTestIoTHub** nell'area degli **Stati Uniti orientali**:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Il nome dell'hub IoT deve essere univoco.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


È possibile elencare tutti gli hub IoT nella sottoscrizione con il comando seguente:

```powershell
Get-AzureRmIotHub
```

Nell'esempio precedente viene aggiunto un hub IoT Standard S1 che viene addebitato. È possibile eliminare l'hub IoT con il comando seguente:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

In alternativa, è possibile rimuovere un gruppo di risorse e tutte le risorse che contiene con il comando seguente:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT mediante il cmdlet di PowerShell, può essere opportuno ottenere informazioni più dettagliate:

* Individuare altri [cmdlet di PowerShell da usare con l'hub IoT][lnk-iothub-cmdlets].
* Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Simulazione di un dispositivo con IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
