<properties 
   pageTitle="Distribuire una VM con un IP pubblico statico con PowerShell in Gestione risorse | Microsoft Azure"
   description="Scoprire come distribuire le VM con un IP pubblico statico tramite PowerShell in Gestione risorse"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
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
   ms.date="03/15/2016"
   ms.author="jdial" />  

# Distribuire una VM con un IP pubblico statico mediante PowerShell

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] Modello di distribuzione classica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Passaggio 1 - avviare lo script

È possibile scaricare lo script di PowerShell completo utilizzato [qui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-ps.ps1). Attenersi alla procedura seguente per modificare lo script da usare nell'ambiente.

1. Modificare i valori delle variabili indicate di seguito in base ai valori che si desidera usare per la distribuzione. I valori riportati sotto si riferiscono allo scenario usato in questo documento.

		# Set variables resource group
		$rgName                = "IaaSStory"
		$location              = "West US"
		
		# Set variables for VNet
		$vnetName              = "WTestVNet"
		$vnetPrefix            = "192.168.0.0/16"
		$subnetName            = "FrontEnd"
		$subnetPrefix          = "192.168.1.0/24"
		
		# Set variables for storage
		$stdStorageAccountName = "iaasstorystorage"
		
		# Set variables for VM
		$vmSize                = "Standard_A1"
		$diskSize              = 127
		$publisher             = "MicrosoftWindowsServer"
		$offer                 = "WindowsServer"
		$sku                   = "2012-R2-Datacenter"
		$version               = "latest"
		$vmName                = "WEB1"
		$osDiskName            = "osdisk"
		$nicName               = "NICWEB1"
		$privateIPAddress      = "192.168.1.101"
		$pipName               = "PIPWEB1"
		$dnsName               = "iaasstoryws1"

## Passaggio 2 - Creare le risorse necessarie per la VM

Prima di creare una VM, è necessario disporre di un gruppo di risorse, una rete virtuale, un IP pubblico e una scheda di rete utilizzabili dalla VM.

1. Creare un nuovo gruppo di risorse.

		New-AzureRmResourceGroup -Name $rgName -Location $location
		
2. Creare rete virtuale e subnet.

		$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName `
		    -AddressPrefix $vnetPrefix -Location $location   
		
		Add-AzureRmVirtualNetworkSubnetConfig -Name $subnetName `
		    -VirtualNetwork $vnet -AddressPrefix $subnetPrefix
		
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 

3. Creare la risorsa di IP pubblico.

		$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName `
		    -AllocationMethod Static -DomainNameLabel $dnsName -Location $location

4. Creare l'interfaccia di rete (NIC) per la VM nella subnet creata in precedenza, con l'IP pubblico. Notare che il primo cmdlet che recupera la rete virtuale da Azure è necessario perché **Set-AzureRmVirtualNetwork** è stato eseguito per modificare la rete virtuale esistente.

		$vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name $subnetName
		$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
		    -Subnet $subnet -Location $location -PrivateIpAddress $privateIPAddress `
		    -PublicIpAddress $pip

5. Creare un account di archiviazione per ospitare l'unità del sistema operativo della VM.

		$stdStorageAccount = New-AzureRmStorageAccount -Name $stdStorageAccountName `
		    -ResourceGroupName $rgName -Type Standard_LRS -Location $location

## Passaggio 3 - Creare la VM 

Ora che tutte le risorse necessarie sono presenti, è possibile creare una nuova VM.

1. Creare l'oggetto di configurazione per la VM.

		$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize 

1. Ottenere le credenziali per l'account amministratore locale della VM.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

2. Creare un oggetto di configurazione della VM.

		$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName `
    		-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

3. Impostare l'immagine del sistema operativo per la VM.

		$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher `
    		-Offer $offer -Skus $sku -Version $version

4. Configurare il disco del sistema operativo.

		$osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage

5. Aggiungere la scheda di rete alla VM.

		$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id -Primary

6. Creare la macchina virtuale

		New-AzureRmVM -VM $vmConfig -ResourceGroupName $rgName -Location $location

7. Salvare il file di script.

## Passaggio 4 - eseguire lo script.

Dopo aver apportato tutte le modifiche necessarie e aver compreso il funzionamento dello script illustrato sopra, eseguire lo script.

1. Dalla console di PowerShell o PowerShell ISE, eseguire lo script sopra riportato.
2. L'output seguente deve essere visualizzato dopo pochi minuti.

		ResourceGroupName : IaaSStory
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory
				
		AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
		DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
		Subnets           : {FrontEnd}
		ProvisioningState : Succeeded
		AddressSpaceText  : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptionsText   : {}
		SubnetsText       : [
		                      {
		                        "Name": "FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24"
		                      }
		                    ]
		ResourceGroupName : IaaSStory
		Location          : westus
		ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Tag               : {}
		TagsTable         : 
		Name              : WTestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
		
		AddressSpace      : Microsoft.Azure.Commands.Network.Models.PSAddressSpace
		DhcpOptions       : Microsoft.Azure.Commands.Network.Models.PSDhcpOptions
		Subnets           : {FrontEnd}
		ProvisioningState : Succeeded
		AddressSpaceText  : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptionsText   : {
		                      "DnsServers": []
		                    }
		SubnetsText       : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]
		ResourceGroupName : IaaSStory
		Location          : westus
		ResourceGuid      : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Tag               : {}
		TagsTable         : 
		Name              : WTestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/WTestVNet
				
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		Status              : Succeeded
		StatusCode          : OK
		Output              : 
		StartTime           : 1/12/2016 12:57:56 PM -08:00
		EndTime             : 1/12/2016 12:59:13 PM -08:00
		Error               : 
		ErrorText           : 

   

<!---HONumber=AcomDC_0810_2016-->