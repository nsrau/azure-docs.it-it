---
title: Comandi di PowerShell comuni per le macchine virtuali di Azure | Microsoft Docs
description: Comandi di PowerShell comuni utili per iniziare a creare e gestire le macchine virtuali Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 9d0e27c2427d53554b454e0c319ce9cf180f1633
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820791"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandi di PowerShell comuni per la creazione e la gestione di macchine virtuali di Azure

Questo articolo illustra alcuni comandi di Azure PowerShell che è possibile usare per creare e gestire macchine virtuali nella sottoscrizione di Azure.  Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare il *comando* **Get-Help**.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

Queste variabili potrebbero essere utili se si esegue più di uno tra i comandi indicati nell'articolo:

- $location: la posizione della macchina virtuale. È possibile usare [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) per trovare un'[area geografica](https://azure.microsoft.com/regions/) appropriata.
- $myResourceGroup: nome del gruppo di risorse che contiene la macchina virtuale.
- $myVM: il nome della macchina virtuale.

## <a name="create-a-vm---simplified"></a>Creare una macchina virtuale semplificata

| Attività | Comando |
| ---- | ------- |
| Creare una macchina virtuale semplice | [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -Name $myVM <BR></BR><BR></BR> New-AzVM include un set di parametri *semplificati* che richiedono un solo nome. Il valore di -Name viene usato come nome di tutte le risorse necessarie per la creazione di una macchina virtuale. Sebbene sia possibile specificare altri elementi, l'unico elemento obbligatorio è il nome.|
| Creare una VM da un'immagine personalizzata | New-AzVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "ImmagineUtente" -Location $location  <BR></BR><BR></BR>È necessario aver già creato la propria [immagine gestita](capture-image-resource.md). È possibile usare un'immagine per creare più macchine virtuali identiche. |



## <a name="create-a-vm-configuration"></a>Creare una configurazione di macchina virtuale

| Attività | comando |
| ---- | ------- |
| Creare una configurazione di macchina virtuale |$vm = [New-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>La configurazione della macchina virtuale viene usata per definire o aggiornare le impostazioni per la VM. La configurazione viene inizializzata con il nome della VM e le rispettive [dimensioni](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Aggiungere le impostazioni di configurazione |$vm = [Set-AzVMOperatingSystem](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Le impostazioni del sistema operativo, incluse le [credenziali](https://technet.microsoft.com/library/hh849815.aspx), vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzVMConfig. |
| Aggiungere un'interfaccia di rete |$vm = [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/Add-AzVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una VM deve avere un'[interfaccia di rete](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per le comunicazioni in una rete virtuale. È anche possibile usare [Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) per recuperare un oggetto di interfaccia di rete esistente. |
| Specificare un'immagine della piattaforma |$vm = [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) -VM $vm -PublisherName "nome_publisher" -Offer "offerta_publisher" -Skus "sku_prodotto" -Version "più_recente"<BR></BR><BR></BR>Le [informazioni sull'immagine](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzVMConfig. L'oggetto restituito da questo comando viene usato solo quando si configura il disco del sistema operativo in modo che usi un'immagine della piattaforma. |
| Creare una macchina virtuale |[New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Tutte le risorse vengono create in un [gruppo di risorse](../../azure-resource-manager/manage-resource-groups-powershell.md). Prima di questo comando, eseguire New-AzVMConfig, Set-AzVMOperatingSystem, Set-AzVMSourceImage, Add-AzVMNetworkInterface e Set-AzVMOSDisk. |
| Aggiornare una macchina virtuale |[Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Ottenere la configurazione della macchina virtuale usando Get-AzVM, cambiare le impostazioni di configurazione nell'oggetto della macchina virtuale e quindi eseguire questo comando. |

## <a name="get-information-about-vms"></a>Visualizzare le informazioni sulle VM

| Attività | Comando |
| ---- | ------- |
| Elencare le macchine virtuali in una sottoscrizione |[Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) |
| Elencare le macchine virtuali in un gruppo di risorse |Get-AzVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Per ottenere un elenco di gruppi di risorse disponibili nella sottoscrizione, usare [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup). |
| Visualizzare informazioni su una macchina virtuale |Get-AzVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gestire le macchine virtuali
| Attività | Comando |
| --- | --- |
| Avviare una macchina virtuale |[Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Arrestare una macchina virtuale |[Stop-AzVM](https://docs.microsoft.com/powershell/module/az.compute/stop-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Riavviare una macchina virtuale in esecuzione |[Restart-AzVM](https://docs.microsoft.com/powershell/module/az.compute/restart-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Eliminare una macchina virtuale |[Remove-AzVM](https://docs.microsoft.com/powershell/module/az.compute/remove-azvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle procedure di base per creare una macchina virtuale, vedere [Creare una VM Windows con Resource Manager e PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

