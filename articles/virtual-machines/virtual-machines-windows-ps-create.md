<properties
	pageTitle="Creare una VM di Azure usando PowerShell | Microsoft Azure"
	description="Usare Azure PowerShell e Azure Resource Manager per creare facilmente una nuova VM che esegue Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# Creare una VM Windows con Resource Manager e PowerShell

Questo articolo illustra come creare rapidamente una macchina virtuale di Azure che esegue Windows Server e le risorse associate usando Resource Manager e PowerShell.

L'esecuzione della procedura illustrata in questo articolo richiede circa 30 minuti.

## Passaggio 1: installare Azure PowerShell

Vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md) per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure.
        
## Passaggio 2: Creare un gruppo di risorse

Tutte le risorse devono essere distribuite in un gruppo di risorse. Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../resource-group-overview.md).

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.

	    Get-AzureLocation | sort Name | Select Name

2. Sostituire il valore di **$locName** con una località dell'elenco, ad esempio **Stati Uniti centrali**. Creare la variabile.

        $locName = "location name"
        
3. Sostituire il valore di **$rgName** con il nome del nuovo gruppo di risorse. Creare la variabile e il gruppo di risorse.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Passaggio 3: Creare un account di archiviazione

È necessario un account di archiviazione per archiviare il disco rigido virtuale associato alla macchina virtuale creata.

1. Sostituire il valore di **$stName** (solo lettere minuscole e numeri) con il nome dell'account di archiviazione. Testare l'univocità del nome.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Se questo comando restituisce **False**, il nome proposto è univoco.
    
2. Ora eseguire il comando per creare l'account di archiviazione.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Passaggio 4: Creare una rete virtuale

Tutte le macchine virtuali devono essere associate a una rete virtuale.

1. Sostituire il valore di **$subnetName** con il nome della subnet. Creare la variabile e la subnet.
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Sostituire il valore di **$vnetName** con il nome della rete virtuale. Creare la variabile e la rete virtuale con la subnet.

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## Passaggio 5: Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un indirizzo IP pubblico e un'interfaccia di rete.

1. Sostituire il valore di **$ipName** con il nome dell'indirizzo IP pubblico. Creare la variabile e l'indirizzo IP pubblico.

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Sostituire il valore di **$nicName** con il nome dell'interfaccia di rete. Creare la variabile e l'interfaccia di rete.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Passaggio 6: Creare una macchina virtuale

Ora che è tutto pronto, è possibile creare la macchina virtuale.

1. Eseguire il comando per impostare il nome account e la password amministratore per la macchina virtuale.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. Sostituire il valore di **$vmName** con il nome della macchina virtuale. Creare la variabile e la configurazione della macchina virtuale.

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-windows-sizes.md) per un elenco di dimensioni disponibili per una macchina virtuale.
    
3. Sostituire il valore di **$compName** con il nome computer della macchina virtuale. Creare la variabile e aggiungere le informazioni sul sistema operativo alla configurazione.

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Definire l'immagine da usare per effettuare il provisioning della macchina virtuale.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Vedere [Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando](virtual-machines-windows-cli-ps-findimage.md) per altre informazioni sulla selezione delle immagini da usare.
        
5. Aggiungere l'interfaccia di rete creata alla configurazione.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Sostituire il valore di **$blobPath** con il percorso e il nome file nella risorsa di archiviazione del disco rigido virtuale. Il file VHD è in genere archiviato in un contenitore, ad esempio "vhds/WindowsVMosDisk.vhd". Creare le variabili.

        $blobPath = "vhd path and file name"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Sostituire il valore di **$diskName** con il nome del disco del sistema operativo. Creare la variabile e aggiungere le informazioni sul disco alla configurazione.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Creare infine la macchina virtuale.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Nel portale di Azure verranno visualizzati il gruppo di risorse e tutte le risorse e nella finestra di PowerShell uno stato di esito positivo:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Passaggi successivi

- Se si sono verificati problemi con la distribuzione, è consigliabile leggere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Per informazioni su come gestire la macchina virtuale appena creata, vedere [Gestire le macchine virtuali usando Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).
- Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute in [Creare una macchina virtuale Windows con un modello di Resource Manager](virtual-machines-windows-ps-template.md)

<!---HONumber=AcomDC_0420_2016-->