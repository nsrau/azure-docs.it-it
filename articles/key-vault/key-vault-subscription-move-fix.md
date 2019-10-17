---
title: Modificare l'ID tenant dell'insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione - Azure Key Vault | Microsoft Docs
description: Informazioni su come modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione a un altro tenant
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: bc2e73d037b05c012002d7a07e2a2af2431423fa
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428900"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Quando si crea un nuovo insieme di credenziali delle chiavi in una sottoscrizione, questo viene automaticamente associato all'ID tenant di Azure Active Directory predefinito per la sottoscrizione. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. 

Se si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti non sono accessibili dalle entità di sicurezza, ovvero utenti e applicazioni, nel tenant B. Per risolvere questo problema è necessario:

* Modificare l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione impostandolo sul tenant B.
* Rimuovere tutte le voci dei criteri di accesso esistenti.
* Aggiungere nuove voci dei criteri di accesso associate al tenant B.

Se ad esempio è presente un insieme di credenziali delle chiavi 'myvault' in una sottoscrizione che è stata spostata dal tenant A al tenant B, sarà possibile usare Azure PowerShell per modificare l'ID tenant e rimuovere i criteri di accesso precedenti.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

In alternativa, è possibile usare l'interfaccia della riga di comando di Azure.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Ora che l'insieme di credenziali è associato all'ID tenant corretto e che le voci dei criteri di accesso precedenti sono state rimosse, impostare le nuove voci dei criteri di accesso con il cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) di Azure PowerShell o il comando [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure.

Se si usa un'identità gestita per le risorse di Azure, sarà necessario aggiornarla con il nuovo tenant di Azure AD. Per altre informazioni sulle identità gestite, vedere [Eseguire l'autenticazione a Key Vault con un'identità gestita](managed-identity.md).


Se si usa l'identità del servizio gestito, sarà anche necessario aggiornare l'identità del servizio gestita perché l'identità precedente non risiederà più nel tenant AAD corretto.

## <a name="next-steps"></a>Passaggi successivi

In caso di domande sull'insieme di credenziali delle chiavi di Azure, visitare i [forum sull'insieme di credenziali delle chiavi di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
