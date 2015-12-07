<properties 
   pageTitle="Distribuire macchine virtuali con più schede di rete tramite PowerShell in Gestione risorse | Microsoft Azure"
   description="Informazioni su come distribuire macchine virtuali con più schede di rete tramite PowerShell in Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/20/2015"
   ms.author="telmos" />

#Distribuire macchine virtuali con più schede di rete tramite PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Poiché in questo momento non è possibile disporre di macchine virtuali con una singola scheda di interfaccia di rete e di macchine virtuali con più schede di interfaccia di rete nello stesso gruppo di risorse, i server back-end verranno implementati in un gruppo di risorse diverso da quello di tutti gli altri componenti. La procedura seguente usa un gruppo di risorse denominato *IaaSStory* per il gruppo di risorse principale e *IaaSStory-BackEnd* per i server back-end.

## Prerequisiti

Prima di distribuire i server back-end, è necessario distribuire il gruppo di risorse principale con tutte le risorse necessarie per questo scenario. Per distribuire tali risorse, seguire questa procedura.

1. Passare alla [pagina del modello](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC).
2. Nella pagina del modello, a destra del **gruppo di risorse padre**, fare clic su **Deploy to Azure**.
3. Se necessario, modificare i valori dei parametri, quindi seguire i passaggi nel portale di anteprima di Azure per distribuire il gruppo di risorse.

> [AZURE.IMPORTANT]Assicurarsi che i nomi degli account di archiviazione siano univoci. In Azure non sono infatti ammessi nomi di account di archiviazione duplicati.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Distribuire le macchine virtuali di back-end

Le macchine virtuali di back-end dipendono dalla creazione di risorse elencate di seguito.

- **Account di archiviazione per i dischi dati**. Per migliorare le prestazioni, i dischi dati sui server di database utilizzano la tecnologia SSD (Solid State Drive), che richiede un account di archiviazione premium. Verificare che la posizione di Azure distribuita supporti l'archiviazione premium.
- **Schede di rete**. Ogni macchina virtuale ha due schede di rete, una per l'accesso al database e una per la gestione.
- **Set di disponibilità**. Tutti i server di database vengono aggiunti a un singolo set di disponibilità, per garantire che almeno una delle macchine virtuali sia attiva e in esecuzione durante la manutenzione.  

### Passaggio 1 - Avviare lo script

È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/multinic.ps1). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Modificare i valori delle variabili indicate di seguito in base al gruppo di risorse esistente distribuito in precedenza in [Prerequisiti](#Prerequisites).

		$existingRGName        = "IaaSStory"
		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"
		$remoteAccessNSGName   = "NSG-RemoteAccess"
		$stdStorageAccountName = "wtestvnetstoragestd"

2. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione di back-end.

		$backendRGName         = "IaaSStory-Backend"
		$prmStorageAccountName = "wtestvnetstorageprm"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$publisher             = "MicrosoftSQLServer"
		$offer                 = "SQL2014SP1-WS2012R2"
		$sku                   = "Standard"
		$version               = "latest"
		$vmNamePrefix          = "DB"
		$osDiskPrefix          = "osdiskdb"
		$dataDiskPrefix        = "datadisk"
		$nicNamePrefix         = "NICDB"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

3. Recuperare le risorse esistenti necessarie per la distribuzione.

		$vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
		$backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
		$remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
		$stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### Passaggio 2 - Creare le risorse necessarie per le macchine virtuali

È necessario creare un nuovo gruppo di risorse, un account di archiviazione per i dischi dati e un set di disponibilità per tutte le macchine virtuali. Sono inoltre necessarie credenziali dell'account amministratore locale per ciascuna macchina virtuale. Per creare queste risorse, eseguire questi passaggi.

1. Creare un nuovo gruppo di risorse.

		New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Creare un nuovo account di archiviazione premium nel gruppo di risorse creato in precedenza.

		$prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
			-ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Creare un nuovo set di disponibilità.

		$avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Ottenere le credenziali dell'account amministratore locale per ciascuna macchina virtuale.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

### Passaggio 3 - Creare le schede di rete e le macchine virtuali di back-end

È necessario utilizzare un ciclo per creare tutte le macchine virtuali che si desidera, nonché le schede di rete e le macchine virtuali necessarie all'interno del ciclo. Per creare le schede di rete e le macchine virtuali, eseguire questa procedura.

1. Avviare un ciclo `for` per ripetere i comandi per la creazione di una macchina virtuale e di due schede di rete per il numero di volte necessario, in base al valore della variabile `$numberOfVMs`.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Creare la scheda di rete utilizzata per l'accesso al database.
		
		    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
		    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
		    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Creare la scheda di rete utilizzata per l'accesso remoto. Si noti che a questa scheda di rete è associato un NSG.

		    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
		    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
		    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
				-NetworkSecurityGroupId $remoteAccessNSG.Id

4. Creare l'oggetto `vmConfig`.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Creare due dischi dati per ciascuna macchina virtuale. Si noti che i dischi dati si trovano nell'account di archiviazione premium creato in precedenza.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
				-VhdUri $data1VhdUri -CreateOption empty -Lun 0
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"    
		    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
				-VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Configurare il sistema operativo e l'immagine da utilizzare per la macchina virtuale.
		    
		    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Aggiungere le due schede di rete create in precedenza per l'oggetto `vmConfig`.

		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Creare il disco del sistema operativo e la macchina virtuale. Si noti il simbolo `}` al termine del ciclo `for`.

		    $osDiskName = $vmName + "-" + $osDiskSuffix
		    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
		    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
		}

### Passaggio 4 - Eseguire lo script.

Una volta scaricato e modificato lo script in base alle esigenze, eseguire lo script per creare macchine virtuali del database di back-end con più schede di rete.

1. Salvare lo script ed eseguirlo dal prompt dei comandi **PowerShell**, o **PowerShell ISE**. Verrà visualizzato l'output iniziale, come illustrato di seguito.

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Dopo qualche minuto, inserire le credenziali nella casella di richiesta e fare clic su **OK**. L'output seguente rappresenta una singola macchina virtuale. Si noti che per il completamento dell'intero processo sono stati necessari 8 minuti.

		ResourceGroupName            : 
		Id                           : 
		Name                         : DB2
		Type                         : 
		Location                     : 
		Tags                         : 
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   : 
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 : 
		InstanceViewText             : null
		NetworkProfile               : 
		NetworkProfileText           : null
		OSProfile                    : 
		OSProfileText                : null
		Plan                         : 
		PlanText                     : null
		ProvisioningState            : 
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1}
		NetworkInterfaceIDs          : 
		RequestId                    : 
		StatusCode                   : 0
		
		
		ResourceGroupName            : 
		Id                           : 
		Name                         : DB2
		Type                         : 
		Location                     : 
		Tags                         : 
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   : 
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 : 
		InstanceViewText             : null
		NetworkProfile               : 
		NetworkProfileText           : null
		OSProfile                    : 
		OSProfileText                : null
		Plan                         : 
		PlanText                     : null
		ProvisioningState            : 
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   },
		                                   {
		                                     "Lun": 1,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-2",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
		NetworkInterfaceIDs          : 
		RequestId                    : 
		StatusCode                   : 0
		
		
		EndTime             : 10/30/2015 9:30:03 AM -08:00
		Error               : 
		Output              : 
		StartTime           : 10/30/2015 9:22:54 AM -08:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK

<!---HONumber=AcomDC_1125_2015-->