<properties 
   pageTitle="Comandi di PowerShell comuni per le macchine virtuali | Microsoft Azure"
   description="Comandi di PowerShell comuni utili per iniziare a creare e gestire le macchine virtuali in Azure su Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="06/07/2016"
   ms.author="davidmu" />

# Comandi di PowerShell comuni per la creazione e la gestione di macchine virtuali

Questo articolo illustra alcuni comandi di Azure PowerShell che è possibile usare per creare e gestire macchine virtuali nella sottoscrizione di Azure. Per una guida più dettagliata con parametri e opzioni della riga di comando specifici, è possibile usare **Get-Help** come *comando*.

## Creare risorse con Azure Powershell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Attività | Comando
-------------- | -------------------------
Creare una configurazione di macchina virtuale | $vm = [New-AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) -VMName "nome\_vm" -VMSize "dimensioni\_vm"<BR></BR><BR></BR>La configurazione della macchina virtuale viene usata per definire o aggiornare le impostazioni per la VM. La configurazione viene inizializzata con il nome della macchina virtuale e le rispettive [dimensioni](virtual-machines-windows-sizes.md).
Aggiungere le impostazioni di configurazione | $vm = [Set-AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) -VM $vm -Windows -ComputerName "nome\_computer" -Credential $cred -ProvisionVMAgent -EnableAutoUpdate<BR></BR><BR></BR>Le impostazioni del sistema operativo, incluse le [credenziali](https://technet.microsoft.com/library/hh849815.aspx), vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig.
Aggiungere un'interfaccia di rete | $vm = [Add-AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) -VM $vm -Id $nic.Id<BR></BR><BR></BR>Una macchina virtuale deve avere un'[interfaccia di rete](virtual-machines-windows-ps-create.md) per le comunicazioni in una rete virtuale. È anche possibile usare [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) per recuperare un oggetto di interfaccia di rete esistente.
Specificare un'immagine della piattaforma | $vm = [Set-AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) -VM $vm -PublisherName "nome\_publisher" -Offer "offerta\_publisher" -Skus "sku\_prodotto" -Version "più\_recente"<BR></BR><BR></BR>Le [informazioni sull'immagine](virtual-machines-windows-cli-ps-findimage.md) vengono aggiunte all'oggetto di configurazione creato in precedenza con New-AzureRmVMConfig. L'oggetto restituito da questo comando viene usato solo quando si configura il disco del sistema operativo in modo che usi un'immagine della piattaforma.
Configurare il disco del sistema operativo in modo che usi un'immagine della piattaforma | $vm = [Set-AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) -VM $vm -Name "nome\_disco" -VhdUri "http://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption fromImage<BR></BR><BR></BR>Il nome del disco del sistema operativo e la posizione in cui sarà inserito nella [risorsa di archiviazione](../storage/storage-powershell-guide-full.md) vengono aggiunti all'oggetto di configurazione creato in precedenza.
Configurare il disco del sistema operativo in modo che usi un'immagine generalizzata | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "nome\_disco" -SourceImageUri "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk.{guid}.vhd" -VhdUri "https://mystore1.blob.core.windows.net/vhds/disk\_name.vhd" -CreateOption fromImage -Windows<BR></BR><BR></BR>Il nome del disco del sistema operativo, la posizione dell'immagine di origine e la posizione in cui verrà inserito il disco nella [risorsa di archiviazione](../storage/storage-powershell-guide-full.md) vengono aggiunti all'oggetto di configurazione creato in precedenza.
Configurare il disco del sistema operativo in modo che usi un'immagine specializzata | $vm = Set-AzureRmVMOSDisk -VM $vm -Name "nome\_del\_disco" -VhdUri "http://mystore1.blob.core.windows.net/vhds/" -CreateOption Attach -Windows
Creare una macchina virtuale | [New-AzureRmVM]() -ResourceGroupName "nome\_gruppo\_di\_risorse" -Location "nome\_posizione" -VM $vm<BR></BR><BR></BR>Tutte le risorse vengono create in un [gruppo di risorse](../powershell-azure-resource-manager.md). La macchina virtuale deve essere creata nella stessa [posizione](https://msdn.microsoft.com/library/azure/dn495177.aspx) del gruppo di risorse. Prima di eseguire questo comando, eseguire New-AzureRmVMConfig, Set-AzureRmVMOperatingSystem, Set-AzureRmVMSourceImage, Add-AzureRmVMNetworkInterface e Set-AzureRmVMOSDisk.
Elencare le macchine virtuali in una sottoscrizione| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Elencare le macchine virtuali in un gruppo di risorse | Get-AzureRmVM -ResourceGroupName "nome\_gruppo\_di\_risorse"<BR></BR><BR></BR>Per ottenere un elenco di gruppi di risorse disponibili nella sottoscrizione, usare [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Visualizzare informazioni su una macchina virtuale | Get-AzureRmVM -ResourceGroupName "nome\_gruppo\_di\_risorse" -Name "nome\_vm"
Avviare una macchina virtuale | [Start-AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -Name "nome\_vm"
Arrestare una macchina virtuale | [Stop-AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -Name "nome\_vm"
Riavviare una macchina virtuale in esecuzione | [Restart-AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -Name "nome\_vm"
Eliminare una macchina virtuale | [Remove-AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -Name "nome\_vm"
Generalizzare una macchina virtuale | [Set-AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) -ResourceGroupName YourResourceGroup -Name "nome\_vm" -Generalized<BR></BR><BR></BR>È necessario eseguire questo comando prima di eseguire Save-AzureRmVMImage.
Acquisire una macchina virtuale | [Save-AzureRmVMImage](https://msdn.microsoft.com/library/mt619423.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -VMName "nome\_vm" -DestinationContainerName "contenitore\_immagine" -VHDNamePrefix "prefisso\_nome\_immagine" -Path "C:\\percorsofile\\nomefile.json"<BR></BR><BR></BR>Una macchina virtuale deve essere [arrestata e generalizzata](virtual-machines-windows-capture-image.md) per potere essere usata per creare un'immagine. Prima di eseguire questo comando, eseguire Set-AzureRmVm.
Aggiornare una macchina virtuale | [Update-AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -VM $vm<BR></BR><BR></BR>Ottenere la configurazione della macchina virtuale usando Get-AzureRmVM, cambiare le impostazioni di configurazione nell'oggetto VM e quindi eseguire questo comando.
Aggiungere un disco dati a una macchina virtuale | [Add-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) -VM $vm -Name "nome\_disco" -VhdUri "https://mystore1.blob.core.windows.net/vhds/nome\_disco.vhd" -LUN # -Caching ReadWrite -DiskSizeinGB # -CreateOption Empty<BR></BR><BR></BR>Usare Get-AzureRmVM per ottenere l'oggetto VM. Specificare il numero LUN e le dimensioni del disco. Eseguire Update-AzureRmVM per applicare le modifiche della configurazione alla macchina virtuale. Il disco aggiunto non viene inizializzato. A questo scopo, vedere [Gestire macchine virtuali di Azure con Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).
Rimuovere un disco dati da una macchina virtuale | [Remove-AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) -VM $vm -Name "nome\_disco"<BR></BR><BR></BR>Usare Get-AzureRmVM per ottenere l'oggetto VM. Eseguire Update-AzureRmVM per applicare le modifiche della configurazione alla macchina virtuale.
Aggiungere un'estensione a una macchina virtuale | [Set-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -Location "posizione\_azure" -VMName "nome\_vm" -Name "nome\_estensione" -Publisher "nome\_publisher" -Type "tipo\_estensione" -TypeHandlerVersion "#.#" -Settings $Settings -ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Eseguire questo comando con le [informazioni di configurazione ](virtual-machines-windows-extensions-configuration-samples.md) appropriate per l'estensione da installare.
Rimuovere un'estensione di macchina virtuale | [Remove-AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) -ResourceGroupName "nome\_gruppo\_di\_risorse" -Name "nome\_estensione" -VMName "nome\_vm"

## Passaggi successivi

- Per informazioni sulle procedure di base per creare una macchina virtuale, vedere [Creare una VM Windows con Resource Manager e PowerShell](virtual-machines-windows-ps-create.md).

<!---HONumber=AcomDC_0615_2016-->