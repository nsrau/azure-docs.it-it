---
title: Selezionare immagini di VM Windows in Azure | Microsoft Docs
description: Imparare a usare Azure PowerSHell per determinare l'editore, l'offerta, lo SKU e la versione per le immagini di VM del Marketplace.
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Come trovare immagini di VM Windows in Azure Marketplace con Azure PowerShell

Questo argomento descrive come usare Azure PowerShell per trovare immagini di VM in Azure Marketplace. Usare queste informazioni per specificare un'immagine del Marketplace quando si crea una VM Windows.

Verificare di aver prima installato e configurato il [modulo di Azure PowerShell](/powershell/azure/install-azurerm-ps) più recente.



## <a name="table-of-commonly-used-windows-images"></a>Tabella delle immagini Windows usate comunemente
| PublisherName | Offerta | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008 R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>Trovare immagini specifiche


Quando si crea una nuova macchina virtuale con Gestione risorse di Azure, in alcuni casi è necessario specificare un'immagine combinando le seguenti proprietà dell'immagine:

* Editore
* Offerta
* SKU

Ad esempio, usare questi valori con il cmdlet di PowerShell [Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) o con un modello di gruppo di risorse in cui è necessario specificare il tipo di VM da creare.

Se è necessario determinare questi valori, è possibile eseguire i cmdlet [Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher), [Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) e [Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) per passare alle immagini. Questi valori possono essere determinati:

1. Elencando gli editori di immagini.
2. Elencando le offerte di un determinato editore.
3. Elencando le SKU di una determinata offerta.

In primo luogo, elencare gli editori con i seguenti comandi:

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

Specificare il nome dell'editore prescelto ed eseguire i seguenti comandi:

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Specificare il nome dell'offerta prescelta ed eseguire i seguenti comandi:

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Nell'output del comando `Get-AzureRMVMImageSku` ci sono tutte le informazioni necessarie per specificare l'immagine per una nuova macchina virtuale.

Di seguito è riportato un esempio completo:

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

Output:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

Per l'editore "MicrosoftWindowsServer":

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

Output:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

Per l'offerta "WindowsServer":

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

Output:

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Nano-Server
```

In questo elenco, copiare il nome della SKU scelta per disporre di tutte le informazioni per il cmdlet di PowerShell `Set-AzureRMVMSourceImage` o per un modello di gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi
A questo punto è possibile scegliere con precisione l'immagine da usare. Per creare rapidamente una macchina virtuale usando le informazioni dell'immagine, appena trovate, vedere [Creare una macchina virtuale Windows con PowerShell](quick-create-powershell.md).

