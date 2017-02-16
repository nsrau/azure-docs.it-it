---
title: Comandi di PowerShell comuni per le macchine virtuali | Microsoft Docs
description: Comandi di PowerShell comuni utili per iniziare a creare e gestire le macchine virtuali in Azure su Windows
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
ms.date: 09/27/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: d86db0f0487c8c3627fa91b656db6a4c1576348c


---
# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandi di PowerShell comuni per la creazione e la gestione di macchine virtuali
Questo articolo illustra alcuni comandi di Azure PowerShell che è possibile usare per creare e gestire macchine virtuali nella sottoscrizione di Azure.  Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare **Get-Help** come *comando*.

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione e accedere all'account, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="create-a-vm"></a>Creare una macchina virtuale
| Attività | comando |
| --- | --- |
| Creare una configurazione di macchina virtuale |$vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "nome_vm" -VMSize "dimensioni_vm"<BR></BR><BR></BR>La configurazione della macchina virtuale viene usata per definire o aggiornare le impostazioni per la VM. La configurazione viene inizializzata con il nome della VM e le rispettive [dimensioni](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Aggiungere le impostazioni di configurazione |$vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "nome_computer" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Le impostazioni del sistema operativo, incluse le [credenziali](https://technet.microsoft.com/library/hh849815.aspx), vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. |
| Aggiungere un'interfaccia di rete |$vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una VM deve avere un'[interfaccia di rete](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per le comunicazioni in una rete virtuale. È anche possibile usare [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) per recuperare un oggetto di interfaccia di rete esistente. |
| Specificare un'immagine della piattaforma |$vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "nome_publisher" -Offer "offerta_publisher" -Skus "sku_prodotto" -Version "più_recente"<BR></BR><BR></BR>Le [informazioni sull'immagine](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. L'oggetto restituito da questo comando viene usato solo quando si configura il disco del sistema operativo in modo che usi un'immagine della piattaforma. |
| Configurare il disco del sistema operativo in modo che usi un'immagine della piattaforma |$vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "nome_disco" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage<BR></BR><BR></BR>Il nome del disco del sistema operativo e la posizione in cui verrà inserito nella [risorsa di archiviazione](../storage/storage-powershell-guide-full.md) vengono aggiunti all'oggetto di configurazione creato in precedenza. |
| Configurare il disco del sistema operativo in modo che usi un'immagine generalizzata |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "nome_disco" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -CreateOption FromImage -Windows<BR></BR><BR></BR>Il nome del disco del sistema operativo, la posizione dell'immagine di origine e la posizione in cui verrà inserito il disco nella [risorsa di archiviazione](../storage/storage-powershell-guide-full.md) vengono aggiunti all'oggetto di configurazione. |
| Configurare il disco del sistema operativo in modo che usi un'immagine specializzata |$vm = Set-AzureRmVMOSDisk -VM $vm -Name "nome_del_disco" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows |
| Creare una macchina virtuale |[New-AzureRmVM]() -ResourceGroupName "nome_gruppo_di_risorse" -Location "nome_posizione" -VM $vm<BR></BR><BR></BR>Tutte le risorse vengono create in un [gruppo di risorse](../powershell-azure-resource-manager.md). La macchina virtuale deve essere creata nella stessa [posizione](https://msdn.microsoft.com/library/azure/dn495177.aspx) del gruppo di risorse. Prima di eseguire questo comando, eseguire New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk. |

## <a name="get-information-about-vms"></a>Visualizzare le informazioni sulle VM
| Attività | Comando |
| --- | --- |
| Elencare le macchine virtuali in una sottoscrizione |[Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx) |
| Elencare le macchine virtuali in un gruppo di risorse |Get-AzureRmVM -ResourceGroupName "nome_gruppo_di_risorse"<BR></BR><BR></BR>Per ottenere un elenco di gruppi di risorse disponibili nella sottoscrizione, usare [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx). |
| Visualizzare informazioni su una macchina virtuale |Get-AzureRmVM -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_vm" |

## <a name="manage-vms"></a>Gestire le macchine virtuali
| Attività | Comando |
| --- | --- |
| Avviare una macchina virtuale |[Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_vm" |
| Arrestare una macchina virtuale |[Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_vm" |
| Riavviare una macchina virtuale in esecuzione |[Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_vm" |
| Eliminare una macchina virtuale |[Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_vm" |
| Generalizzare una macchina virtuale |[Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "nome_vm" -Generalized<BR></BR><BR></BR>È necessario eseguire questo comando prima di eseguire Save-AzureRmVMImage. |
| Acquisire una macchina virtuale |[Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -VMName "nome_vm" -DestinationContainerName "contenitore_immagine" -VHDNamePrefix "prefisso_nome_immagine" -Path "C:\filepath\filename.json"<BR></BR><BR></BR>Una macchina virtuale deve essere [arrestata e generalizzata](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per potere essere usata per creare un'immagine. Prima di eseguire questo comando, eseguire Set-AzureRmVm. |
| Aggiornare una macchina virtuale |[Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -VM $vm<BR></BR><BR></BR>Ottenere la configurazione della macchina virtuale usando Get-AzureRmVM, cambiare le impostazioni di configurazione nell'oggetto VM e quindi eseguire questo comando. |
| Aggiungere un disco dati a una macchina virtuale |[Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "nome_disco" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Usare Get-AzureRmVM per ottenere l'oggetto VM. Specificare il numero LUN e le dimensioni del disco. Eseguire Update-AzureRmVM per applicare le modifiche della configurazione alla macchina virtuale. Il disco aggiunto non viene inizializzato. Per informazioni sull'inizializzazione dei dischi al momento della relativa aggiunta, vedere [Gestire macchine virtuali di Azure con Resource Manager di Azure e PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). |
| Rimuovere un disco dati da una macchina virtuale |[Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "nome_disco"<BR></BR><BR></BR>Usare Get-AzureRmVM per ottenere l'oggetto VM. Eseguire Update-AzureRmVM per applicare le modifiche della configurazione alla macchina virtuale. |
| Aggiungere un'estensione a una macchina virtuale |[Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Location "posizione_azure" -VMName "nome_vm" -Name "nome_estensione" -Publisher "nome_publisher" -Type "tipo_estensione" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Eseguire questo comando con le [informazioni di configurazione](virtual-machines-windows-extensions-configuration-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) appropriate per l'estensione da installare. |
| Rimuovere un'estensione di macchina virtuale |[Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "nome_gruppo_di_risorse" -Name "nome_estensione" -VMName "nome_vm" |

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni sulle procedure di base per creare una macchina virtuale, vedere [Creare una VM Windows con Resource Manager e PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Dec16_HO2-->


