---
title: Ripristinare la chiave dell'insieme di credenziali delle chiavi e il segreto per le macchine virtuali crittografate con Backup di Azure | Documentazione Microsoft
description: Informazioni su come ripristinare la chiave dell'insieme di credenziali delle chiavi e il segreto in Backup di Azure usando PowerShell
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f2db3449187d655248b13198b268841052570626
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Ripristinare la chiave dell'insieme di credenziali delle chiavi e il segreto per le macchine virtuali crittografate con Backup di Azure
Questo articolo illustra l'uso di Backup di Azure per eseguire il ripristino delle macchine virtuali crittografate di Azure nel caso in cui la chiave e il segreto non siano presenti nell'insieme di credenziali delle chiavi. Questa procedura può essere usata anche per conservare una copia separata della chiave (chiave di crittografia della chiave) e del segreto (chiave di crittografia BitLocker) per la macchina virtuale ripristinata.

## <a name="prerequisites"></a>Prerequisiti
* **Backup delle macchine virtuali crittografate**: il backup delle macchine virtuali crittografate di Azure deve essere eseguito tramite Backup di Azure. Per informazioni dettagliate sul backup di macchine virtuali crittografate di Azure, vedere l'articolo relativo alla [gestione del backup e ripristino di macchine virtuali di Azure con PowerShell](backup-azure-vms-automation.md).
* **Configurazione dell'insieme di credenziali delle chiavi di Azure**: assicurarsi che l'insieme di credenziali delle chiavi in cui eseguire il ripristino di chiavi e segreti sia presente. Per informazioni dettagliate sulla gestione dell'insieme di credenziali delle chiavi, vedere l'articolo [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).
* **Ripristino del disco**: assicurarsi di aver attivato il processo di ripristino per ripristinare i dischi delle macchine virtuali crittografate usando le [procedure di PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm). Questo processo, infatti, genera un file JSON nell'account di archiviazione contenente le chiavi e i segreti della macchina virtuale crittografata da ripristinare.

## <a name="get-key-and-secret-from-azure-backup"></a>Ottenere la chiave e il segreto da Backup di Azure

> [!NOTE]
> Dopo aver ripristinato il disco per la macchina virtuale crittografata, verificare che:
> 1. In $details siano specificati i dettagli del processo di ripristino del disco, come indicato nella [procedura relativa a PowerShell della sezione Ripristinare i dischi](backup-azure-vms-automation.md#restore-an-azure-vm)
> 2. La macchina virtuale venga creata dai dischi ripristinati solo **dopo aver ripristinato la chiave e il segreto nell'insieme di credenziali delle chiavi**.
>
>

Ricercare i dettagli del processo nelle proprietà del disco ripristinato.

```
PS C:\> $properties = $details.properties
PS C:\> $storageAccountName = $properties["Target Storage Account Name"]
PS C:\> $containerName = $properties["Config Blob Container Name"]
PS C:\> $encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Impostare il contesto di archiviazione di Azure e ripristinare il file di configurazione JSON contenente i dettagli relativi alla chiave e al segreto della macchina virtuale crittografata.

```
PS C:\> Set-AzureRmCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
PS C:\> $destination_path = 'C:\vmencryption_config.json'
PS C:\> Get-AzureStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
PS C:\> $encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>Ripristinare la chiave
Dopo aver generato il file JSON nel percorso di destinazione indicato in precedenza, generare il file BLOB della chiave dal file JSON e compilarlo in modo da ripristinare il cmdlet della chiave e reinserire la chiave di crittografia della chiave (KEK) nell'insieme di credenziali delle chiavi.

```
PS C:\> $keyDestination = 'C:\keyDetails.blob'
PS C:\> [io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>Ripristinare il segreto
Usare il file JSON generato in precedenza per ottenere il valore e il nome del segreto e compilarlo in modo da impostare il cmdlet del segreto per reinserire il segreto (BEK) nell'insieme di credenziali delle chiavi. **Usare questi cmdlet se la macchina virtuale è crittografata con BEK e KEK.**

```
PS C:\> $secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

Se la macchina virtuale è **crittografata solo con BEK**, generare il file di BLOB del segreto dal file JSON e compilarlo in modo da ripristinare il cmdlet del segreto per reinserire il segreto (BEK) nell'insieme di credenziali chiave di ripristino.

```
PS C:\> $secretDestination = 'C:\secret.blob'
PS C:\> [io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
PS C:\> Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
```

> [!NOTE]
> 1. È possibile ottenere il valore di $secretname facendo riferimento all'output di $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl e usando il testo presente dopo secrets/. Se, ad esempio, l'URL del segreto di output è https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163, il nome del segreto sarà B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Il valore del tag DiskEncryptionKeyFileName è uguale al nome del segreto.
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>Creare una macchina virtuale dal disco ripristinato
Se il backup della macchina virtuale crittografata è stato eseguito tramite Backup di Azure, i cmdlet di PowerShell elencati in precedenza consentono di ripristinare chiave e segreto nell'insieme di credenziali delle chiavi. Dopo avere eseguito il ripristino, per creare le macchine virtuali crittografate dal disco, dalla chiave e dal segreto ripristinato, vedere l'articolo relativo alla [gestione del backup e del ripristino di macchine virtuali di Azure con PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="legacy-approach"></a>Approccio legacy
L'approccio sopra descritto potrà essere adottato per tutti i punti di ripristino. L'approccio precedente, che prevedeva di recuperare le informazioni sulla chiave e sul segreto dal punto di ripristino, può essere ancora adottato per i punti di ripristino precedenti all'11 luglio 2017 per le macchine virtuali crittografate con BEK e KEK. Dopo aver completato il processo di ripristino del disco per le macchine virtuali crittografate applicando la [procedura relativa a PowerShell](backup-azure-vms-automation.md#restore-an-azure-vm), assicurarsi che in $rp sia inserito un valore valido.

### <a name="restore-key"></a>Ripristinare la chiave
Usare i cmdlet seguenti per ottenere informazioni sulla chiave di crittografia della chiave dal punto di ripristino e compilarli in modo da ripristinare il cmdlet della chiave e reinserirlo nell'insieme di credenziali delle chiavi.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
PS C:\> Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>Ripristinare il segreto
Usare i cmdlet seguenti per ottenere informazioni sulla chiave di crittografia a blocchi dal punto di ripristino e compilarli in modo da impostare il cmdlet del segreto e reinserirlo nell'insieme di credenziali delle chiavi.

```
PS C:\> $secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
PS C:\> Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> 1. È possibile ottenere il valore di $secretname facendo riferimento all'output di $rp1.KeyAndSecretDetails.SecretUrl e usando il testo presente dopo secrets/. Se, ad esempio, l'URL del segreto di output è https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163, il nome del segreto sarà B3284AAA-DAAA-4AAA-B393-60CAA848AAAA
> 2. Il valore del tag DiskEncryptionKeyFileName è uguale al nome del segreto.
> 3. È possibile ottenere il valore di DiskEncryptionKeyEncryptionKeyURL dall'insieme di credenziali delle chiavi dopo il ripristino delle chiavi, usando il cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).
>
>

## <a name="next-steps"></a>Passaggi successivi
Dopo aver ripristinato la chiave e il segreto nell'insieme di credenziali delle chiavi, vedere l'articolo relativo alla [gestione delle procedure di backup e ripristino di macchine virtuali di Azure con PowerShell](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) per creare le macchine virtuali crittografate dal disco, dalla chiave e dal segreto ripristinati.
