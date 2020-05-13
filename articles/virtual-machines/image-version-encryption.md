---
title: 'Anteprima: creare una versione di immagine crittografata con le proprie chiavi'
description: Creare una versione di immagine in una raccolta di immagini condivise usando chiavi di crittografia gestite dal cliente.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/06/2020
ms.author: cynthn
ms.openlocfilehash: aeacfdc07e5349dfce45b209da1d78bddf870f33
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83269581"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Anteprima: usare chiavi gestite dal cliente per la crittografia delle immagini

Le immagini della raccolta vengono archiviate come dischi gestiti, in modo che vengano crittografate automaticamente tramite la crittografia lato server. La crittografia lato server utilizza la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. Per altre informazioni sui moduli di crittografia sottostanti di Azure Managed disks, vedere [API di crittografia: prossima generazione](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia delle immagini oppure è possibile gestire la crittografia usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dischi nelle immagini. 

La crittografia lato server con chiavi gestite dal cliente usa Azure Key Vault. È possibile importare [le chiavi RSA](../key-vault/keys/hsm-protected-keys.md) nella Key Vault o generare nuove chiavi rsa in Azure Key Vault.

Per usare le chiavi gestite dal cliente per le immagini, è necessario prima di tutto un Azure Key Vault. Si crea quindi un set di crittografia del disco. Il set di crittografia del disco viene quindi utilizzato per la creazione di versioni dell'immagine.

Per altre informazioni sulla creazione e l'uso di set di crittografia del disco, vedere [chiavi gestite dal cliente](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#customer-managed-keys).

## <a name="limitations"></a>Limitazioni

Esistono diverse limitazioni quando si usano chiavi gestite dal cliente per la crittografia delle immagini della raccolta di immagini condivise:  

- I set di chiavi di crittografia devono trovarsi nella stessa sottoscrizione e nella stessa area dell'immagine.

- Non è possibile condividere immagini che usano chiavi gestite dal cliente. 

- Non è possibile replicare immagini che usano chiavi gestite dal cliente in altre aree.

- Dopo aver usato le proprie chiavi per crittografare un disco o un'immagine, non è possibile tornare all'uso delle chiavi gestite dalla piattaforma per la crittografia di tali dischi o immagini.


> [!IMPORTANT]
> La crittografia con chiavi gestite dal cliente è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="powershell"></a>PowerShell

Per l'anteprima pubblica, è necessario prima registrare la funzionalità.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Sono necessari alcuni minuti per il completamento della registrazione. Usare Get-AzProviderFeature per verificare lo stato della registrazione della funzionalità.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Quando RegistrationState restituisce registrato, è possibile passare al passaggio successivo.

Controllare la registrazione del provider. Verificare che restituisca `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Se non viene restituito `Registered` , utilizzare il comando seguente per registrare i provider:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Per specificare una crittografia del disco impostata su per una versione dell'immagine, usare [New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) con il `-TargetRegion` parametro. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @{$region1}


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

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è identica alla creazione di una macchina virtuale [generalizzata](vm-generalized-image-version-powershell.md) o [specializzata](vm-specialized-image-version-powershell.md) da un'immagine, è necessario usare il set di parametri esteso e aggiungere `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` alla configurazione della macchina virtuale.

Per i dischi dati, è necessario aggiungere il `-DiskEncryptionSetId $setID` parametro quando si usa [Add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk).


## <a name="cli"></a>CLI 

Per l'anteprima pubblica, è necessario prima registrare la funzionalità.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Verificare lo stato della registrazione della funzionalità.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Quando viene restituito `"state": "Registered"` , è possibile passare al passaggio successivo.

Controllare la registrazione.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Se non è registrato, eseguire le operazioni seguenti:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Per specificare una crittografia del disco impostata su per una versione dell'immagine, usare [AZ Image Gallery create-Image-Version](/cli/azure/sig/image-version#az-sig-image-version-create) con il `--target-region-encryption` parametro. Il formato per `--target-region-encryption` è un elenco di chiavi separate da spazi per la crittografia del sistema operativo e dei dischi dati. L'aspetto dovrebbe risultare simile al seguente: `<encryption set for the OS disk>,<Lun number of the data disk>, <encryption set for the data disk>, <Lun number for the second data disk>, <encryption set for the second data disk>`. 

Se l'origine per il disco del sistema operativo è un disco gestito o una macchina virtuale, usare `--managed-image` per specificare l'origine per la versione dell'immagine. In questo esempio l'origine è un'immagine gestita con un disco del sistema operativo e un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Se l'origine per il disco del sistema operativo è uno snapshot, usare `--os-snapshot` per specificare il disco del sistema operativo. Se sono presenti snapshot del disco dati che devono anche essere parte della versione dell'immagine, aggiungerli usando `--data-snapshot-luns` per specificare il lun e `--data-snapshots` per specificare gli snapshot.

In questo esempio, le origini sono snapshot del disco. È presente un disco del sistema operativo e anche un disco dati in LUN 0. Il disco del sistema operativo verrà crittografato con DiskEncryptionSet1 e il disco dati verrà crittografato con DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot"
   --data-snapshot-luns 0
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot"
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>Creare la VM

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. La sintassi è identica alla creazione di una macchina virtuale [generalizzata](vm-generalized-image-version-cli.md) o [specializzata](vm-specialized-image-version-cli.md) da un'immagine. è sufficiente aggiungere il `--os-disk-encryption-set` parametro con l'ID del set di crittografia. Per i dischi dati, aggiungere `--data-disk-encryption-sets` con un elenco delimitato da spazi dei set di crittografia del disco per i dischi dati.


## <a name="portal"></a>Portale

Quando si crea la versione dell'immagine nel portale, è possibile usare la scheda **crittografia** per immettere le informazioni sui set di crittografia di archiviazione.

1. Nella pagina **Crea una versione immagine** selezionare la scheda **crittografia** .
2. In **tipo di crittografia**selezionare **crittografia dei componenti inattivi con una chiave gestita dal cliente**. 
3. Per ogni disco nell'immagine, selezionare il **set di crittografia del disco** da usare dall'elenco a discesa. 

### <a name="create-the-vm"></a>Creare la VM

È possibile creare una macchina virtuale da una raccolta di immagini condivise e usare chiavi gestite dal cliente per crittografare i dischi. Quando si crea la macchina virtuale nel portale, nella scheda **dischi** selezionare crittografia dei **componenti inattivi con chiavi gestite dal cliente** per il **tipo di crittografia**. È quindi possibile selezionare il set di crittografia dall'elenco a discesa.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [crittografia del disco sul lato server](/windows/disk-encryption.md)).
