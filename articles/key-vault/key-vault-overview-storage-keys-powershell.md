---
title: Account di archiviazione gestita di Azure Key Vault - Versione PowerShell
description: Un account di archiviazione gestita consente un'integrazione diretta tra Azure Key Vault e un account di Archiviazione di Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 530f38289afb3fce85bbb025e7b699862eedbadc
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382690"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Account di archiviazione gestita di Azure Key Vault - PowerShell

> [!NOTE]
> [L'integrazione di Archiviazione di Azure con Azure Active Directory (Azure AD) è ora disponibile in anteprima](../storage/common/storage-auth-aad.md). Si consiglia di usare Azure AD per le fasi di autenticazione e autorizzazione, poiché offre l'accesso basato sul token OAuth2 ad Archiviazione di Azure, proprio come Azure Key Vault. In questo modo è possibile:
> - Autenticare l'applicazione client con un'identità di applicazione o utente, anziché con le credenziali dell'account di archiviazione. 
> - Usare un'[identità gestita di Azure AD](/azure/active-directory/managed-identities-azure-resources/) per l'esecuzione in Azure. Le identità gestite eliminano la necessità di eseguire l'autenticazione di tutti i client e di archiviare le credenziali nell'applicazione.
> - Usare il controllo degli accessi in base al ruolo per la gestione delle autorizzazioni, che è supportato anche da Key Vault.
> - L'accesso ad AAD all'account di archiviazione non funziona ancora per l'accesso alle tabelle.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Un'[account di Archiviazione Azure](/azure/storage/storage-create-storage-account) usa credenziali costituite da un nome account e una chiave. La chiave viene generata automaticamente e, contrariamente a una chiave di crittografia, svolge la funzione di "password". Per gestire queste chiavi degli account di archiviazione, Key Vault può archiviarle come [segreti di Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Panoramica

Gli account di archiviazione gestiti di Key Vault eseguono diverse funzioni di gestione per conto dell'utente:

- Elencano le chiavi (sincronizzazioni) con un account di Archiviazione di Azure.
- Rigenerano (ruotano) le chiavi con cadenza periodica.
- Gestiscono le chiavi sia degli account di archiviazione sia degli account di archiviazione classici.
- I valori di chiave non vengono mai restituiti in risposta al chiamante.

Quando si usano le chiavi di un account di archiviazione gestito:

- **Consentire solo a Key Vault di gestire le chiavi dell'account di archiviazione.** Non tentare di gestirle manualmente per evitare di interferire con i processi di Key Vault.
- **Non consentire alle chiavi degli account di archiviazione di essere gestite da più di un oggetto di Key Vault**.
- **Non rigenerare manualmente le chiavi dell'account di archiviazione**. È opportuno rigenerarle tramite Key Vault.

Gli esempi seguenti illustrano come consentire a Key Vault di gestire le chiavi degli account di archiviazione.

## <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione di PowerShell usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 
```azurepowershell-interactive
Connect-AzAccount
```
Se si hanno più sottoscrizioni di Azure, è possibile elencarle usando il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) e specificare la sottoscrizione che si vuole usare con il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autorizzare Key Vault ad accedere all'account di archiviazione

> [!IMPORTANT]
> Un tenant Azure AD assegna ad ogni applicazione registrata un' **[entità servizio](/azure/active-directory/develop/developer-glossary#service-principal-object)** , che svolge la funzione di identità dell'applicazione. Viene usato l'ID di applicazione dell'entità servizio per concedere a tale entità l'autorizzazione ad accedere ad altre risorse di Azure tramite il controllo degli accessi in base al ruolo. Key Vault è un'applicazione Microsoft pre-registrata in tutti i tenant di Azure AD con lo stesso ID di applicazione all'interno di ogni cloud di Azure:
> - I tenant di Azure AD nel cloud di Azure per enti pubblici usano l'ID di applicazione `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - I tenant di Azure AD nel cloud pubblico di Azure e in tutti gli altri usano l'ID di applicazione `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Prima che Key Vault possa accedere e gestire le chiavi dell'account di archiviazione, è necessario autorizzarlo ad accedere all'account di archiviazione. L'applicazione Key Vault richiede inoltre apposite autorizzazioni per *elencare* e *rigenerare* le chiavi di un account di archiviazione. Queste autorizzazioni vengono abilitate tramite il ruolo predefinito Controllo degli accessi in base al ruolo [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Assegnare questo ruolo all'entità servizio di Key Vault, limitandone l'ambito al proprio account di archiviazione, seguendo la procedura seguente. Assicurarsi di aggiornare le variabili `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey` e `$keyVaultName` prima di eseguire lo script:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Get your User Id for later commands
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Se l'assegnazione del ruolo ha esito positivo, l'output dovrebbe essere simile all'esempio seguente:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Se Key Vault era già stato aggiunto a questo ruolo nel proprio account di archiviazione, si riceverà l'errore *"L'assegnazione di ruolo esiste già"* . È possibile anche verificare l'assegnazione del ruolo consultando la pagina "Controllo di accesso (IAM)" dell'account di archiviazione nel portale di Azure.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Concedere all'account utente l'autorizzazione per gli account di archiviazione gestiti

>[!TIP] 
> Azure AD fornisce un'**entità servizio** per l'identità di un'applicazione e un'**entità utente** per l'identità di un utente. All'entità utente viene quindi concessa l'autorizzazione per accedere a Key Vault tramite i criteri di accesso di Key Vault.

Usando la stessa sessione di PowerShell, aggiornare i criteri di accesso di Key Vault per gli account di archiviazione gestiti. Questo passaggio applica le autorizzazioni dell'account di archiviazione all'account utente, in modo che sia possibile accedere alle funzionalità dell'account di archiviazione gestito: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Tenere presente che le autorizzazioni per gli account di archiviazione non sono disponibili nella pagina "Criteri di accesso" dell'account di archiviazione nel portale di Azure.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Aggiungere un account di archiviazione gestito all'istanza di Key Vault

Usando la stessa sessione di PowerShell, creare un account di archiviazione gestito nell'istanza di Key Vault. L'opzione `-DisableAutoRegenerateKey` specifica di NON rigenerare le chiavi dell'account di archiviazione.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Dopo aver aggiunto l'account di archiviazione senza la rigenerazione delle chiavi, verrà visualizzato un output simile al seguente:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Abilitare la rigenerazione delle chiavi

Se si vuole che Key Vault rigeneri periodicamente le chiavi dell'account di archiviazione, è possibile impostare un periodo di rigenerazione. Nell'esempio seguente viene impostato un periodo di rigenerazione di tre giorni. Dopo tre giorni, Key Vault rigenererà' Key2' e scambierà la chiave attiva da' Key2' a' Key1'.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Dopo aver aggiunto l'account di archiviazione con la rigenerazione delle chiavi, verrà visualizzato un output simile al seguente:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Passaggi successivi

- [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Documentazione su PowerShell per Azure Key Vault)
