---
title: Anteprima - Creare una versione di immagine crittografata con le chiavi personali
description: Creare una versione di immagine in una raccolta di immagini condivise usando chiavi di crittografia gestite dal cliente.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/3/2020
ms.author: cynthn
ms.openlocfilehash: b19dab8dffaa0c9c888e8a9974a43cbb48006fd7
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94504323"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Anteprima: Usare le chiavi gestite dal cliente per crittografare le immagini

Le immagini in una raccolta di immagini condivise vengono archiviate come snapshot, quindi vengono crittografate automaticamente tramite la crittografia lato server. La crittografia lato server utilizza la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili. Anche la crittografia lato server è conforme allo standard FIPS 140-2. Per altre informazioni sui moduli di crittografia sottostanti di Azure Managed disks, vedere [API di crittografia: prossima generazione](/windows/desktop/seccng/cng-portal).

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia delle immagini oppure usare le proprie chiavi. È anche possibile usare entrambi insieme per la crittografia doppia. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dischi nelle immagini. 

La crittografia lato server tramite chiavi gestite dal cliente usa Azure Key Vault. È possibile importare [le chiavi RSA](../key-vault/keys/hsm-protected-keys.md) nell'insieme di credenziali delle chiavi o generare nuove chiavi rsa in Azure Key Vault.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo richiede che sia già stata impostata una crittografia del disco in ogni area in cui si vuole replicare l'immagine:

- Per usare solo una chiave gestita dal cliente, vedere gli articoli relativi all'abilitazione delle chiavi gestite dal cliente con la crittografia lato server usando il [portale di Azure](./disks-enable-customer-managed-keys-portal.md) o [PowerShell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Per usare le chiavi gestite dalla piattaforma e gestite dal cliente (per la crittografia doppia), vedere gli articoli relativi all'abilitazione della crittografia doppia inattiva tramite il [portale di Azure](./disks-enable-double-encryption-at-rest-portal.md) o [PowerShell](./windows/disks-enable-double-encryption-at-rest-powershell.md).

   > [!IMPORTANT]
   > [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates)Per accedere alla portale di Azure, è necessario usare il collegamento. La crittografia a doppio inattivo non è attualmente visibile nel portale di Azure pubblico a meno che non si usi tale collegamento.

## <a name="limitations"></a>Limitazioni

Quando si usano chiavi gestite dal cliente per la crittografia delle immagini in una raccolta di immagini condivise, si applicano le limitazioni seguenti:   

- I set di chiavi di crittografia devono trovarsi nella stessa sottoscrizione dell'immagine.

- I set di chiavi di crittografia sono risorse regionali, quindi ogni area richiede un set di chiavi di crittografia diverso.

- Non è possibile copiare o condividere immagini che usano chiavi gestite dal cliente. 

- Dopo aver usato le proprie chiavi per crittografare un disco o un'immagine, non è possibile tornare all'uso delle chiavi gestite dalla piattaforma per la crittografia di tali dischi o immagini.


> [!IMPORTANT]
> La crittografia tramite chiavi gestite dal cliente è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene fornita senza un contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Per l'anteprima pubblica, è prima di tutto necessario registrare la funzionalità:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Sono necessari alcuni minuti per il completamento della registrazione. Usare `Get-AzProviderFeature` per verificare lo stato della registrazione della funzionalità:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando `RegistrationState` restituisce `Registered` , è possibile passare al passaggio successivo.

Controllare la registrazione del provider. Verificare che restituisca `Registered`.

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se non viene restituito `Registered` , usare il codice seguente per registrare i provider:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Per specificare un set di crittografia del disco per una versione dell'immagine, usare  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) con il `-TargetRegion` parametro: 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$eastUS2osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet'}

$eastUS2dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet1';Lun=1}

$eastUS2dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myEastUS2DESet2';Lun=2}

$eastUS2DataDiskImageEncryptions = @($eastUS2dataDiskImageEncryption1,$eastUS2dataDiskImageEncryption2)

$encryption2 = @{OSDiskImage=$eastUS2osDiskImageEncryption;DataDiskImages=$eastUS2DataDiskImageEncryptions}

$region2 = @{Name='East US 2';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption2}

$targetRegion = @($region1, $region2)


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

È possibile creare una macchina virtuale (VM) da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è identica alla creazione di una macchina virtuale [generalizzata](vm-generalized-image-version-powershell.md) o [specializzata](vm-specialized-image-version-powershell.md) da un'immagine. Usare il set di parametri esteso e aggiungere `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` alla configurazione della macchina virtuale.

Per i dischi dati, aggiungere il `-DiskEncryptionSetId $setID` parametro quando si usa [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Per l'anteprima pubblica, è prima di tutto necessario registrarsi per la funzionalità. La registrazione richiede circa 30 minuti.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verificare lo stato della registrazione della funzionalità:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando il codice `"state": "Registered"` viene restituito, è possibile passare al passaggio successivo.

Controllare la registrazione:

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se non è registrato, eseguire il comando seguente:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Per specificare un set di crittografia del disco per una versione dell'immagine, usare [AZ Image Gallery create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) con il `--target-region-encryption` parametro. Il formato per `--target-region-encryption` è un elenco delimitato da virgole di chiavi per la crittografia del sistema operativo e dei dischi dati. L'aspetto dovrebbe risultare simile al seguente: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Se l'origine per il disco del sistema operativo è un disco gestito o una macchina virtuale, usare `--managed-image` per specificare l'origine per la versione dell'immagine. In questo esempio l'origine è un'immagine gestita con un disco del sistema operativo e un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs eastus2 \
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se l'origine per il disco del sistema operativo è uno snapshot, usare `--os-snapshot` per specificarlo. Se sono presenti snapshot del disco dati che devono anche essere parte della versione dell'immagine, aggiungerli. Utilizzare `--data-snapshot-luns` per specificare il lun e utilizzare `--data-snapshots` per specificare gli snapshot.

In questo esempio le origini sono costituite da snapshot del disco. Sono presenti un disco del sistema operativo e un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs eastus\
   --target-region-encryption WestUSDiskEncryptionSet1,0,WestUSDiskEncryptionSet2 EastUS2DiskEncryptionSet1,0,EastUS2DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Creare la VM

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è identica alla creazione di una macchina virtuale [generalizzata](vm-generalized-image-version-cli.md) o [specializzata](vm-specialized-image-version-cli.md) da un'immagine. È sufficiente aggiungere il `--os-disk-encryption-set` parametro con l'ID del set di crittografia. Per i dischi dati, aggiungere `--data-disk-encryption-sets` con un elenco delimitato da spazi dei set di crittografia del disco per i dischi dati.


## <a name="portal"></a>Portale

Quando si crea la versione dell'immagine nel portale, è possibile usare la scheda **crittografia** per applicare i set di crittografia di archiviazione.

> [!IMPORTANT]
> Per utilizzare la crittografia doppia, è necessario utilizzare il collegamento [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) per accedere al portale di Azure. La crittografia a doppio inattivo non è attualmente visibile nel portale di Azure pubblico a meno che non si usi tale collegamento.


1. Nella pagina **Crea versione immagine** selezionare la scheda **crittografia** .
2. In **tipo di crittografia** selezionare **crittografia dei componenti inattivi con una chiave gestita dal cliente o una** **doppia crittografia con chiavi gestite dalla piattaforma e gestite dal cliente**. 
3. Per ogni disco nell'immagine, selezionare un set di crittografia dall'elenco a discesa **Disk Encryption set** . 

### <a name="create-the-vm"></a>Creare la macchina virtuale

È possibile creare una macchina virtuale da una versione di immagine e usare chiavi gestite dal cliente per crittografare i dischi. Quando si crea la macchina virtuale nel portale, nella scheda **dischi** selezionare crittografia dei valori inattivi **con chiavi gestite dal cliente** o **crittografia doppia con chiavi gestite dalla piattaforma e chiavi gestite dal cliente** per il **tipo di crittografia**. È quindi possibile selezionare il set di crittografia dall'elenco a discesa.

## <a name="next-steps"></a>Passaggi successivi

Vedere altre informazioni sulla [crittografia dei dischi sul lato server](./windows/disk-encryption.md).

Per informazioni su come fornire informazioni sul piano di acquisto, vedere [fornire informazioni sul piano di acquisto di Azure Marketplace durante la creazione di immagini](marketplace-images.md).
