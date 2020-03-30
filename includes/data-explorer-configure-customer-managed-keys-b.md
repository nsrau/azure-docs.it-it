---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297907"
---
## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

Per creare un nuovo insieme di credenziali delle chiavi tramite PowerShell, chiamare [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). L'insieme di credenziali delle chiavi usato per archiviare le chiavi gestite dal cliente per la crittografia di Azure Data Explorer deve avere due impostazioni di protezione delle chiavi abilitate, **Eliminazione temporanea** e **Non eliminare**. Sostituire i valori segnaposto tra parentesi quadre con valori personalizzati nell'esempio seguente.

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali delle chiavi

Configurare quindi i criteri di accesso per l'insieme di credenziali delle chiavi in modo che il cluster disponga delle autorizzazioni per accedervi. In questo passaggio verrà utilizzata l'identità gestita assegnata dal sistema assegnata in precedenza al cluster. Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Sostituire i valori segnaposto tra parentesi quadre con valori personalizzati e utilizzare le variabili definite negli esempi precedenti.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una nuova chiave nell'insieme di credenziali delle chiavi. Per creare una nuova chiave, chiamare [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey). Sostituire i valori segnaposto tra parentesi quadre con valori personalizzati e utilizzare le variabili definite negli esempi precedenti.

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
