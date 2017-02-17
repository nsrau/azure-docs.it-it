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
ms.date: 07/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: f6537e4ebac76b9f3328223ee30647885ee15d3e
ms.openlocfilehash: c118f9ebb8da496ad33b6e24e80d16ccf6557928


---
# <a name="about-images-for-windows-virtual-machines"></a>Informazioni sulle immagini per le macchine virtuali Windows
> [!IMPORTANT] 
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md). Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti. Per informazioni sulla ricerca e l'uso delle immagini con il modello di Resource Manager, vedere [qui](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [virtual-machines-common-classic-about-images](../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>Utilizzo di immagini
È possibile utilizzare il modulo Azure PowerShell per gestire le immagini disponibili per la sottoscrizione di Azure. È inoltre possibile utilizzare il portale di Azure classico per alcune attività di immagine, ma la riga di comando offre ulteriori opzioni.

* **Ottenere tutte le immagini**: `Get-AzureVMImage` restituisce un elenco di tutte le immagini disponibili nella sottoscrizione corrente, le immagini dell'utente, nonché quelle fornite da Azure o dai partner. Ciò significa che è possibile ottenere un elenco di grandi dimensioni. Negli esempi successivi viene illustrato come ottenere un elenco più breve.
* **Ottenere famiglie di immagini**: `Get-AzureVMImage | select ImageFamily` restituisce un elenco delle famiglie di immagini visualizzando la proprietà **ImageFamily** delle stringhe.
* **Ottenere tutte le immagini in una famiglia specifica**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **Trovare immagini VM**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` funziona filtrando la proprietà DataDiskConfiguration, che si applica solo alle immagini di macchina virtuale. In questo esempio viene inoltre Filtra l'output solo il nome di etichetta e l'immagine.
* **Salvare un'immagine generalizzata**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **Salvare un'immagine specializzata**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`
  >[Azure.Tip] Il parametro OSState è obbligatorio se si desidera creare un'immagine di macchina virtuale, che include dischi dati, nonché il disco del sistema operativo. Se non si utilizza il parametro, il cmdlet crea un'immagine del sistema operativo. Il valore del parametro indica se l'immagine è generalizzata o specializzata, basati su se il disco del sistema operativo è stato preparato per il riutilizzo.
* **Eliminare un'immagine**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>Passaggi successivi
È anche possibile [creare una macchina Windows mediante il portale classico](virtual-machines-windows-classic-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)




<!--HONumber=Dec16_HO1-->


