---
title: "Esercitazione: Usare un'immagine di VM personalizzata in un set di scalabilità con Azure PowerShell"
description: Informazioni su come usare Azure PowerShell per creare un'immagine di VM personalizzata che è possibile usare per distribuire un set di scalabilità di macchine virtuali
author: cynthn
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 4b072991a86922fe2b4ba5be93b4c96841dc24af
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792769"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-azure-powershell"></a>Esercitazione: Creare e usare un'immagine personalizzata per i set di scalabilità di macchine virtuali con Azure PowerShell

Quando si crea un set di scalabilità, si specifica un'immagine da usare quando vengono distribuite le istanze di macchina virtuale. Per ridurre il numero di attività dopo la distribuzione delle istanze di macchina virtuale, è possibile usare un'immagine di VM personalizzata. Questa immagine di VM personalizzata include le installazioni o le configurazioni delle applicazioni necessarie. Le istanze di macchina virtuale create nel set di scalabilità usano l'immagine di VM personalizzata e sono pronte per gestire il traffico delle applicazioni. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione dell'immagine
> * Creare una versione di immagine
> * Creare un set di scalabilità da un'immagine 
> * Condividere una raccolta di immagini

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come convertire una macchina virtuale esistente in un'immagine personalizzata riutilizzabile che è possibile usare per creare un set di scalabilità.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, vedere l'[avvio rapido di PowerShell](quick-create-powershell.md) per creare una macchina virtuale da usare per questa esercitazione. Quando si esegue l'esercitazione, sostituire i nomi delle risorse dove necessario.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.


## <a name="get-the-vm"></a>Ottenere la macchina virtuale

Per visualizzare un elenco di macchine virtuali disponibili in un gruppo di risorse, usare [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm). Quando si conosce il nome della macchina virtuale e il gruppo di risorse in cui si trova, è possibile usare nuovamente `Get-AzVM` per ottenere l'oggetto macchina virtuale e archiviarlo in una variabile da usare in un secondo momento. Questo esempio mostra come ottenere una macchina virtuale denominata *sourceVM* dal gruppo di risorse "myResourceGroup" e assegnarla alla variabile *$vm*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```
## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup).

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Nell'esempio seguente viene creato un gruppo di risorse denominato *myGalleryRG* nell'area *EastUS*:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per il nome della raccolta sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini. I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usate per gestire le informazioni sulle versioni di immagini create al loro interno. I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Per creare la definizione di immagine, usare [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). In questo esempio l'immagine della raccolta è denominata *myGalleryImage* e viene creata per un'immagine specializzata. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Creare una versione di immagine

Per creare una versione di immagine da una macchina virtuale, usare [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei data center degli *Stati Uniti orientali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, è necessario includere l'area *source* come destinazione per la replica.

Per creare una versione di immagine dalla macchina virtuale, usare `$vm.Id.ToString()` per `-Source`.

```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='East US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $vm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

La replica dell'immagine in tutte le aree di destinazione può richiedere tempo.

## <a name="create-a-scale-set-from-the-image"></a>Creare un set di scalabilità dall'immagine
Creare ora un set di scalabilità con [New-AzVmss](/powershell/module/az.compute/new-azvmss), che usa il parametro `-ImageName` per definire l'immagine di VM personalizzata creata nel passaggio precedente. Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985. Quando richiesto, fornire le proprie credenziali amministrative desiderate per le istanze di macchina virtuale nel set di scalabilità:

```azurepowershell-interactive
# Define variables for the scale set
$resourceGroupName = "myVMSSRG3"
$scaleSetName = "myScaleSet3"
$location = "East US"

# Create a resource group
New-AzResourceGroup -ResourceGroupName $resourceGroupName -Location $location

# Create a networking pieces
$subnet = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName $resourceGroupName `
  -Name "myVnet" `
  -Location $location `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $subnet
$publicIP = New-AzPublicIpAddress `
  -ResourceGroupName $resourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -Name "myPublicIP"
$frontendIP = New-AzLoadBalancerFrontendIpConfig `
  -Name "myFrontEndPool" `
  -PublicIpAddress $publicIP
$backendPool = New-AzLoadBalancerBackendAddressPoolConfig -Name "myBackEndPool"
$inboundNATPool = New-AzLoadBalancerInboundNatPoolConfig `
  -Name "myRDPRule" `
  -FrontendIpConfigurationId $frontendIP.Id `
  -Protocol TCP `
  -FrontendPortRangeStart 50001 `
  -FrontendPortRangeEnd 50010 `
  -BackendPort 3389
# Create the load balancer and health probe
$lb = New-AzLoadBalancer `
  -ResourceGroupName $resourceGroupName `
  -Name "myLoadBalancer" `
  -Location $location `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool `
  -InboundNatPool $inboundNATPool
Add-AzLoadBalancerProbeConfig -Name "myHealthProbe" `
  -LoadBalancer $lb `
  -Protocol TCP `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
Add-AzLoadBalancerRuleConfig `
  -Name "myLoadBalancerRule" `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol TCP `
  -FrontendPort 80 `
  -BackendPort 80 `
  -Probe (Get-AzLoadBalancerProbeConfig -Name "myHealthProbe" -LoadBalancer $lb)
Set-AzLoadBalancer -LoadBalancer $lb

# Create IP address configurations
$ipConfig = New-AzVmssIpConfig `
  -Name "myIPConfig" `
  -LoadBalancerBackendAddressPoolsId $lb.BackendAddressPools[0].Id `
  -LoadBalancerInboundNatPoolsId $inboundNATPool.Id `
  -SubnetId $vnet.Subnets[0].Id

# Create a configuration 
$vmssConfig = New-AzVmssConfig `
    -Location $location `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode "Automatic"

# Reference the image version
Set-AzVmssStorageProfile $vmssConfig `
  -OsDiskCreateOption "FromImage" `
  -ImageReferenceId $galleryImage.Id

# Complete the configuration
 
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name "network-config" `
  -Primary $true `
  -IPConfiguration $ipConfig 

# Create the scale set 
New-AzVmss `
  -ResourceGroupName $resourceGroupName `
  -Name $scaleSetName `
  -VirtualMachineScaleSet $vmssConfig
```

La creazione e la configurazione di tutte le macchine virtuali e risorse del set di scalabilità richiedono alcuni minuti.


## <a name="share-the-gallery"></a>Condividere la raccolta

È consigliabile condividere l'accesso a livello di raccolta immagini. Usare un indirizzo di posta elettronica e il cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) per ottenere l'ID oggetto per l'utente e quindi usare [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) per concedere l'accesso alla raccolta. Sostituire l'indirizzo di posta elettronica di esempio (in questo esempio alinne_montes@contoso.com) con quello personalizzato.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate con il cmdlet [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the scale set resource group
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure offre anche un servizio, basato su Packer, ovvero [Image Builder per macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/windows/image-builder-overview). È sufficiente descrivere le personalizzazioni in un modello per gestire la creazione dell'immagine tramite questo servizio. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato descritto come creare e usare un'immagine di macchina virtuale personalizzata per il set di scalabilità con Azure PowerShell:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione dell'immagine
> * Creare una versione di immagine
> * Creare un set di scalabilità da un'immagine 
> * Condividere una raccolta di immagini

Passare all'esercitazione successiva per informazioni su come distribuire le applicazioni nel set di scalabilità.

> [!div class="nextstepaction"]
> [Distribuire le applicazioni nei set di scalabilità](tutorial-install-apps-powershell.md)
