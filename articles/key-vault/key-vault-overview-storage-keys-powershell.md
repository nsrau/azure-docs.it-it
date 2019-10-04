---
title: Account di archiviazione gestita di Azure Key Vault - Versione PowerShell
description: La funzionalità dell'account di archiviazione gestito offre un'integrazione uniforme tra Azure Key Vault e un account di archiviazione di Azure.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 225d9b715c56e4813a8e26d881c876e7bd498155
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204207"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gestire le chiavi dell'account di archiviazione con Key Vault e Azure PowerShell

Un account di archiviazione di Azure usa le credenziali che includono un nome di account e una chiave. La chiave viene generata automaticamente e funge da password, anziché come chiave crittografica. Key Vault gestisce le chiavi degli account di archiviazione archiviando tali chiavi come [Key Vault segreti](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

È possibile usare la funzionalità chiave dell'account di archiviazione gestita di Key Vault per elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) periodicamente le chiavi. È possibile gestire le chiavi sia per gli account di archiviazione che per gli account di archiviazione classici.

Quando si usa la funzionalità chiave dell'account di archiviazione gestita, prendere in considerazione i punti seguenti:

- I valori di chiave non vengono mai restituiti in risposta a un chiamante.
- Solo Key Vault deve gestire le chiavi dell'account di archiviazione. Non gestire personalmente le chiavi ed evitare di interferire con Key Vault processi.
- Solo un singolo oggetto Key Vault deve gestire le chiavi dell'account di archiviazione. Non consentire la gestione delle chiavi da più oggetti.
- È possibile richiedere Key Vault per gestire l'account di archiviazione con un'entità utente, ma non con un'entità servizio.
- Rigenerare le chiavi usando solo Key Vault. Non rigenerare manualmente le chiavi dell'account di archiviazione.

È consigliabile usare l'integrazione di archiviazione di Azure con Azure Active Directory (Azure AD), il servizio di gestione delle identità e degli accessi basato sul cloud Microsoft. L'integrazione di Azure AD è disponibile per i [BLOB e le code di Azure](../storage/common/storage-auth-aad.md)e fornisce l'accesso basato su token OAuth2 ad archiviazione di Azure (proprio come Azure Key Vault).

Azure AD consente di autenticare l'applicazione client usando un'identità dell'applicazione o dell'utente, anziché le credenziali dell'account di archiviazione. È possibile usare un' [identità gestita Azure ad](/azure/active-directory/managed-identities-azure-resources/) quando si esegue in Azure. Le identità gestite eliminano la necessità dell'autenticazione client e l'archiviazione delle credenziali in o con l'applicazione.

Azure AD usa il controllo degli accessi in base al ruolo (RBAC) per gestire l'autorizzazione, supportata anche da Key Vault.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID applicazione dell'entità servizio

Un tenant Azure AD fornisce a ogni applicazione registrata un' [entità servizio](/azure/active-directory/develop/developer-glossary#service-principal-object). L'entità servizio funge da ID applicazione, che viene usato durante la configurazione dell'autorizzazione per l'accesso ad altre risorse di Azure tramite RBAC.

Key Vault è un'applicazione Microsoft già registrata in tutti i tenant di Azure AD. Key Vault viene registrato con lo stesso ID applicazione in ogni cloud di Azure.

| tenant | cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altro  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è innanzitutto necessario eseguire le operazioni seguenti:

- [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Creare un insieme di credenziali delle chiavi](quick-create-powershell.md)
- [Creare un account di Archiviazione di Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-powershell). Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.
      

## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazione

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione di PowerShell usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) . 

```azurepowershell-interactive
Connect-AzAccount
```
Se si hanno più sottoscrizioni di Azure, è possibile elencarle usando il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) e specificare la sottoscrizione che si vuole usare con il cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Impostare variabili

Per prima cosa, impostare le variabili che devono essere usate dai cmdlet di PowerShell nei passaggi seguenti. Assicurarsi di aggiornare i <YourResourceGroupName> <YourKeyVaultName> segnaposto <YourStorageAccountName>, e e impostare $keyVaultSpAppId su `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (come specificato in [ID applicazione dell'entità servizio](#service-principal-application-id), sopra).

Si userà anche il Azure PowerShell cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) e [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) per ottenere l'ID utente e il contesto dell'account di archiviazione di Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Concedere a Key Vault l'accesso all'account di archiviazione

Prima che Key Vault possa accedere e gestire le chiavi dell'account di archiviazione, è necessario autorizzarlo ad accedere all'account di archiviazione. L'applicazione Key Vault richiede inoltre apposite autorizzazioni per *elencare* e *rigenerare* le chiavi di un account di archiviazione. Queste autorizzazioni vengono abilitate tramite il ruolo predefinito Controllo degli accessi in base al ruolo [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Assegnare questo ruolo all'entità servizio Key Vault, limitando l'ambito all'account di archiviazione, usando il cmdlet Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) .

```azurepowershell-interactive
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

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Concedere all'account utente l'autorizzazione per gli account di archiviazione gestiti

Usare il cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) di Azure PowerShell per aggiornare i criteri di accesso key Vault e concedere le autorizzazioni per l'account di archiviazione all'account utente.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Tenere presente che le autorizzazioni per gli account di archiviazione non sono disponibili nella pagina "Criteri di accesso" dell'account di archiviazione nel portale di Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Aggiungere un account di archiviazione gestito all'istanza di Key Vault

Usare il cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) di Azure PowerShell per creare un account di archiviazione gestito nell'istanza di Key Vault. L'opzione `-DisableAutoRegenerateKey` specifica di NON rigenerare le chiavi dell'account di archiviazione.

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

Se si desidera Key Vault rigenerare periodicamente le chiavi dell'account di archiviazione, è possibile utilizzare il Azure PowerShell cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) per impostare un periodo di rigenerazione. In questo esempio viene impostato un periodo di rigenerazione di tre giorni. Dopo tre giorni, Key Vault rigenererà' Key2' e scambierà la chiave attiva da' Key2' a' Key1'.

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

## <a name="shared-access-signature-tokens"></a>Token di firma di accesso condiviso

È anche possibile richiedere Key Vault per generare token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione senza condividere le chiavi dell'account. Una firma di accesso condiviso offre un modo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione completano le azioni seguenti:

- Impostare la definizione di una firma di accesso condiviso dell'account. 
- Creare un token di firma di accesso condiviso dell'account per i servizi BLOB, file, tabelle e di Accodamento. Il token viene creato per i tipi di risorse Service, container e Object. Il token viene creato con tutte le autorizzazioni, su HTTPS e con le date di inizio e di fine specificate.
- Impostare un Key Vault definizione di firma di accesso condiviso di archiviazione gestita nell'insieme di credenziali. La definizione ha l'URI del modello del token della firma di accesso condiviso che è stato creato. La definizione ha il tipo `account` di firma di accesso condiviso ed è valida per N giorni.
- Verificare che la firma di accesso condiviso sia stata salvata nell'insieme di credenziali delle chiavi come segreto.
- 
### <a name="set-variables"></a>Impostare variabili

Per prima cosa, impostare le variabili che devono essere usate dai cmdlet di PowerShell nei passaggi seguenti. Assicurarsi di aggiornare i <YourStorageAccountName> segnaposto e. <YourKeyVaultName>

Si userà anche il Azure PowerShell cmdlet [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) per ottenere il contesto dell'account di archiviazione di Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Creazione di un token di firma di accesso condiviso

Creare una definizione di firma di accesso condiviso usando il Azure PowerShell cmdlet [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) .
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Il valore di $sasToken sarà simile al seguente.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generare una definizione di firma di accesso condiviso

Usare il cmdlet [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) di Azure PowerShell per creare una definizione di firma di accesso condiviso.  È possibile specificare il nome desiderato per il `-Name` parametro.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificare la definizione della firma di accesso condiviso

È possibile verificare che la definizione della firma di accesso condiviso sia stata archiviata nell'insieme di credenziali delle chiavi usando il cmdlet Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) .

Per prima cosa, trovare la definizione della firma di accesso condiviso nell'insieme di credenziali delle chiavi.

```azurepowershell-interactive
Get-AzKeyVaultSecret -vault-name <YourKeyVaultName>
```

Il segreto che corrisponde alla definizione SAS avrà queste proprietà:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

È ora possibile usare il cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e la proprietà `Name` Secret per visualizzare il contenuto del segreto.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

L'output di questo comando visualizzerà la stringa di definizione della firma di accesso condiviso.


## <a name="next-steps"></a>Passaggi successivi

- [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informazioni su chiavi, segreti e certificati](about-keys-secrets-and-certificates.md)
- [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Documentazione su PowerShell per Azure Key Vault)
