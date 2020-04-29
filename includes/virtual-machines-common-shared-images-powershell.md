---
title: includere il file
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/21/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bae66078a1bcb1d80f0798b1d501598fa785fb80
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/26/2020
ms.locfileid: "66241224"
---
## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/powershell](https://shell.azure.com/powershell). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere INVIO per eseguirli.


## <a name="get-the-managed-image"></a>Ottenere l'immagine gestita

È possibile visualizzare un elenco delle immagini disponibili in un gruppo di risorse usando [Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage). Quando si conosce il nome dell'immagine e in quale gruppo di risorse si trova, è possibile usare nuovamente `Get-AzImage` per ottenere l'oggetto immagine e archiviarlo in una variabile da usare in un secondo momento. Questo esempio mostra come ottenere un'immagine denominata *myImage* dal gruppo di risorse "myResourceGroup" che lo assegna alla variabile *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>Creare un raccolta di immagini 

Una raccolta di immagini è la risorsa principale usata per l'abilitazione della condivisione di immagini. I caratteri consentiti per i nomi delle raccolte sono lettere maiuscole o minuscole, numeri e punti. Il nome della raccolta non può contenere trattini. I nomi di raccolta devono essere univoci all'interno della sottoscrizione. 

Creare una raccolta di immagini usando [New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery). L'esempio seguente crea una raccolta denominata *myGallery* nel gruppo di risorse *myGalleryRG*.

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>Creare una definizione dell'immagine 

Le definizioni di immagine creano un raggruppamento logico per le immagini. Vengono utilizzati per gestire le informazioni sulle versioni delle immagini create al suo interno. I nomi delle definizioni di immagine possono essere costituiti da lettere maiuscole o minuscole, cifre, punti, trattini e punti. Per ulteriori informazioni sui valori che è possibile specificare per la definizione di un'immagine, vedere [definizioni di immagine](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#image-definitions).

Creare la definizione dell'immagine usando [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). In questo esempio, l'immagine della raccolta è denominata *myGalleryImage*.

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Creare una versione di immagine

Creare una versione dell'immagine da un'immagine gestita usando [New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion). 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*. *MinorVersion*. *Patch*.

In questo esempio la versione dell'immagine è *1.0.0* e viene replicata nei datacenter degli *Stati Uniti centro-occidentali* e degli *Stati Uniti centro-meridionali*. Quando si scelgono le aree di destinazione per la replica, tenere presente che è necessario includere anche l'area di *origine* come destinazione per la replica.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

Può essere necessario un po' di tempo per replicare l'immagine in tutte le aree di destinazione, per questo motivo Microsoft ha creato un processo in modo che sia possibile tenere traccia dello stato di avanzamento. Per visualizzare lo stato di avanzamento del processo, digitare `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> È necessario attendere che la versione dell'immagine completi la compilazione e la replica prima di poter usare la stessa immagine gestita per creare un'altra versione dell'immagine. 
>
> È anche possibile archiviare la versione dell'immagine nell' [archiviazione con ridondanza della zona](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) aggiungendo `-StorageAccountType Standard_ZRS` quando si crea la versione dell'immagine.
>


## <a name="share-the-gallery"></a>Condividere la raccolta

Si consiglia di condividere l'accesso a livello di raccolta immagini. Usare un indirizzo di posta elettronica e il cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) per ottenere l'ID oggetto per l'utente e quindi usare [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) per concedere loro l'accesso alla raccolta. Sostituire l'indirizzo di posta alinne_montes@contoso.com elettronica di esempio, in questo esempio, con le proprie informazioni.

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