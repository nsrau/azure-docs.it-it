---
title: Copiare un'immagine da un'altra raccolta usando PowerShell
description: Copiare un'immagine da un'altra raccolta usando Azure PowerShell.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2f26716a4d04b574bf393f502758a725948419da
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89299950"
---
# <a name="copy-an-image-from-another-gallery-using-powershell"></a>Copiare un'immagine da un'altra raccolta usando PowerShell

Se nell'organizzazione sono presenti più raccolte, è possibile creare immagini da immagini archiviate in altre raccolte. È ad esempio possibile disporre di una raccolta di sviluppo e test per la creazione e il test di nuove immagini. Quando sono pronti per l'uso nell'ambiente di produzione, è possibile copiarli in una raccolta di produzione usando questo esempio. È anche possibile creare un'immagine da un'immagine in un'altra raccolta usando l'interfaccia della riga di comando di [Azure](image-version-another-gallery-cli.md).


## <a name="before-you-begin"></a>Prima di iniziare

Per completare questo articolo, è necessario disporre di una raccolta di origine, di una definizione di immagine e di una versione dell'immagine. È inoltre necessario disporre di una raccolta di destinazione. 

La versione dell'immagine di origine deve essere replicata nell'area in cui si trova la raccolta di destinazione. 

Verranno creati una nuova definizione di immagine e una nuova versione dell'immagine nella raccolta di destinazione.


Quando si lavora in questo articolo, sostituire i nomi delle risorse laddove necessario.


## <a name="get-the-source-image"></a>Ottenere l'immagine di origine 

Sono necessarie informazioni della definizione dell'immagine di origine, in modo che sia possibile crearne una copia nella raccolta di destinazione.

Elenca le informazioni sulle raccolte, le definizioni di immagini e le versioni di immagine esistenti usando il cmdlet [Get-AzResource](/powershell/module/az.resources/get-azresource) .

I risultati sono nel formato `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource `
   -ResourceType Microsoft.Compute/galleries/images/versions | `
   Format-Table -Property Name,ResourceGroupName
```

Quando si hanno tutte le informazioni necessarie, è possibile ottenere l'ID della versione dell'immagine di origine usando [Get-AzGalleryImageVersion](/powershell/module/az.compute/get-azgalleryimageversion). In questo esempio viene recuperata la `1.0.0` versione dell'immagine, della `myImageDefinition` definizione, nella raccolta di `myGallery` origine, nel gruppo di `myResourceGroup` risorse.

```azurepowershell-interactive
$sourceImgVer = Get-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name 1.0.0
```


## <a name="create-the-image-definition"></a>Creare la definizione dell'immagine 

È necessario creare una nuova definizione di immagine che corrisponda alla definizione dell'immagine dell'origine. È possibile visualizzare tutte le informazioni necessarie per ricreare la definizione dell'immagine usando [Get-AzGalleryImageDefinition](/powershell/module/az.compute/get-azgalleryimagedefinition).

```azurepowershell-interactive
Get-AzGalleryImageDefinition `
   -GalleryName myGallery `
   -ResourceGroupName myResourceGroup `
   -Name myImageDefinition
```


Verrà visualizzato un risultato simile al seguente:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Windows",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Creare una nuova definizione di immagine nella raccolta di destinazione usando il cmdlet [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) e le informazioni dell'output precedente.


In questo esempio, la definizione dell'immagine è denominata *myDestinationImgDef* nella raccolta denominata *myDestinationGallery*.


```azurepowershell-interactive
$destinationImgDef  = New-AzGalleryImageDefinition `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -Name 'myDestinationImgDef' `
   -OsState specialized `
   -OsType Windows `
   -HyperVGeneration v1 `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-the-image-version"></a>Creare la versione di immagine

Creare una versione dell'immagine usando [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). È necessario passare l'ID dell'immagine di origine nel `--managed-image` parametro per creare la versione dell'immagine nella raccolta di destinazione. 

I caratteri consentiti per le versioni delle immagini sono numeri e punti. I numeri devono essere compresi nell'intervallo di un valore Integer a 32 bit. Formato: *MajorVersion*.*MinorVersion*.*Patch*.

In questo esempio, la raccolta di destinazione è denominata *myDestinationGallery*, nel gruppo di risorse *myDestinationRG* , nella località *Stati Uniti occidentali* . La versione dell'immagine è *1.0.0* e verrà creata una replica nell'area *Stati Uniti centro-meridionali* e 2 repliche nell'area *Stati Uniti occidentali* . 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West US';ReplicaCount=2}
$targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $destinationImgDef.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName myDestinationGallery `
   -ResourceGroupName myDestinationRG `
   -Location WestUS `
   -TargetRegion $targetRegions  `
   -Source $sourceImgVer.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -asJob 
```

Può essere necessario un po' di tempo per replicare l'immagine in tutte le aree di destinazione, per questo motivo Microsoft ha creato un processo in modo che sia possibile tenere traccia dello stato di avanzamento. Per visualizzare lo stato di avanzamento del processo, digitare `$job.State`.

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> È necessario attendere che la creazione della versione dell'immagine venga interamente completata e replicata prima di poter usare la stessa immagine gestita o creare un'altra versione di immagine.
>
> Quando si crea la versione dell'immagine, è anche possibile archiviare l'immagine nell'archiviazione Premium, aggiungendo `-StorageAccountType Premium_LRS`, oppure nell'[archiviazione con ridondanza della zona](../storage/common/storage-redundancy.md), aggiungendo `-StorageAccountType Standard_ZRS`.
>


## <a name="next-steps"></a>Passaggi successivi

Creare una macchina virtuale da una versione di immagine [generalizzata](vm-generalized-image-version-powershell.md) o [specializzata](vm-specialized-image-version-powershell.md) .

Il [Generatore di immagini di Azure (anteprima)](./linux/image-builder-overview.md) consente di automatizzare la creazione della versione di immagine. è anche possibile usarla per aggiornare e [creare una nuova versione dell'immagine da una versione di immagine esistente](./linux/image-builder-gallery-update-image-version.md). 

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).
