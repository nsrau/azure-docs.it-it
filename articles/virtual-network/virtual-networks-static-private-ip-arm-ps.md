<properties 
   pageTitle="Come impostare un indirizzo IP statico privato in modalità ARM utilizzando PowerShell| Microsoft Azure"
   description="Informazioni su indirizzi IP statici (DIP) e come gestirli in modalità ARM tramite PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/08/2015"
   ms.author="telmos" />

# Come impostare un indirizzo IP statico privato in PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Questo articolo illustra il modello di distribuzione Gestione risorse. È inoltre possibile [gestire un indirizzo IP statico privato nel modello di distribuzione classico](virtual-networks-static-private-ip-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

I comandi di esempio PowerShell riportati di seguito prevedono un ambiente semplice già creato in base allo scenario precedente. Se si desidera eseguire i comandi illustrati in questo documento, creare innanzitutto l'ambiente di prova descritto in [creare una rete virtuale](virtual-networks-create-vnet-arm-ps.md).

## Come specificare un indirizzo IP statico privato durante la creazione di una macchina virtuale.
Per creare una macchina virtuale denominata *DNS01* nel subnet *FrontEnd* di una rete virtuale denominata *TestVNet* con un indirizzo IP statico privato di *192.168.1.101*, seguire la procedura seguente:

1. Se è la prima volta che si usa Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](powershell-install-configure.md) e seguire le istruzioni fino al termine della procedura per accedere ad Azure e selezionare la sottoscrizione preferita.
2. Da un prompt di Azure PowerShell, eseguire il cmdlet **Switch-AzureMode** per passare alla modalità Gestione risorse, come illustrato di seguito.

		Switch-AzureMode AzureResourceManager
	
	Output previsto:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Il cmdlet Switch-AzureMode verrà presto dichiarato obsoleto. Di conseguenza, tutti i cmdlet di Gestione risorse verranno rinominati.
1. Impostare le variabili per l'account di archiviazione, il percorso, il gruppo di risorse e le credenziali da utilizzare. È necessario immettere un nome utente e una password per la macchina virtuale. L’account di archiviazione e il gruppo di risorse deve esistere già.

		$stName = "vnetstorage"
		$locName = "Central US"
		$rgName = "TestRG"
	    $cred = Get-Credential -Message "Type the name and password of the local administrator account."

3. Recuperare la rete virtuale e la subnet che si desidera creare nella macchina virtuale.

	    $vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet	
	    $subnet = $vnet.Subnets[0].Id

4. Se necessario, creare un indirizzo IP pubblico per accedere alla macchina virtuale da Internet.

		$pip = New-AzurePublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

5. Creare una scheda di rete utilizzando l'indirizzo IP privato statico da assegnare alla macchina virtuale. Assicurarsi che l'indirizzo IP venga dall'intervallo di subnet che si aggiunge alla macchina virtuale. Questo è il passaggio principale di questo articolo, in cui si imposta l’IP privato che deve essere statico.

		$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101

6. Creare la macchina virtuale utilizzando la scheda di rete creata in precedenza.

		$vm = New-AzureVMConfig -VMName DNS01 -VMSize "Standard_A1"
		$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
		$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
		$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
		$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
		New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm 

	Output previsto:

		EndTime             : 9/8/2015 2:32:09 PM -07:00
		Error               : 
		Output              : 
		StartTime           : 9/8/2015 2:27:42 PM -07:00
		Status              : Succeeded
		TrackingOperationId : 249e4cc9-1257-42a7-a2fd-6962243c5b6d
		RequestId           : 8dd60f6d-6453-49c4-8f6a-d3c6356dd2a4
		StatusCode          : OK 


## Come recuperare le informazioni relative all'indirizzo IP privato statico per una macchina virtuale
Per visualizzare le informazioni relative all'indirizzo IP privato statico per la macchina virtuale creata con lo script precedente, eseguire il comando PowerShell seguente e osservare i valori per *PrivateIpAddress* e *PrivateIpAllocationMethod*:

	Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG

Output previsto:

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"ee300945-3bb1-4eda-bc90-3a6d65fad60a"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/DNS01"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"ee300945-3bb1-4eda-bc90-3a6d65fad60a"",
	                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Static",
	                           "Subnet": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Come rimuovere un indirizzo IP statico privato da una macchina virtuale
Per rimuovere l'indirizzo IP privato statico aggiunto alla macchina virtuale nello script precedente, eseguire il comando PowerShell seguente:
	
	$nic=Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
	Set-AzureNetworkInterface -NetworkInterface $nic

Output previsto:

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"45ccb5a5-934e-485f-b2fd-e4a014131032"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/WindowsVM"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"45ccb5a5-934e-485f-b2fd-e4a014131032"",
	                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Dynamic",
	                           "Subnet": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
	                           },
	                           "LoadBalancerBackendAddressPools": [],
	                           "LoadBalancerInboundNatRules": [],
	                           "ProvisioningState": "Succeeded"
	                         }
	                       ]
	DnsSettings          : {
	                         "DnsServers": [],
	                         "AppliedDnsServers": [],
	                         "InternalDnsNameLabel": null,
	                         "InternalFqdn": null
	                       }
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Come aggiungere un indirizzo IP statico privato a una macchina virtuale esistente
Per aggiungere un indirizzo IP privato statico alla macchina virtuale creata usando lo script precedente, eseguire il comando seguente:

	$nic=Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
	$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
	Set-AzureNetworkInterface -NetworkInterface $nic

## Passaggi successivi

- Informazioni su [indirizzi IP pubblici riservati](../virtual-networks-reserved-public-ip).
- Informazioni su [indirizzi IP pubblici a livello di istanza (ILPIP)](../virtual-networks-instance-level-public-ip).
- Consultare le [API REST dell'indirizzo IP riservato](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Sept15_HO3-->