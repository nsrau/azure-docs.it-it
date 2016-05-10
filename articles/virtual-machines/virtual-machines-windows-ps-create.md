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
	ms.date="05/02/2016"
	ms.author="davidmu"/>

# Creare una VM Windows con Resource Manager e PowerShell

Questo articolo illustra come creare rapidamente una macchina virtuale di Azure che esegue Windows Server e le risorse necessarie usando [Resource Manager](../resource-group-overview.md) e PowerShell.

Tutti i passaggi in questo articolo sono necessari per creare una macchina virtuale e l'esecuzione di queste operazioni richiede circa 30 minuti.

## Passaggio 1: installare Azure PowerShell

Per informazioni su come installare la versione più recente di Azure PowerShell, selezionare la sottoscrizione che si vuole usare e accedere all'account Azure, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).
        
## Passaggio 2: Creare un gruppo di risorse

Per prima cosa, creare un gruppo di risorse.

1. Ottenere un elenco di località disponibili in cui possono essere create le risorse.

	    Get-AzureLocation | sort Name | Select Name
        
    Verrà visualizzata una schermata analoga alla seguente:
    
        Name
        ----
        Australia East
        Australia Southeast
        Brazil South
        Central India
        Central US
        East Asia
        East US
        East US 2
        Japan East
        Japan West
        North Central US
        North Europe
        South Central US
        South India
        Southeast Asia
        West Europe
        West India
        West US

2. Sostituire il valore di **$locName** con una località presente dell'elenco. Creare la variabile.

        $locName = "Central US"
        
3. Sostituire il valore di **$rgName** con un nome per il nuovo gruppo di risorse. Creare la variabile e il gruppo di risorse.

        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Passaggio 3: Creare un account di archiviazione

È necessario un [account di archiviazione](../storage/storage-introduction.md) per archiviare il disco rigido virtuale usato dalla macchina virtuale creata.

1. Sostituire il valore di **$stName** con un nome per l'account di archiviazione. Testare l'univocità del nome.

        $stName = "mystorage1"
        Test-AzureName -Storage $stName

    Se questo comando restituisce **False**, il nome proposto è univoco in Azure. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
    
2. Ora eseguire il comando per creare l'account di archiviazione.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Passaggio 4: Creare una rete virtuale

Tutte le macchine virtuali fanno parte di una [rete virtuale](../virtual-network/virtual-networks-overview.md).

1. Sostituire il valore di **$subnetName** con un nome per la subnet. Creare la variabile e la subnet.
    	
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Sostituire il valore di **$vnetName** con un nome per la rete virtuale. Creare la variabile e la rete virtuale con la subnet.

        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
    È consigliabile usare valori significativi per l'applicazione e l'ambiente.
        
## Passaggio 5: Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Sostituire il valore di **$ipName** con un nome per l'indirizzo IP pubblico. Creare la variabile e l'indirizzo IP pubblico.

        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Sostituire il valore di **$nicName** con un nome per l'interfaccia di rete. Creare la variabile e l'interfaccia di rete.

        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Passaggio 6: Creare una macchina virtuale

Ora che è tutto pronto, è possibile creare la macchina virtuale.

1. Eseguire il comando per impostare il nome account e la password amministratore per la macchina virtuale.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
    La password deve avere una lunghezza compresa tra 8 e 123 caratteri e includere almeno 3 degli elementi seguenti: una lettera minuscola, una lettera maiuscola, un numero e un carattere speciale.
        
2. Sostituire il valore di **$vmName** con un nome per la macchina virtuale. Creare la variabile e la configurazione della macchina virtuale.

        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Per un elenco di dimensioni disponibili per una macchina virtuale, vedere [Dimensioni delle macchine virtuali in Azure](virtual-machines-windows-sizes.md).
    
3. Sostituire il valore di **$compName** con un nome computer per la macchina virtuale. Creare la variabile e aggiungere le informazioni sul sistema operativo alla configurazione.

        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Definire l'immagine da usare per effettuare il provisioning della macchina virtuale.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Per altre informazioni sulla selezione delle immagini di VM Windows da usare, vedere [Esplorare e selezionare immagini di macchine virtuali Windows in Azure con PowerShell o l'interfaccia della riga di comando](virtual-machines-windows-cli-ps-findimage.md).
        
5. Aggiungere l'interfaccia di rete creata alla configurazione.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Sostituire il valore di **$blobPath** con un percorso e un nome file nella risorsa di archiviazione, che saranno usati dal disco rigido virtuale. Il file del disco rigido virtuale è in genere archiviato in un contenitore, ad esempio **vhds/WindowsVMosDisk.vhd**. Creare le variabili.

        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Sostituire il valore di **$diskName** con un nome per il disco del sistema operativo. Creare la variabile e aggiungere le informazioni sul disco alla configurazione.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Creare infine la macchina virtuale.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Nel portale di Azure verranno visualizzati il gruppo di risorse e tutte le risorse e nella finestra di PowerShell uno stato di esito positivo:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Passaggi successivi

- Se si sono verificati problemi con la distribuzione, è consigliabile vedere [Risoluzione dei problemi relativi alle distribuzioni di gruppi di risorse con il portale di Azure](../resource-manager-troubleshoot-deployments-portal.md).
- Per informazioni su come gestire la macchina virtuale appena creata, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e PowerShell](virtual-machines-windows-ps-manage.md).
- Per usare un modello per creare una macchina virtuale, vedere le informazioni contenute nell'articolo [Creare una macchina virtuale Windows con un modello di Resource Manager](virtual-machines-windows-ps-template.md)

<!---HONumber=AcomDC_0504_2016-->