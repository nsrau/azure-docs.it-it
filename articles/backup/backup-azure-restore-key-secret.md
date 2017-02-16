---
title: Ripristinare la chiave dell&quot;insieme di credenziali delle chiavi e il segreto per le macchine virtuali crittografate con Backup di Azure | Documentazione Microsoft
description: Informazioni su come ripristinare la chiave dell&quot;insieme di credenziali delle chiavi e il segreto in Backup di Azure usando PowerShell
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
ms.date: 10/18/2016
ms.author: pajosh
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0c5b2969ddc943b2b15826003f5a9e686e84e1c4


---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Ripristinare la chiave dell'insieme di credenziali delle chiavi e il segreto per le macchine virtuali crittografate con Backup di Azure
Questo articolo illustra l'uso di Backup di Azure per eseguire il ripristino delle macchine virtuali crittografate di Azure nel caso in cui la chiave e il segreto non siano presenti nell'insieme di credenziali delle chiavi. Questa procedura può essere usata anche per conservare una copia separata della chiave (chiave di crittografia della chiave) e del segreto (chiave di crittografia BitLocker) per la macchina virtuale ripristinata.

## <a name="pre-requisites"></a>Prerequisiti
1. **Backup delle macchine virtuali crittografate**: il backup delle macchine virtuali crittografate di Azure deve essere eseguito tramite Backup di Azure. Per informazioni dettagliate sul backup di macchine virtuali crittografate di Azure, vedere l'articolo relativo alla [gestione del backup e ripristino di macchine virtuali di Azure con PowerShell](backup-azure-vms-automation.md).
2. **Configurazione dell'insieme di credenziali delle chiavi di Azure**: assicurarsi che l'insieme di credenziali delle chiavi in cui eseguire il ripristino di chiavi e segreti sia presente. Per informazioni dettagliate sulla gestione dell'insieme di credenziali delle chiavi, vedere l'articolo [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md).

## <a name="setup-recovery-services-vault"></a>Configurare l'insieme di credenziali dei servizi di ripristino
Seguire questa procedura per accedere a PowerShell e impostare il contesto dell'insieme di credenziali dei servizi di ripristino.

### <a name="log-in-to-azure-powershell"></a>Accedere ad Azure PowerShell
Accedere all'account Azure tramite il cmdlet seguente

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Impostare il contesto dell'insieme di credenziali dei servizi di ripristino
Dopo aver eseguito l'accesso, usare il cmdlet seguente per ottenere l'elenco delle sottoscrizioni disponibili

```
PS C:\> Get-AzureRmSubscription
```

Selezionare la sottoscrizione in cui sono disponibili risorse

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

Impostare il contesto dell'insieme di credenziali usando l'insieme di credenziali dei servizi di ripristino in cui è stato abilitato il backup per le macchine virtuali crittografate

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>Ottenere un punto di ripristino
Selezionare un contenitore nell'insieme di credenziali che rappresenta la macchina virtuale crittografata di Azure

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

Usare questo contenitore per ottenere l'elemento di backup per la macchina virtuale corrispondente

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

Ottenere una matrice di punti di ripristino per l'elemento di backup selezionato nella variabile rp

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>Ripristinare la macchina virtuale crittografata
Seguire questa procedura per ripristinare la macchina virtuale crittografata, nonché la relativa chiave e il segreto.

### <a name="restore-key"></a>Ripristinare la chiave
La matrice $rp precedente viene presentata in ordine cronologico inverso, con il punto di ripristino più recente in corrispondenza dell'indice 0. Ad esempio: $rp [0] consente di selezionare il punto di ripristino più recente.

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> Al termine dell'esecuzione di questo cmdlet, nel computer viene generato un file BLOB nella cartella specificata. Il file BLOB rappresenta la chiave di crittografia della chiave in formato crittografato.
> 
> 

Ripristinare la chiave nell'insieme di credenziali delle chiavi usando il cmdlet seguente. 

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>Ripristinare il segreto
Ripristinare i dati del segreto dal punto di ripristino ottenuto in precedenza.

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> Il testo prima di vault.azure.net rappresenta il nome dell'insieme di credenziali delle chiavi originale. Il testo dopo secrets/ rappresenta il nome del segreto. 
> 
> 

Se si vuole usare lo stesso nome del segreto, è possibile ottenere il nome e il valore del segreto dall'output del cmdlet eseguito in precedenza. In caso contrario, aggiornare $secretname di seguito per usare il nuovo nome del segreto. 

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

Se è necessario ripristinare anche macchina virtuale, impostare i tag per il segreto. Il nome del tag DiskEncryptionKeyFileName deve contenere il nome del segreto che si intende usare. 

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> Il valore di DiskEncryptionKeyFileName è uguale al nome del segreto ottenuto in precedenza. È possibile ottenere il valore di DiskEncryptionKeyEncryptionKeyURL dall'insieme di credenziali delle chiavi dopo il ripristino delle chiavi, usando il cmdlet [Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx).    
> 
> 

Impostare di nuovo il segreto nell'insieme di credenziali delle chiavi

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>Ripristinare la macchina virtuale
Il cmdlet di PowerShell precedente permette di ripristinare la chiave e il segreto nell'insieme di credenziali delle chiavi, se è stato eseguito il backup della macchina virtuale crittografata tramite Backup di Azure. Successivamente, per ripristinare le macchine virtuali crittografate, vedere l'articolo relativo alla [gestione del backup e ripristino di macchine virtuali di Azure con PowerShell](backup-azure-vms-automation.md).




<!--HONumber=Nov16_HO3-->


