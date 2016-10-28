<properties
	pageTitle="Spostare una VM di Windows | Microsoft Azure"
	description="Spostare una VM di Windows in un'altra sottoscrizione o in un altro gruppo di risorse di Azure nel modello di distribuzione di Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>  

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/08/2016"
	ms.author="cynthn"/>  

	


# Spostare una VM di Windows in un'altra sottoscrizione o in un altro gruppo di risorse di Azure 

Questo articolo illustra come spostare una VM di Windows tra gruppi di risorse o sottoscrizioni. Lo spostamento tra sottoscrizioni può essere comodo se in origine è stata creata una VM in una sottoscrizione personale e ora si vuole spostarla alla sottoscrizione dell'azienda per continuare il lavoro.

> [AZURE.NOTE] Nell'ambito dello spostamento verranno creati nuovi ID risorsa. Una volta spostata la VM, sarà necessario aggiornare strumenti e script in modo che utilizzino i nuovi ID risorsa.


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## Usare PowerShell per spostare una VM

Per spostare una macchina virtuale in un altro gruppo di risorse, è necessario assicurarsi di spostare anche tutte le risorse dipendenti. Per usare il cmdlet Move-AzureRMResource, è necessario conoscere il nome della risorsa e il tipo di risorsa. È possibile ottenere entrambi con il cmdlet Find-AzureRMResource.

	Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
	

Per spostare una VM, occorre spostare più risorse. È sufficiente creare variabili separate per ogni risorsa e quindi elencarle. Questo esempio include la maggior parte delle risorse di base per una VM, ma è possibile aggiungerne altre in base alle esigenze.

	$sourceRG = "<sourceResourceGroupName>"
	$destinationRG = "<destinationResourceGroupName>"
    
	$vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
	$diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
	$vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
	$nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
	$ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
	$nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
	
	Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Per spostare le risorse in una diversa sottoscrizione, includere il parametro **DestinationSubscriptionId**.

	Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Verrà richiesto di confermare che si vuole spostare le risorse specificate. Digitare **Y** per confermare lo spostamento delle risorse.

  
## Passaggi successivi

È possibile spostare molti tipi diversi di risorse tra gruppi di risorse e sottoscrizioni. Per altre informazioni, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0810_2016-->