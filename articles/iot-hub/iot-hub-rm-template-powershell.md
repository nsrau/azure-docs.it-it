---
title: Creare un hub IoT di Azure con un modello (PowerShell) | Documentazione Microsoft
description: Come usare un modello di Azure Resource Manager per creare un IoT Hub con Azure PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609175"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Creare un hub IoT usando un modello di Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Informazioni su come usare un modello di Azure Resource Manager per creare un IoT Hub e un gruppo di consumer. I modelli di Resource Manager sono file JSON che definiscono le risorse che è necessario distribuire per la soluzione. Per altre informazioni sullo sviluppo di modelli di Resource Manager, vedere [documentazione di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-iot-hub"></a>Creare un hub IoT

Il modello di Resource Manager usato in questa Guida introduttiva proviene [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Di seguito è riportata una copia del modello:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

Il modello crea un hub Iot di Azure con tre endpoint (hub eventi, da cloud a dispositivo e messaggistica) e un gruppo di consumer. Per ulteriori esempi di modelli, vedere [modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Lo schema del modello dell'Iot Hub è reperibile [qui](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Esistono diversi metodi per la distribuzione di un modello.  In questa esercitazione si usa Azure PowerShell.

Per eseguire lo script di PowerShell, selezionare **Provalo** per aprire Azure Cloud shell. Per incollare lo script, la shell e quindi scegliere Incolla:

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

Come si può notare dello script di PowerShell, il modello usato è dai modelli di avvio rapido di Azure. Per usare il proprio, è necessario innanzitutto caricare il file di modello in Cloud shell e quindi usare il `-TemplateFile` per specificare il nome del file.  Per un esempio, vedere [distribuire il modello](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito un hub IoT usando un modello di Azure Resource Manager, è possibile esplorare ulteriormente:

* Informazioni sulle funzionalità dell'[API REST del provider di risorse dell'hub IoT][lnk-rest-api].
* Per altre informazioni sulle funzionalità di Azure Resource Manager, vedere la [Panoramica di Azure Resource Manager][lnk-azure-rm-overview].
* Per la sintassi e le proprietà JSON da usare nei modelli, vedere i [tipi di risorsa Microsoft.Devices](/azure/templates/microsoft.devices/iothub-allversions).

Per altre informazioni sulle attività di sviluppo per l'hub IoT, vedere gli articoli seguenti:

* [Introduzione a C SDK][lnk-c-sdk]
* [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure)

Per altre informazioni sulle funzionalità dell'hub IoT, vedere:

* [Distribuzione dell'intelligenza artificiale in dispositivi perimetrali con Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
