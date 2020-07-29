---
title: Creare un hub IoT di Azure con un modello (PowerShell) | Documentazione Microsoft
description: Come usare un modello di Azure Resource Manager per creare un hub Internet delle cose con Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75976614"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Creare un hub IoT usando un modello di Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Informazioni su come usare un modello di Azure Resource Manager per creare un hub e un gruppo di consumer. I modelli di Resource Manager sono file JSON che definiscono le risorse che è necessario distribuire per la soluzione. Per ulteriori informazioni sullo sviluppo di modelli di Gestione risorse, vedere [Azure Resource Manager documentazione](https://docs.microsoft.com/azure/azure-resource-manager/).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Il modello di Gestione risorse usato in questa Guida introduttiva è da [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Di seguito è riportata una copia del modello:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Il modello crea un hub Azure Internet con tre endpoint (eventhub, da cloud a dispositivo e messaggistica) e un gruppo di consumer. Per altri esempi di modelli, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Lo schema del modello dell'hub Internet è disponibile [qui](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Sono disponibili diversi metodi per la distribuzione di un modello.  In questa esercitazione si usa Azure PowerShell.

Per eseguire lo script di PowerShell, selezionare **prova** per aprire Azure cloud Shell. Per incollare lo script, fare clic con il pulsante destro del mouse sulla shell, quindi scegliere Incolla:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Come si può notare dallo script di PowerShell, il modello usato è da modelli di avvio rapido di Azure. Per usare il proprio, è necessario innanzitutto caricare il file di modello in cloud Shell e quindi usare l' `-TemplateFile` opzione per specificare il nome del file.  Per un esempio, vedere [distribuire il modello](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Passaggi successivi

Ora che è stato distribuito un hub Internet delle cose usando un modello di Azure Resource Manager, può essere utile esplorare ulteriormente:

* Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].
* Per altre informazioni sulle funzionalità di Azure Resource Manager, vedere la [Panoramica di Azure Resource Manager][lnk-azure-rm-overview].
* Per la sintassi e le proprietà JSON da usare nei modelli, vedere i [tipi di risorsa Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
