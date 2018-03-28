---
title: Crittografia dischi di Azure per set di scalabilità di macchine virtuali | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per crittografare istanze di macchine virtuali e i dischi collegati in set di scalabilità di macchine virtuali
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: iainfou
ms.openlocfilehash: 856d4bc7dd636b3a2f3d072a10989cafd7efd6a6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set"></a>Crittografare il disco del sistema operativo e i dischi dati collegati in un set di scalabilità di macchine virtuali
Per proteggere i dati inattivi tramite la tecnologia di crittografia standard di settore, i set di scalabilità di macchine virtuali supportano Crittografia dischi di Azure. La crittografia può essere abilitata per i set di scalabilità di macchine virtuali Windows e Linux. Per altre informazioni, vedere [Crittografia dischi di Azure per Windows e Linux](../security/azure-security-disk-encryption.md).

> [!NOTE]
>  Crittografia dischi di Azure per i set di scalabilità di macchine virtuali è attualmente disponibile come anteprima in tutte le aree pubbliche di Azure. 
>
> Le operazioni di creazione di una nuova immagine e di aggiornamento delle macchine virtuali dei set di scalabilità non sono supportate nell'anteprima corrente. Nella versione di anteprima è consigliabile usare la crittografia dei set di scalabilità solo in ambienti di test. Non abilitare la crittografia dei dischi in ambienti di produzione in cui può essere necessario aggiornare un'immagine del sistema operativo.

Crittografia dischi di Azure è supportata per le risorse seguenti:
- Set di scalabilità creati con dischi gestiti. Non è tuttavia supportata per i set di scalabilità di dischi nativi (o non gestiti).
- Volumi di dati e sistema operativo nei set di scalabilità Windows. La disabilitazione della crittografia è supportata per volumi di dati e sistema operativo nei set di scalabilità Windows.
- Volumi di dati nei set di scalabilità Linux. Nell'anteprima corrente la crittografia del disco del sistema operativo NON è supportata per i set di scalabilità Linux.


## <a name="prerequisites"></a>Prerequisiti
Questo articolo richiede il modulo Azure PowerShell 5.3.0 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

Registrare la propria sottoscrizione di Azure per l'anteprima della crittografia dischi per i set di scalabilità di macchine virtuali con [Register-AzureRmProviderFeature](/powershell/module/azurerm.resources/register-azurermproviderfeature): 

```powershell
Login-AzureRmAccount
Register-AzureRmProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```

Attendere circa 10 minuti fino a quando non viene restituito lo stato *Registrato* dal comando [Get-AzureRmProviderFeature](/powershell/module/AzureRM.Resources/Get-AzureRmProviderFeature), quindi registrare il nuovo il provider `Microsoft.Compute`: 

```powershell
Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
```


## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creare un insieme di credenziali delle chiavi di Azure abilitato per la crittografia dei dischi
L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso.

Creare un insieme di credenziali delle chiavi con [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault). Per consentire l'uso dell'insieme di credenziali delle chiavi per la crittografia dei dischi, impostare il parametro *EnabledForDiskEncryption*. L'esempio seguente definisce anche le variabili per il nome del gruppo di risorse, il nome dell'insieme di credenziali delle chiavi e la posizione. Specificare il nome dell'insieme di credenziali delle chiavi:

```powershell
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzureRmResourceGroup -Name $rgName -Location $location
New-AzureRmKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```


### <a name="use-an-existing-key-vault"></a>Usare un insieme di credenziali delle chiavi esistente
Questo passaggio è necessario solo se si vuole usare un insieme di credenziali delle chiavi già esistente con la crittografia dei dischi. Ignorare questo passaggio se si è creato un insieme di credenziali delle chiavi nella sezione precedente.

È possibile abilitare un insieme di credenziali delle chiavi esistente nella stessa sottoscrizione e nella stessa area del set di scalabilità per la crittografia dei dischi con [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/AzureRM.KeyVault/Set-AzureRmKeyVaultAccessPolicy). Definire il nome dell'insieme di credenziali delle chiavi esistente nella variabile *$vaultName*, come segue:

```powershell
$vaultName="myexistingkeyvault"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```


## <a name="create-a-scale-set"></a>Creare un set di scalabilità
Impostare prima di tutto nome utente e password dell'amministratore delle istanze di macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Ora si può creare un set di scalabilità di macchine virtuali con [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985:

```powershell
$vmssName="myScaleSet"

New-AzureRmVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```


## <a name="enable-encryption"></a>Abilitare la crittografia
Per crittografare le istanze di macchine virtuali in un set di scalabilità, ottenere prima di tutto alcune informazioni sull'URI e l'ID di risorsa dell'insieme di credenziali delle chiavi con [Get-AzureRmKeyVault](/powershell/module/AzureRM.KeyVault/Get-AzureRmKeyVault). Queste variabili vengono quindi usate per avviare il processo di crittografia con [Set-AzureRmVmssDiskEncryptionExtension](/powershell/module/AzureRM.Compute/Set-AzureRmVmssDiskEncryptionExtension):

```powershell
$diskEncryptionKeyVaultUrl=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzureRmKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzureRmVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Quando richiesto, digitare *y* per continuare il processo di crittografia dei dischi nelle istanze di macchine virtuali del set di scalabilità.


## <a name="check-encryption-progress"></a>Verificare lo stato della crittografia
Per controllare lo stato della crittografia dei dischi, usare [Get-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Get-AzureRmVmssDiskEncryption):

```powershell
Get-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Quando le istanze di macchine virtuali sono crittografate, il codice *EncryptionSummary* riporta *ProvisioningState/succeeded*, come mostrato nell'output di esempio seguente:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```


## <a name="disable-encryption"></a>Disabilitare la crittografia
Se non si vogliono più usare i dischi delle istanze di macchine virtuali crittografate, è possibile disabilitare la crittografia con [Disable-AzureRmVmssDiskEncryption](/powershell/module/AzureRM.Compute/Disable-AzureRmVmssDiskEncryption), come segue:

```powershell
Disable-AzureRmVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```


## <a name="next-steps"></a>Passaggi successivi
In questo articolo è stato crittografato un set di scalabilità di macchine virtuali con Azure PowerShell. È anche possibile usare l'[interfaccia della riga di comando di Azure 2.0](virtual-machine-scale-sets-encrypt-disks-cli.md) o i modelli per [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
