---
title: Creare e crittografare una VM Windows con Azure PowerShell
description: In questa guida introduttiva si apprenderà come usare Azure PowerShell per creare e crittografare una macchina virtuale Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: b5e5b44742c85591b913b94e622c76a2aba111ce
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245711"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Guida introduttiva: Creare e crittografare una macchina virtuale Windows in Azure con PowerShell

Il modulo Azure PowerShell viene usato per creare e gestire le risorse di Azure dalla riga di comando di PowerShell o negli script. Questa guida introduttiva illustra come usare il modulo Azure PowerShell per creare una macchina virtuale (VM) Windows, creare un insieme di credenziali delle chiavi per l'archiviazione delle chiavi di crittografia e crittografare la macchina virtuale. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una macchina virtuale Azure con [New-AzVM](/powershell/module/az.compute/new-azvm). È necessario fornire le credenziali al cmdlet. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

La distribuzione della macchina virtuale richiederà alcuni minuti. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Creare un insieme di credenziali delle chiavi configurato per le chiavi di crittografia

Crittografia dischi di Azure archivia la chiave di crittografia in Azure Key Vault. Creare un insieme di credenziali delle chiavi con [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Per consentire all'insieme di credenziali delle chiavi di archiviare le chiavi di crittografia, usare il parametro -EnabledForDiskEncryption.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. L'esempio seguente crea un insieme di credenziali delle chiavi denominato *myKV*, ma è necessario assegnare un nome diverso al proprio.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>Crittografare la macchina virtuale

Crittografare la macchina virtuale con [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension richiede alcuni valori dell'oggetto dell'insieme di credenziali delle chiavi. È possibile ottenere questi valori passando il nome univoco dell'insieme di credenziali delle chiavi in [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Dopo alcuni minuti il processo restituirà quanto segue:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

È possibile verificare il processo di crittografia eseguendo [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus).

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Quando è abilitata la crittografia, nell'output restituito verrà visualizzato quanto segue:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate con il cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup):

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare una macchina virtuale, come creare un insieme di credenziali delle chiavi abilitato per le chiavi di crittografia e come crittografare una macchina virtuale.  Passare all'articolo successivo per altre informazioni sui prerequisiti di Crittografia dischi di Azure per le macchine virtuali IaaS.

> [!div class="nextstepaction"]
> [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)