---
title: Comandi di PowerShell comuni per le macchine virtuali di Azure | Microsoft Docs
description: Comandi di PowerShell comuni utili per iniziare a creare e gestire le macchine virtuali Windows in Azure.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: ba3839a2-f3d5-4e19-a5de-95bfb1c0e61e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/17/2017
ms.author: davidmu
ms.openlocfilehash: 7744f0bef5969a41130c09635d608df1a2952993
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="common-powershell-commands-for-creating-and-managing-azure-virtual-machines"></a>Comandi di PowerShell comuni per la creazione e la gestione di macchine virtuali di Azure

Questo articolo illustra alcuni comandi di Azure PowerShell che è possibile usare per creare e gestire macchine virtuali nella sottoscrizione di Azure.  Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare **Get-Help** come *comando*.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

Queste variabili potrebbero essere utili se si esegue più di uno tra i comandi indicati nell'articolo:

- $location: la posizione della macchina virtuale. È possibile usare [Get-AzureRmLocation](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermlocation) per trovare un'[area geografica](https://azure.microsoft.com/regions/) appropriata.
- $myResourceGroup: nome del gruppo di risorse che contiene la macchina virtuale.
- $myVM: il nome della macchina virtuale.

## <a name="create-a-vm"></a>Creare una macchina virtuale

| Attività | comando |
| ---- | ------- |
| Creare una configurazione di macchina virtuale |$vm = [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig) -VMName $myVM -VMSize "Standard_D1_v1"<BR></BR><BR></BR>La configurazione della macchina virtuale viene usata per definire o aggiornare le impostazioni per la VM. La configurazione viene inizializzata con il nome della VM e le rispettive [dimensioni](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Aggiungere le impostazioni di configurazione |$vm = [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) -VM $vm -Windows -ComputerName $myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Le impostazioni del sistema operativo, incluse le [credenziali](https://technet.microsoft.com/library/hh849815.aspx), vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. |
| Aggiungere un'interfaccia di rete |$vm = [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.5.0/Add-AzureRmVMNetworkInterface) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una VM deve avere un'[interfaccia di rete](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per le comunicazioni in una rete virtuale. È anche possibile usare [Get-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) per recuperare un oggetto di interfaccia di rete esistente. |
| Specificare un'immagine della piattaforma |$vm = [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage) -VM $vm -PublisherName "nome_publisher" -Offer "offerta_publisher" -Skus "sku_prodotto" -Version "più_recente"<BR></BR><BR></BR>Le [informazioni sull'immagine](cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. L'oggetto restituito da questo comando viene usato solo quando si configura il disco del sistema operativo in modo che usi un'immagine della piattaforma. |
| Configurare il disco del sistema operativo in modo che usi un'immagine della piattaforma |$vm = [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk) -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/myOSDisk.vhd" -CreateOption FromImage<BR></BR><BR></BR>Il nome del disco del sistema operativo e la posizione in cui verrà inserito nella [risorsa di archiviazione](../../storage/common/storage-powershell-guide-full.md) vengono aggiunti all'oggetto di configurazione creato in precedenza. |
| Configurare il disco del sistema operativo in modo che usi un'immagine generalizzata |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Il nome del disco del sistema operativo, la posizione dell'immagine di origine e la posizione in cui verrà inserito il disco nella [risorsa di archiviazione](../../storage/common/storage-powershell-guide-full.md) vengono aggiunti all'oggetto di configurazione. |
| Configurare il disco del sistema operativo in modo che usi un'immagine specializzata |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "myOSDisk" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Creare una macchina virtuale |[New-AzureRmVM]() -ResourceGroupName $myResourceGroup -Location $location -VM $vm<BR></BR><BR></BR>Tutte le risorse vengono create in un [gruppo di risorse](../../azure-resource-manager/powershell-azure-resource-manager.md). Prima di eseguire questo comando, eseguire New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk. |

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
| Generalizzare una macchina virtuale |[Set-AzureRmVm](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvm) -ResourceGroupName $myResourceGroup -Name $myVM -Generalized<BR></BR><BR></BR>È necessario eseguire questo comando prima di eseguire Save-AzureRmVMImage. |
| Acquisire una macchina virtuale |[Save-AzureRmVMImage](https://docs.microsoft.com/powershell/module/azurerm.compute/save-azurermvmimage) -ResourceGroupName $myResourceGroup -VMName $myVM -DestinationContainerName "myImageContainer" -VHDNamePrefix "myImagePrefix" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Una macchina virtuale deve essere [preparata, arrestata e generalizzata](prepare-for-upload-vhd-image.md) per potere essere usata per creare un'immagine. Prima di eseguire questo comando, eseguire Set-AzureRmVm. |
| Aggiornare una macchina virtuale |[Update-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvm) -ResourceGroupName $myResourceGroup -VM $vm<BR></BR><BR></BR>Ottenere la configurazione della macchina virtuale usando Get-AzureRmVM, cambiare le impostazioni di configurazione nell'oggetto VM e quindi eseguire questo comando. |
| Aggiungere un disco dati a una macchina virtuale |[Add-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmdatadisk) -VM $vm -Name "myDataDisk" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Usare Get-AzureRmVM per ottenere l'oggetto VM. Specificare il numero LUN e le dimensioni del disco. Eseguire Update-AzureRmVM per applicare le modifiche della configurazione alla macchina virtuale. Il disco aggiunto non viene inizializzato. |
| Rimuovere un disco dati da una macchina virtuale |[Remove-AzureRmVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmdatadisk) -VM $vm -Name "myDataDisk"<BR></BR><BR></BR>Usare Get-AzureRmVM per ottenere l'oggetto VM. Eseguire Update-AzureRmVM per applicare le modifiche della configurazione alla macchina virtuale. |
| Aggiungere un'estensione a una macchina virtuale |[Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) -ResourceGroupName $myResourceGroup -Location $location -VMName $myVM -Name "extensionName" -Publisher "publisherName" -Type "extensionType" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Eseguire questo comando con le [informazioni di configurazione](template-description.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#extensions) appropriate per l'estensione da installare. |
| Rimuovere un'estensione di macchina virtuale |[Remove-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/remove-azurermvmextension) -ResourceGroupName $myResourceGroup -Name "extensionName" -VMName $myVM |

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle procedure di base per creare una macchina virtuale, vedere [Creare una VM Windows con Resource Manager e PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

