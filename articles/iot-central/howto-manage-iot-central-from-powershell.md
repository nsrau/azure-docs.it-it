---
title: Gestire IoT Central da Azure PowerShell | Microsoft Docs
description: Gestire IoT Central da Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: bfda4f4ebe8990a16853bdfe40065a7501de5275
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70086898"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gestire IoT Central da Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire applicazioni IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , è possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per gestire le applicazioni.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si preferisce eseguire Azure PowerShell nel computer locale, vedere [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Installare il modulo Azure PowerShell). Quando si esegue Azure PowerShell in locale, usare il cmdlet **Connect-AzAccount** per accedere ad Azure prima di eseguire i cmdlet illustrati in questo articolo.

## <a name="install-the-iot-central-module"></a>Installare il modulo IoT Central

Per verificare che il [modulo IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) sia installato nell'ambiente PowerShell, eseguire il comando seguente:

```powershell
Get-InstalledModule -name Az.I*
```

Se l'elenco dei moduli installati non include **Az.IotCentral**, eseguire il comando seguente:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Creare un'applicazione

Usare il cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) per creare un'applicazione IoT Central nella sottoscrizione di Azure. Esempio:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

Lo script crea prima un gruppo di risorse nell'area Stati Uniti orientali per l'applicazione. La tabella seguente descrive i parametri usati con il comando **New-AzIotCentralApp**:

|Parametro         |Description |
|------------------|------------|
|ResourceGroupName |Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
|Location |Per impostazione predefinita, questo cmdlet usa la località definita per il gruppo di risorse. Attualmente è possibile creare un'applicazione IoT Central nelle aree **Stati Uniti orientali**, **Stati Uniti occidentali**, **Europa settentrionale** ed **Europa occidentale**. |
|NOME              |Nome dell'applicazione nel portale di Azure. |
|Sottodominio         |Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è https://mysubdomain.azureiotcentral.com. |
|Sku               |L'unico valore attualmente disponibile è **S1** (livello standard). Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modello          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente: |
|DisplayName       |Nome dell'applicazione visualizzato nell'interfaccia utente. |

**Modelli di applicazione**

|Nome modello  |Descrizione |
|---------------|------------|
|iotc-default@1.0.0 |Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |
|iotc-demo@1.0.0    |Crea un'applicazione che include un modello di dispositivo già creato per un distributore automatico refrigerato. Usare questo modello per iniziare a esplorare Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono fare pratica con questi tipi di dispositivi. |

## <a name="view-your-iot-central-applications"></a>Visualizzare le applicazioni IoT Central

Usare il cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) per elencare le applicazioni IoT Central e visualizzare i metadati.

## <a name="modify-an-application"></a>Modificare un'applicazione

Usare il cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) per aggiornare i metadati di un'applicazione IoT Central. Ad esempio, per modificare il nome visualizzato dell'applicazione creata:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Rimuovere un'applicazione

Usare il cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) per eliminare un'applicazione IoT Central. Esempio:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central in Azure PowerShell, il passaggio successivo consigliato è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
