---
title: Anteprima - Creare una versione di immagine crittografata con le chiavi personali
description: Creare una versione di immagine in una raccolta di immagini condivise usando le chiavi di crittografia gestite dal cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/11/2020
ms.author: cynthn
ms.openlocfilehash: 91f485d03717ab80bac26abd16da165d7b0dead7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291926"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Anteprima: Usare le chiavi gestite dal cliente per crittografare le immagini

Le immagini della raccolta vengono archiviate come dischi gestiti e vengono pertanto crittografate automaticamente tramite la crittografia lato server. La crittografia lato server usa la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme allo standard FIPS 140-2. Per altre informazioni sui moduli crittografici sottostanti i dischi gestiti di Azure, vedere [API di crittografia: prossima generazione](/windows/desktop/seccng/cng-portal).

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia delle immagini, usare le proprie chiavi oppure è possibile usare entrambe insieme per la crittografia doppia. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dischi nelle immagini. 

La crittografia lato server con chiavi gestite dal cliente usa Azure Key Vault. È possibile importare [le chiavi RSA personali](../key-vault/keys/hsm-protected-keys.md) nel proprio insieme di credenziali delle chiavi o generare nuove chiavi RSA in Azure Key Vault.

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario avere già un set di crittografia del disco da usare per l'immagine.

- Per usare solo una chiave gestita dal cliente, vedere **abilitare le chiavi gestite dal cliente con la crittografia lato server** usando il [portale di Azure](./disks-enable-customer-managed-keys-portal.md) o [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Per usare le chiavi gestite dalla piattaforma e gestite dal cliente (per la crittografia doppia), vedere **abilitare la crittografia a doppia inattiva** usando il [portale di Azure](./disks-enable-double-encryption-at-rest-portal.md) o [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).
    > [!IMPORTANT]
    > È necessario usare questo collegamento [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) per accedere al portale di Azure. La crittografia a doppio inattivo non è attualmente visibile nel portale di Azure pubblico senza usare il collegamento.

## <a name="limitations"></a>Limitazioni

Quando si usano chiavi gestite dal cliente per crittografare le immagini della raccolta di immagini condivise, esistono diverse limitazioni:  

- I set di chiavi di crittografia devono trovarsi nella stessa sottoscrizione e nella stessa area dell'immagine.

- Le immagini che usano chiavi gestite dal cliente non possono essere condivise. 

- Le immagini che usano chiavi gestite dal cliente in altre aree non possono essere replicate.

- Dopo aver usato le proprie chiavi per crittografare un disco o un'immagine, non è possibile tornare all'uso delle chiavi gestite dalla piattaforma per la crittografia di tale disco o immagine.


> [!IMPORTANT]
> La crittografia con chiavi gestite dal cliente è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Per l'anteprima pubblica, è necessario prima registrare la funzionalità.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Per completare la registrazione sono necessari alcuni minuti. Usare Get-AzProviderFeature per verificare lo stato della registrazione della funzionalità.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando RegistrationState restituisce lo stato Registered, è possibile proseguire con il passaggio successivo.

Controllare la registrazione del provider. Verificare che restituisca `Registered`.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se non restituisce `Registered`, usare il comando seguente per registrare i provider:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Per specificare un set di crittografia dischi per una versione dell'immagine, usare [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) con il parametro `-TargetRegion`. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @($region1)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Creare una macchina virtuale

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è identica alla creazione di un macchina virtuale [generalizzata](vm-generalized-image-version-powershell.md) o [specializzata](vm-specialized-image-version-powershell.md) in base a un'immagine. È necessario usare il set di parametri esteso e aggiungere `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` alla configurazione della macchina virtuale.

Per i dischi dati, è necessario aggiungere il parametro `-DiskEncryptionSetId $setID` quando si usa [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Per l'anteprima pubblica, è necessario prima registrare la funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verificare lo stato della registrazione della funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando viene restituito `"state": "Registered"`, è possibile proseguire con il passaggio successivo.

Controllare la registrazione.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se lo stato non risulta registrato, eseguire il comando seguente:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Per specificare un set di crittografia dischi per una versione dell'immagine, usare [az image gallery create-image-version](/cli/azure/sig/image-version#az-sig-image-version-create) con il parametro `--target-region-encryption`. Il formato per `--target-region-encryption` è un elenco delimitato da virgole di chiavi per la crittografia del sistema operativo e dei dischi dati. L'aspetto dovrebbe risultare simile al seguente: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Se l'origine per il disco del sistema operativo è un disco gestito o una macchina virtuale, usare `--managed-image` per specificare l'origine per la versione dell'immagine. In questo esempio l'origine è un'immagine gestita con un disco del sistema operativo e un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se l'origine per il disco del sistema operativo è uno snapshot, usare `--os-snapshot` per specificarlo. Se sono presenti snapshot del disco dati che devono anch'essi far parte della versione dell'immagine, aggiungerli usando `--data-snapshot-luns` per specificare il LUN e `--data-snapshots` per specificare gli snapshot.

In questo esempio le origini sono costituite da snapshot del disco. Sono presenti un disco del sistema operativo e un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Creare la VM

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è identica alla creazione di una macchina virtuale [generalizzata](vm-generalized-image-version-cli.md) o [specializzata](vm-specialized-image-version-cli.md) in base a un'immagine. È sufficiente aggiungere il parametro `--os-disk-encryption-set` con l'ID del set di crittografia. Per i dischi dati, aggiungere `--data-disk-encryption-sets` con un elenco delimitato da spazi dei set di crittografia dischi.


## <a name="portal"></a>Portale

Quando si crea la versione dell'immagine nel portale, è possibile usare la scheda **crittografia** per immettere applica i set di crittografia di archiviazione.

> [!IMPORTANT]
> Per usare la crittografia doppia, è necessario usare questo collegamento [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) per accedere al portale di Azure. La crittografia a doppio inattivo non è attualmente visibile nel portale di Azure pubblico senza usare il collegamento.


1. Nella pagina **Crea versione immagine** selezionare la scheda **Crittografia**.
2. In **tipo di crittografia**selezionare **crittografia dei componenti inattivi con una chiave gestita dal cliente o una** **doppia crittografia con chiavi gestite dalla piattaforma e gestite dal cliente**. 
3. Per ogni disco nell'immagine, selezionare il **set di crittografia del disco** da usare dall'elenco a discesa. 

### <a name="create-the-vm"></a>Creare la VM

È possibile creare una macchina virtuale da una versione di immagine e usare chiavi gestite dal cliente per crittografare i dischi. Quando si crea la macchina virtuale nel portale, nella scheda **dischi** selezionare crittografia dei **componenti inattivi con chiavi gestite dal cliente** o **crittografia doppia con chiavi gestite dalla piattaforma e gestite dal cliente** per il tipo di **crittografia**. È quindi possibile selezionare il set di crittografia dall'elenco a discesa.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sulla [crittografia dei dischi sul lato server](./windows/disk-encryption.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).
