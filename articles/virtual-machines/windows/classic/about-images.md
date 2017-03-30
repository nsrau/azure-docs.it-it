---
title: Informazioni sulle immagini per le macchine virtuali Windows | Microsoft Docs
description: Informazioni sull&quot;utilizzo delle immagini con macchine virtuali Windows in Azure.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.lasthandoff: 03/27/2017


---
# <a name="about-images-for-windows-virtual-machines"></a>Informazioni sulle immagini per le macchine virtuali Windows
> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sulla ricerca e l'uso delle immagini con il modello di Resource Manager, vedere [qui](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Utilizzo di immagini

È possibile usare il modulo Azure PowerShell e il portale di Azure per gestire le immagini disponibili per la sottoscrizione di Azure. Nel modulo Azure PowerShell sono disponibili più opzioni di comando, pertanto è possibile individuare esattamente gli elementi da visualizzare o le operazioni da eseguire. Il portale di Azure offre un'interfaccia utente grafica per molte attività di amministrazione quotidiana.

Di seguito sono riportati alcuni esempi che usano il modulo Azure PowerShell.

* **Ottenere tutte le immagini**: `Get-AzureVMImage` restituisce un elenco di tutte le immagini disponibili nella sottoscrizione corrente, ovvero le immagini dell'utente e quelle fornite da Azure o dai partner. L'elenco risultante potrebbe essere di dimensioni elevate. Negli esempi successivi viene illustrato come ottenere un elenco più breve.
* **Ottenere famiglie di immagini**: `Get-AzureVMImage | select ImageFamily` restituisce un elenco delle famiglie di immagini visualizzando la proprietà **ImageFamily** delle stringhe.
* **Ottenere tutte le immagini in una famiglia specifica**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Trovare immagini VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` funziona filtrando la proprietà DataDiskConfiguration, che si applica solo alle immagini di macchina virtuale. In questo esempio viene inoltre Filtra l'output solo il nome di etichetta e l'immagine.
* **Salvare un'immagine generalizzata**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Salvare un'immagine specializzata**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > Il parametro OSState è necessario per creare un'immagine di macchina virtuale, che include il disco del sistema operativo e i dischi dati collegati. Se non si utilizza il parametro, il cmdlet crea un'immagine del sistema operativo. Il valore del parametro indica se l'immagine è generalizzata o specializzata, basati su se il disco del sistema operativo è stato preparato per il riutilizzo.

* **Eliminare un'immagine**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Passaggi successivi
È anche possibile [creare una macchina Windows nel portale di Azure](tutorial.md).

