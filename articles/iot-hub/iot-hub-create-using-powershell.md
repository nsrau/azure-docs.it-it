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
ms.date: 03/23/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 5c6ab3a8fdacb43546801518b76a15314d0b4b0f
ms.lasthandoff: 03/21/2017


---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>Creare un hub IoT usando il cmdlet New-AzureRmIotHub

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduzione

È possibile usare i cmdlet di Azure PowerShell per creare e gestire hub IoT di Azure. In questa esercitazione viene illustrato come creare un hub IoT con PowerShell.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [modello di distribuzione classica e Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).  In questo articolo viene illustrato l'uso del modello di distribuzione Azure Resource Manager.

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un account Azure attivo. <br/>Se non si ha un account, è possibile crearne uno [gratuito][lnk-free-trial] in pochi minuti.
* [Azure PowerShell 1.0][lnk-powershell-install] o versione successiva.
* [Cmdlet di Azure Resource Manager][lnk-rm-install].

## <a name="connect-to-your-azure-subscription"></a>Connettersi alla sottoscrizione di Azure
In un prompt dei comandi di PowerShell, immettere il comando seguente per accedere alla sottoscrizione di Azure:

```powershell
Login-AzureRmAccount
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

> [!NOTE]
> Il nome dell'hub IoT deve essere univoco.

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

* [Simulazione di un dispositivo con IoT Gateway SDK][lnk-gateway]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: /powershell/azureps-cmdlets-docs
[lnk-iothub-cmdlets]: /powershell/resourcemanager/azurerm.iothub/v1.3.0/azurerm.iothub
[lnk-rm-install]: /powershell/resourcemanager/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

