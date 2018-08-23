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
ms.openlocfilehash: cb9f03ab87079ba33135840e3e7599d2e8cc95e9
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/11/2018
ms.locfileid: "42145796"
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandi di PowerShell comuni per la creazione e la gestione di macchine virtuali di Azure

Questo articolo illustra alcuni comandi di Azure PowerShell che è possibile usare per creare e gestire macchine virtuali nella sottoscrizione di Azure.  Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare il *comando* **Get-Help**.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

Queste variabili potrebbero essere utili se si esegue più di uno tra i comandi indicati nell'articolo:

- $location: la posizione della macchina virtuale. È possibile usare [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) per trovare un'[area geografica](https://azure.microsoft.com/regions/) appropriata.
- $myResourceGroup: nome del gruppo di risorse che contiene la macchina virtuale.
- $myVM: il nome della macchina virtuale.

## <a name="create-a-vm---simplified"></a>Creare una macchina virtuale semplificata

| Attività | Comando |
| ---- | ------- |
| Creare una macchina virtuale semplice | [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -Name $myVM <BR></BR><BR></BR> New-AzureRMVM include un set di parametri *semplificati* che richiedono un solo nome. Il valore di -Name viene usato come nome di tutte le risorse necessarie per la creazione di una macchina virtuale. Sebbene sia possibile specificare altri elementi, l'unico elemento obbligatorio è il nome.|
| Creare una VM da un'immagine personalizzata | New-AzureRmVm -ResourceGroupName $myResourceGroup -Name $myVM ImageName "myImage" -Location $location  <BR></BR><BR></BR>È necessario aver già creato la propria [immagine gestita](capture-image-resource.md). È possibile usare un'immagine per creare più macchine virtuali identiche. |



## <a name="create-a-vm-configuration"></a>Creare una configurazione di macchina virtuale

| Attività | comando |
| ---- | ------- |
| Creare una configurazione di macchina virtuale |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>La configurazione della macchina virtuale viene usata per definire o aggiornare le impostazioni per la VM. La configurazione viene inizializzata con il nome della VM e le rispettive [dimensioni](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Aggiungere le impostazioni di configurazione |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Le impostazioni del sistema operativo, incluse le [credenziali](https://technet.microsoft.com/library/hh849815.aspx), vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. |
| Aggiungere un'interfaccia di rete |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una VM deve avere un'[interfaccia di rete](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per le comunicazioni in una rete virtuale. È anche possibile usare [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) per recuperare un oggetto di interfaccia di rete esistente. |
| Specificare un'immagine della piattaforma |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "nome_publisher" -Offer "offerta_publisher" -Skus "sku_prodotto" -Version "più_recente"<BR></BR><BR></BR>Le [informazioni sull'immagine](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. L'oggetto restituito da questo comando viene usato solo quando si configura il disco del sistema operativo in modo che usi un'immagine della piattaforma. |
| Creare una macchina virtuale |[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Tutte le risorse vengono create in un [gruppo di risorse](../../azure-resource-manager/powershell-azure-resource-manager.md). Prima di eseguire questo comando, eseguire New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk. |
| Aggiornare una macchina virtuale |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Ottenere la configurazione della macchina virtuale usando Get-AzureRmVM, cambiare le impostazioni di configurazione nell'oggetto VM e quindi eseguire questo comando. |

## <a name="get-information-about-vms"></a>Visualizzare le informazioni sulle VM

| Attività | Comando |
| ---- | ------- |
| Elencare le macchine virtuali in una sottoscrizione |[Get-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvm) |
| Elencare le macchine virtuali in un gruppo di risorse |Get-AzureRmVM -ResourceGroupName $myResourceGroup<BR></BR><BR></BR>Per ottenere un elenco di gruppi di risorse disponibili nella sottoscrizione, usare [Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup). |
| Visualizzare informazioni su una macchina virtuale |Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM |

## <a name="manage-vms"></a>Gestire le macchine virtuali
| Attività | Comando |
| --- | --- |
| Avviare una macchina virtuale |[Start-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/start-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Arrestare una macchina virtuale |[Stop-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/stop-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Riavviare una macchina virtuale in esecuzione |[Restart-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/restart-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |
| Eliminare una macchina virtuale |[Remove-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM |


## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle procedure di base per creare una macchina virtuale, vedere [Creare una VM Windows con Resource Manager e PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

