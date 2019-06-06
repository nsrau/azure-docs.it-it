---
title: Crittografare dischi per i set di scalabilità Azure con Azure PowerShell | Microsoft Docs
description: Informazioni su come usare Azure PowerShell per crittografare istanze di macchine virtuali e i dischi collegati in set di scalabilità di macchine virtuali di Windows
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: cynthn
ms.openlocfilehash: a582a4787a4b215d82dcbff60be8853793f92c32
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66728363"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Crittografare il sistema operativo e dischi dati collegati in un macchina virtuale set di scalabilità con Azure PowerShell

Per proteggere i dati inattivi tramite la tecnologia di crittografia standard di settore, i set di scalabilità di macchine virtuali supportano Crittografia dischi di Azure. La crittografia può essere abilitata per i set di scalabilità di macchine virtuali Windows e Linux. Per altre informazioni, vedere [Crittografia dischi di Azure per Windows e Linux](../security/azure-security-disk-encryption.md).

Crittografia dischi di Azure è supportata per le risorse seguenti:
- Set di scalabilità creati con dischi gestiti. Non è tuttavia supportata per i set di scalabilità di dischi nativi (o non gestiti).
- Volumi di dati e sistema operativo nei set di scalabilità Windows. La disabilitazione della crittografia è supportata per volumi di dati e sistema operativo nei set di scalabilità Windows.
- Volumi di dati nei set di scalabilità Linux. Crittografia del disco del sistema operativo non è supportata attualmente per i set di scalabilità Linux.

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

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

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Abilitare la crittografia mediante chiave KEK per eseguire il wrapping della chiave

È anche possibile usare una chiave di crittografia per maggiore sicurezza durante la crittografia del set di scalabilità di macchine virtuali.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId –VolumeType "All"
```

> [!NOTE]
>  La sintassi per il valore del parametro disco-encryption-Key Vault è la stringa dell'identificatore completo:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> La sintassi per il valore del parametro di chiave di crittografia è l'URI completo per la chiave KEK come in:</br>
https://[keyvault-Name].Vault.Azure.NET/Keys/[kekname]/[kek-Unique-ID]

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

- In questo articolo è stato crittografato un set di scalabilità di macchine virtuali con Azure PowerShell. È anche possibile usare l'[interfaccia della riga di comando di Azure](virtual-machine-scale-sets-encrypt-disks-cli.md) o i modelli per [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox) o [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox).
- Se si vuole che la crittografia dischi di Azure applicate dopo il provisioning di un'altra estensione, è possibile usare [sequenziazione di estensione](virtual-machine-scale-sets-extension-sequencing.md). È possibile usare [questi esempi](../security/azure-security-disk-encryption-extension-sequencing.md#sample-azure-templates) per iniziare.
