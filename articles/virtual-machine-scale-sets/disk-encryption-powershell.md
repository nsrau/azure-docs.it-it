---
title: Crittografare dischi per i set di scalabilità Azure con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per crittografare istanze di macchine virtuali e i dischi collegati in set di scalabilità di macchine virtuali di Windows
services: virtual-machine-scale-sets
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: 6c228dd3f2e408c97e684a2cc1490903ac7a2eb0
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530823"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Crittografare il sistema operativo e i dischi dati collegati in un set di scalabilità di macchine virtuali con Azure PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script.  Questo articolo illustra come usare Azure PowerShell per creare e crittografare un set di scalabilità di macchine virtuali. Per altre informazioni sull'applicazione di crittografia dischi di Azure a un set di scalabilità di macchine virtuali, vedere [crittografia dischi di Azure per i set di scalabilità di macchine virtuali](disk-encryption-overview.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Creare un insieme di credenziali delle chiavi di Azure abilitato per la crittografia dei dischi

L'insieme di credenziali delle chiavi di Azure consente di archiviare chiavi, chiavi private o password da implementare in tutta sicurezza in applicazioni e servizi. Le chiavi di crittografia vengono archiviate nell'insieme di credenziali delle chiavi di Azure che usano la protezione del software oppure è possibile importare o generare le chiavi in moduli di protezione hardware certificati per gli standard FIPS 140-2 di livello 2. Queste chiavi di crittografia vengono usate per crittografare e decrittografare i dischi virtuali collegati alla VM. È possibile esercitare il controllo su queste chiavi di crittografia e sul loro uso.

Creare un insieme di credenziali delle chiavi con [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Per consentire l'uso dell'insieme di credenziali delle chiavi per la crittografia dei dischi, impostare il parametro *EnabledForDiskEncryption*. L'esempio seguente definisce anche le variabili per il nome del gruppo di risorse, il nome dell'insieme di credenziali delle chiavi e la posizione. Specificare il nome dell'insieme di credenziali delle chiavi:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Usare un insieme di credenziali delle chiavi esistente

Questo passaggio è necessario solo se si vuole usare un insieme di credenziali delle chiavi già esistente con la crittografia dei dischi. Ignorare questo passaggio se si è creato un insieme di credenziali delle chiavi nella sezione precedente.

È possibile abilitare un insieme di credenziali delle chiavi esistente nella stessa sottoscrizione e nella stessa area del set di scalabilità per la crittografia dei dischi con [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Definire il nome dell'insieme di credenziali delle chiavi esistente nella variabile *$vaultName*, come segue:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Creare un set di scalabilità

Impostare prima di tutto nome utente e password dell'amministratore delle istanze di macchine virtuali con il comando [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Creare ora un set di scalabilità di macchine virtuali con [New-AzVmss](/powershell/module/az.compute/new-azvmss). Per distribuire il traffico alle singole istanze di macchine virtuali, viene creato anche un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico include regole per la distribuzione del traffico sulla porta TCP 80, oltre che per consentire il traffico di Desktop remoto sulla porta TCP 3389 e la comunicazione remota di PowerShell sulla porta TCP 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
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

Per crittografare le istanze di macchine virtuali in un set di scalabilità, ottenere prima di tutto alcune informazioni sull'URI e l'ID di risorsa dell'insieme di credenziali delle chiavi con [Get-AzKeyVault](/powershell/module/az.keyvault/Get-AzKeyVault). Queste variabili vengono quindi usate per avviare il processo di crittografia con [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension):


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

Quando richiesto, digitare *y* per continuare il processo di crittografia dei dischi nelle istanze di macchine virtuali del set di scalabilità.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Abilitare la crittografia con KEK per eseguire il wrapping della chiave

È anche possibile usare una chiave di crittografia della chiave per la sicurezza aggiuntiva durante la crittografia del set di scalabilità di macchine virtuali.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  La sintassi per il valore del parametro Disk-Encryption-Vault è la stringa dell'identificatore completo:</br>
/Subscriptions/[Subscription-ID-GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nome-insieme-credenziali]</br></br>
> La sintassi per il valore del parametro Key-Encryption-Key è l'URI completo di KEK come in:</br>
https:///[nome-insieme-credenziali]. Vault. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Verificare lo stato della crittografia

Per controllare lo stato della crittografia dei dischi, usare [Get-AzVmssDiskEncryption](/powershell/module/az.compute/Get-AzVmssDiskEncryption):


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
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

Se non si vogliono più usare i dischi delle istanze di macchine virtuali crittografate, è possibile disabilitare la crittografia con [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption), come segue:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Passaggi successivi

- In questo articolo è stato crittografato un set di scalabilità di macchine virtuali con Azure PowerShell. È anche possibile usare l' [interfaccia](disk-encryption-cli.md) della riga di comando di Azure o i [modelli di Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Se si vuole che crittografia dischi di Azure venga applicato dopo il provisioning di un'altra estensione, è possibile usare la [sequenziazione delle estensioni](virtual-machine-scale-sets-extension-sequencing.md).
