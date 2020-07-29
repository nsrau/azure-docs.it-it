---
title: 'Esercitazione: Creare immagini di VM personalizzate con Azure PowerShell'
description: Questa esercitazione illustra come usare Azure PowerShell per creare un'immagine personalizzata della macchina virtuale Windows archiviata in un'istanza di Raccolta immagini condivise di Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cf8fc9916384c9eef24c4c50f7647632c0e6b7a2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077470"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Esercitazione: Creare immagini di macchina virtuale Windows con Azure PowerShell

È possibile usare le immagini per eseguire il bootstrap delle distribuzioni e garantire la coerenza tra più macchine virtuali. In questa esercitazione viene creata un'immagine specializzata di una macchina virtuale di Azure con PowerShell e archiviarla in un'istanza di Raccolta immagini condivise. Si apprenderà come:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione dell'immagine
> * Creare una versione di immagine
> * Creare una macchina virtuale da un'immagine 
> * Condividere una raccolta di immagini



## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come convertire una macchina virtuale esistente in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove macchine virtuali.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, vedere l'[avvio rapido con PowerShell](quick-create-powershell.md) per creare una macchina virtuale da usare per questa esercitazione. Quando si esegue l'esercitazione, sostituire i nomi delle risorse dove necessario.

## <a name="overview"></a>Panoramica

La [raccolta di immagini condivise](shared-image-galleries.md) semplifica la condivisione di immagini personalizzate all'interno dell'organizzazione. Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. 

Raccolta immagini condivise consente di condividere le immagini di macchine virtuali personalizzate con altri utenti. Scegliere le immagini che si intende condividere, le aree nelle quali si vuole renderle disponibili e i destinatari. 

La funzionalità Raccolta di immagini condivise presenta più tipi di risorse:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.

## <a name="get-the-vm"></a>Ottenere la macchina virtuale

Per visualizzare un elenco di macchine virtuali disponibili in un gruppo di risorse, usare [Get-AzVM](/powershell/module/az.compute/get-azvm). Quando si conosce il nome della macchina virtuale e il gruppo di risorse in cui si trova, è possibile usare nuovamente `Get-AzVM` per ottenere l'oggetto macchina virtuale e archiviarlo in una variabile da usare in un secondo momento. Questo esempio recupera una VM denominata *sourceVM* dal gruppo di risorse "myResourceGroup" e la assegna alla variabile *$sourceVM*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Nell'esempio seguente viene creato un gruppo di risorse denominato *myGalleryRG* nell'area *EastUS*:

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per il nome della raccolta sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini. I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [New-AzGallery](/powershell/module/az.compute/new-azgallery). L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagini creano un raggruppamento logico per le immagini. Vengono usate per gestire le informazioni sulle versioni di immagini create al loro interno. I nomi delle definizioni di immagini possono essere costituiti da lettere maiuscole o minuscole, numeri, trattini e punti. Per altre informazioni sui valori che è possibile specificare per la definizione di immagine, vedere [Definizioni di immagini](./shared-image-galleries.md#image-definitions).

Per creare la definizione di immagine, usare [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). In questo esempio l'immagine della raccolta è denominata *myGalleryImage* e viene creata per un'immagine specializzata. 

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

Per creare una versione di immagine da una macchina virtuale, usare [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

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
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

La replica dell'immagine in tutte le aree di destinazione può richiedere tempo.


## <a name="create-a-vm"></a>Creare una macchina virtuale 

Ora che si dispone di un'immagine specializzata, è possibile usarla per creare una o più macchine virtuali nuove usando il cmdlet [New-AzVM](/powershell/module/az.compute/new-azvm). Per usare l'immagine, eseguire `Set-AzVMSourceImage` e impostare `-Id` sull'ID definizione dell'immagine (in questo caso $galleryImage.Id) in modo da usare sempre l'ultima versione dell'immagine. 

Sostituire i nomi delle risorse di questo esempio secondo necessità. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


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

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse e tutte le risorse correlate con il cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure offre anche un servizio, basato su Packer, ovvero [Image Builder per macchine virtuali di Azure](./image-builder-overview.md). È sufficiente descrivere le personalizzazioni in un modello per gestire la creazione dell'immagine tramite questo servizio. 

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale specializzata. Si è appreso come:

> [!div class="checklist"]
> * Creare una Raccolta immagini condivise
> * Creare una definizione dell'immagine
> * Creare una versione di immagine
> * Creare una macchina virtuale da un'immagine 
> * Condividere una raccolta di immagini

Passare all'esercitazione successiva per scoprire come creare macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Creare VM a disponibilità elevata](tutorial-availability-sets.md)
