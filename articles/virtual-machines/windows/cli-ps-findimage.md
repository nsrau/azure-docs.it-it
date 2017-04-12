---
title: Individuare e selezionare le immagini delle macchine virtuali Windows | Microsoft Docs
description: Informazioni su come determinare l&quot;editore, l&quot;offerta e l&quot;SKU per le immagini durante la creazione di una macchina virtuale Windows con il modello di distribuzione di Gestione risorse.
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b36fb2b94dcee94eb299221bbb5c82080d23bfb1
ms.lasthandoff: 03/31/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell
Questo argomento descrive come trovare editori, offerte, SKU e versioni di immagini di VM per ogni posizione di distribuzione. Per fare un esempio, alcune immagini di macchina virtuale Windows usate comunemente sono:

## <a name="table-of-commonly-used-windows-images"></a>Tabella delle immagini Windows usate comunemente
| PublisherName | Offerta | Sku |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008 R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>Trovare immagini di Azure con PowerShell
> [!NOTE]
> Installare e configurare la [versione più recente di Azure PowerShell](/powershell/azureps-cmdlets-docs). Se si usano moduli di Azure PowerShell con versione inferiore a 1.0, si continueranno a usare i comandi seguenti, ma è innanzitutto necessario `Switch-AzureMode AzureResourceManager`. 
> 
> 

Quando si crea una nuova macchina virtuale con Gestione risorse di Azure, in alcuni casi è necessario specificare un'immagine combinando le seguenti proprietà dell'immagine:

* Editore
* Offerta
* SKU

Ad esempio, questi valori sono necessari per il cmdlet di PowerShell `Set-AzureRMVMSourceImage` o con un file dei modelli di gruppi di risorse in cui è necessario specificare il tipo di macchina virtuale da creare.

Se necessario, è possibile esplorare le immagini per determinare i valori nei seguenti modi:

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

Nella visualizzazione del comando `Get-AzureRMVMImageSku` si dispone di tutte le informazioni necessarie per specificare l'immagine per una nuova macchina virtuale.

Di seguito è riportato un esempio completo:

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

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
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

Per l'offerta "WindowsServer":

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

In questo elenco, copiare il nome della SKU scelta per disporre di tutte le informazioni per il cmdlet di PowerShell `Set-AzureRMVMSourceImage` o per un modello di gruppo di risorse.

## <a name="next-steps"></a>Passaggi successivi
A questo punto è possibile scegliere con precisione l'immagine da usare. Per creare rapidamente una macchina virtuale mediante le informazioni di immagine trovate o per usare un modello con tali informazioni di immagine, vedere [Creare una VM Windows con Gestione risorse e PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
