---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 0d78e48fead7b1f53e67860e6be8fe6d77469e87
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/20/2020
ms.locfileid: "76280603"
---
Azure Esplora dati crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con le chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile fornire chiavi gestite dal cliente da usare per la crittografia dei dati. Le chiavi gestite dal cliente devono essere archiviate in un [Azure Key Vault](/azure/key-vault/key-vault-overview). È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure è possibile usare un'API Azure Key Vault per generare chiavi. Il cluster Esplora dati di Azure e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area, ma possono trovarsi in sottoscrizioni diverse. Per una spiegazione dettagliata delle chiavi gestite dal cliente, vedere [chiavi gestite dal cliente con Azure Key Vault](/azure/storage/common/storage-service-encryption). Questo articolo illustra come configurare le chiavi gestite dal cliente.

Per configurare le chiavi gestite dal cliente con Azure Esplora dati, è necessario [impostare due proprietà nell'insieme di credenziali delle chiavi](/azure/key-vault/key-vault-ovw-soft-delete): **eliminazione** temporanea e non **ripulitura**. Queste proprietà non sono abilitate per impostazione predefinita. Per abilitare queste proprietà, usare [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) o l'interfaccia della riga di comando di [Azure](/azure/key-vault/key-vault-soft-delete-cli). Sono supportate solo le chiavi RSA e le dimensioni della chiave 2048.

> [!NOTE]
> La crittografia dei dati tramite chiavi gestite dal cliente non è supportata nei [cluster leader e di seguito](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Assegnare un'identità al cluster

Per abilitare le chiavi gestite dal cliente per il cluster, assegnare innanzitutto un'identità gestita assegnata dal sistema al cluster. Questa identità gestita verrà usata per concedere le autorizzazioni del cluster per accedere all'insieme di credenziali delle chiavi. Per configurare le identità gestite assegnate dal sistema, vedere [identità gestite](/azure/data-explorer/managed-identities).

## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

Per creare un nuovo insieme di credenziali delle chiavi usando PowerShell, chiamare [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). L'insieme di credenziali delle chiavi usato per archiviare le chiavi gestite dal cliente per la crittografia Esplora dati di Azure deve avere due impostazioni di protezione della chiave abilitate, l' **eliminazione** temporanea e non la **ripulitura**. Sostituire i valori segnaposto tra parentesi quadre con valori personalizzati nell'esempio riportato di seguito.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Configurare quindi i criteri di accesso per l'insieme di credenziali delle chiavi in modo che il cluster disponga delle autorizzazioni di accesso. In questo passaggio si userà l'identità gestita assegnata dal sistema precedentemente assegnata al cluster. Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Sostituire i valori segnaposto tra parentesi quadre con i propri valori e usare le variabili definite negli esempi precedenti.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una nuova chiave nell'insieme di credenziali delle chiavi. Per creare una nuova chiave, chiamare [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Sostituire i valori segnaposto tra parentesi quadre con i propri valori e usare le variabili definite negli esempi precedenti.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
