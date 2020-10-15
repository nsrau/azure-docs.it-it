---
title: Assegnare un criterio di accesso Azure Key Vault
description: Come usare la portale di Azure, l'interfaccia della riga di comando di Azure o Azure PowerShell per assegnare un criterio di accesso Key Vault a un'entità servizio o a un'identità di applicazione.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 817b9bfc0af054b344ca9f770085ac022a8e6eac
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89381079"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Assegnare un criterio di accesso Key Vault usando Azure PowerShell

Un criterio di accesso Key Vault determina se una determinata entità servizio, ovvero un'applicazione o un gruppo di utenti, può eseguire diverse operazioni su Key Vault [segreti](../secrets/index.yml), [chiavi](../keys/index.yml)e [certificati](../certificates/index.yml). È possibile assegnare i criteri di accesso usando il [portale di Azure](assign-access-policy-portal.md), l'interfaccia della riga di comando di [Azure](assign-access-policy-cli.md)o Azure PowerShell (questo articolo).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per ulteriori informazioni sulla creazione di gruppi in Azure Active Directory tramite Azure PowerShell, vedere [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) e [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Configurare PowerShell e l'accesso

1. Per eseguire i comandi in locale, installare [Azure PowerShell](/powershell/azure/) se non è già stato fatto.

    Per eseguire i comandi direttamente nel cloud, usare il [Azure cloud Shell](/azure/cloud-shell/overview).

1. Solo PowerShell locale:

    1. Installare il [modulo Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureAD).

    1. Accedere ad Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Acquisire l'ID oggetto

Determinare l'ID oggetto dell'applicazione, del gruppo o dell'utente a cui si desidera assegnare i criteri di accesso:

- Applicazioni e altre entità servizio: usare il cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) con il `-SearchString` parametro per filtrare i risultati con il nome dell'entità servizio desiderata:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Gruppi: usare il cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) con il `-SearchString` parametro per filtrare i risultati con il nome del gruppo desiderato:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    Nell'output l'ID oggetto è elencato come `Id` .

- Utenti: usare il cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , passando l'indirizzo di posta elettronica dell'utente al `-UserPrincipalName` parametro.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    Nell'output l'ID oggetto è elencato come `Id` .

## <a name="assign-the-access-policy"></a>Assegnare i criteri di accesso

Usare il cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) per assegnare i criteri di accesso:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

È necessario includere `-PermissionsToSecrets` , `-PermissionsToKeys` e solo quando si `-PermissionsToCertificates` assegnano autorizzazioni a tali tipi particolari. I valori consentiti per `<secret-permissions>` , `<key-permissions>` e `<certificate-permissions>` sono specificati nella documentazione di [set-AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) .

## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](overview-security.md#identity-and-access-management)
- [Proteggere l'](secure-your-key-vault.md)insieme di credenziali delle chiavi.
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)
- [Procedure consigliate per Azure Key Vault](best-practices.md)
