<properties 
   pageTitle="Distribuire più macchine virtuali con funzionalità Multi-NIC mediante PowerShell nel modello di distribuzione classica | Microsoft Azure"
   description="Informazioni su come distribuire più macchine virtuali con funzionalità Multi-NIC con PowerShell nel modello di distribuzione classica"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="telmos" />

#Distribuire macchine virtuali con funzionalità Multi-NIC (classiche) tramite PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poiché in questo momento non è possibile disporre di macchine virtuali con una singola scheda di interfaccia di rete e di macchine virtuali con più schede di interfaccia di rete nello stesso servizio cloud, i server back-end verranno implementati in un servizio cloud diverso rispetto a tutti gli altri componenti in uno scenario. La procedura seguente usa un servizio cloud denominato *IaaSStory* per la risorsa principale e *IaaSStory-BackEnd* per i server di back-end.

## Prerequisiti

Prima di distribuire i server back-end, è necessario distribuire il servizio cloud principale con tutte le risorse necessarie per questo scenario. Come minimo, è necessario creare una rete virtuale con una subnet per il back-end. Visitare [Creare una rete virtuale mediante PowerShell](virtual-networks-create-vnet-classic-ps.md) per informazioni su come distribuire una rete virtuale.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Distribuire le macchine virtuali di back-end

Le macchine virtuali di back-end dipendono dalla creazione di risorse elencate di seguito.

- **Subnet Back-end**. I server del database formeranno parte di una subnet distinta, per separare il traffico. Lo script seguente prevede che questa subnet sia presente in una rete virtuale denominata *WTestVnet*.
- **Account di archiviazione per dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzeranno la tecnologia unità SSD, che richiede un account di archiviazione premium. Verificare la posizione di Azure che viene distribuita per supportare l'archiviazione premium.
- **Set di disponibilità**. Tutti i server di database verranno aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione. 

### Passaggio 1 - avviare lo script

È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.ps1). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### Passaggio 2 - creare le risorse necessarie per le macchine virtuali

È necessario creare un nuovo servizio cloud e un account di archiviazione per i dischi di dati per tutte le macchine virtuali. È inoltre necessario specificare un'immagine e un account amministratore locale per le macchine virtuali. Per creare queste risorse, eseguire questi passaggi.

1. Creare un nuovo servizio cloud

		New-AzureService -ServiceName $backendCSName -Location $location

2. Creare un account di archiviazione premium

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. Impostare l'account di archiviazione creato in precedenza come account di archiviazione corrente per la sottoscrizione.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. Selezionare un'immagine per la macchina virtuale.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. Impostare le credenziali dell'account amministratore locale

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### Passaggio 3 - creare delle macchine virtuali

È necessario utilizzare un ciclo per creare tutte le macchine virtuali che si desidera e creare le schede di rete e le macchine virtuali necessarie all'interno del ciclo. Per creare le schede di rete e le macchine virtuali, eseguire questa procedura.

1. Avviare un `for` ciclo per ripetere i comandi per la creazione di una macchina virtuale e di due schede di rete tutte le volte che lo si ritiene necessario, in base al valore della `$numberOfVMs` variabile.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Creare un `VMConfig` oggetto che specifica l'immagine, le dimensioni e il set di disponibilità per la macchina virtuale.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. Eseguire il provisioning della macchina virtuale come macchina virtuale di Windows.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. Impostare il valore predefinito NIC e assegnare un indirizzo IP statico.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Aggiungere una seconda scheda di rete per ogni macchina virtuale.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig 

6. Creare dischi di dati per ogni macchina virtuale.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. Creare ogni macchina virtuale e terminare il ciclo.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### Passaggio 4 - eseguire lo script.

Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal prompt dei comandi **PowerShell**, o **PowerShell ISE**. Verrà visualizzato l'output iniziale, come illustrato di seguito.

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		                                                                            
		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Compilare le informazioni necessarie nella richiesta di credenziali e fare clic su **OK**. Verrà visualizzato l'output seguente.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 

<!---HONumber=Nov15_HO4-->