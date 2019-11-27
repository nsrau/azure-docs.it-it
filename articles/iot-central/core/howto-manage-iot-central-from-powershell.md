---
title: Gestire IoT Central da Azure PowerShell | Microsoft Docs
description: Questo articolo descrive come creare e gestire le applicazioni IoT Central da Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480290"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Gestire IoT Central da Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Invece di creare e gestire applicazioni IoT Central nel sito Web di [Azure IOT Central Application Manager](https://aka.ms/iotcentral) , è possibile usare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) per gestire le applicazioni.

## <a name="prerequisites"></a>prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

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

Usare il cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) per creare un'applicazione IoT Central nella sottoscrizione di Azure. Ad esempio:

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

Lo script crea innanzitutto un gruppo di risorse nella località Stati Uniti orientali per l'applicazione. La tabella seguente descrive i parametri usati con il comando **New-AzIotCentralApp**:

|.         |DESCRIZIONE |
|------------------|------------|
|ResourceGroupName |Gruppo di risorse che contiene l'applicazione. Questo gruppo di risorse deve già esistere nella sottoscrizione. |
|Location |Per impostazione predefinita, questo cmdlet usa la località definita per il gruppo di risorse. Attualmente, è possibile creare un'applicazione IoT Central in **Stati Uniti**, **Australia**, **Asia Pacifico**o nelle località dell' **Europa** .  |
|Nome              |Nome dell'applicazione nel portale di Azure. |
|Sottodominio         |Sottodominio nell'URL dell'applicazione. In questo esempio l'URL dell'applicazione è https://mysubdomain.azureiotcentral.com. |
|Sku               |L'unico valore attualmente disponibile è **S1** (livello standard). Vedere [Prezzi di Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Modello          | Modello di applicazione da usare. Per altre informazioni, vedere la tabella seguente: |
|displayName       |Nome dell'applicazione visualizzato nell'interfaccia utente. |

**Modelli di applicazione con funzionalità disponibili a livello generale**

| Nome modello            | DESCRIZIONE |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Crea un'applicazione vuota per l'utente da popolare con i propri modelli di dispositivi e dispositivi. |
| iotc-demo@1.0.0          | Crea un'applicazione che include un modello di dispositivo già creato per un distributore automatico refrigerato. Usare questo modello per iniziare a esplorare Azure IoT Central. |
| iotc-devkit-sample@1.0.0 | Crea un'applicazione con modelli di dispositivo pronti per la connessione a un dispositivo MXChip o Raspberry Pi. Si consiglia di usare questo modello agli sviluppatori di dispositivi che vogliono fare pratica con questi tipi di dispositivi. |


**Modelli di applicazione con funzionalità di anteprima pubblica**

| Nome modello            | DESCRIZIONE |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Consente di creare un'applicazione di anteprima plug and Play vuota da popolare con i propri dispositivi e modelli di dispositivo. |
| iotc-condition@1.0.0     | Crea un'applicazione con un modello di monitoraggio della condizione di analisi in archivio. Usare questo modello per connettersi e monitorare l'ambiente di archiviazione. |
| iotc-consumption@1.0.0   | Crea un'applicazione con il modello di monitoraggio del consumo di acqua. Usare questo modello per monitorare e controllare il flusso di acqua. |
| iotc-distribution@1.0.0  | Crea un'applicazione con un modello di distribuzione digitale. Usare questo modello per migliorare l'efficienza dell'output del warehouse con azioni e azioni chiave digitalizzazione. |
| iotc-inventory@1.0.0     | Crea un'applicazione con un modello di gestione dell'inventario intelligente. Usare questo modello per automatizzare la ricezione, lo spostamento del prodotto, il conteggio del ciclo e il rilevamento dei sensori. |
| iotc-logistics@1.0.0     | Crea un'applicazione con un modello logistico connesso. Usare questo modello per tenere traccia della spedizione in tempo reale in ambienti diversi, ad aria, acqua e terra con monitoraggio della posizione e della condizione. |
| iotc-meter@1.0.0         | Consente di creare un'applicazione con il modello di monitoraggio di Smart Meter. Usare questo modello per monitorare il consumo di energia, lo stato della rete e identificare le tendenze per migliorare il supporto clienti e la gestione intelligente dei contatori.  |
| iotc-patient@1.0.0       | Crea un'applicazione con un modello di monitoraggio paziente continuo. Usare questo modello per estendere la cura del paziente, le riammissioni e la gestione delle patologie. |
| iotc-power@1.0.0         | Crea un'applicazione con il modello di monitoraggio del pannello solare. Usare questo modello per monitorare lo stato del pannello solare, le tendenze di generazione dell'energia. |
| iotc-quality@1.0.0       | Crea un'applicazione con un modello di monitoraggio della qualità dell'acqua. Usare questo modello per monitorare digitalmente la qualità dell'acqua.|
| iotc-store@1.0.0         | Crea un'applicazione con un modello di estrazione di analisi in-Store. Usare questo modello per monitorare e gestire il flusso di estrazione all'interno dell'archivio. |
| iotc-waste@1.0.0         | Crea un'applicazione con un modello di gestione dello spreco connesso. Usare questo modello per monitorare i cassonetti e gli operatori dei campi di invio. |

> [!NOTE]
> I modelli di applicazione di anteprima sono attualmente disponibili solo nelle località **Europa** e **Stati Uniti** .

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

Usare il cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) per eliminare un'applicazione IoT Central. Ad esempio:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come gestire le applicazioni Azure IoT Central in Azure PowerShell, il passaggio successivo consigliato è:

> [!div class="nextstepaction"]
> [Amministrare l'applicazione](howto-administer.md)
