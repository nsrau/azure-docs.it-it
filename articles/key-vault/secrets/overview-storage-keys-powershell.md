---
title: Account di archiviazione gestita di Azure Key Vault - Versione PowerShell
description: La funzionalità dell'account di archiviazione gestito offre un'integrazione senza soluzione di continuità, tra L'insieme di credenziali delle chiavi di Azure e un account di archiviazione di Azure.The managed storage account feature provides a seamless integration, between Azure Key Vault and an Azure storage account.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: f8c526148e37ba1b716aafd32dcc3f242358f1eb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427783"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gestire le chiavi dell'account di archiviazione con Key Vault e Azure PowerShellManage storage account keys with Key Vault and Azure PowerShell

Un account di archiviazione di Azure usa credenziali che comprendono un nome di account e una chiave. La chiave viene generata automaticamente e funge da password, anziché come chiave crittografica. Key Vault gestisce le chiavi dell'account di archiviazione archiviandole come segreti dell'insieme di credenziali delle chiavi. 

È possibile usare la funzionalità chiave dell'account di archiviazione gestito dell'insieme di credenziali delle chiavi per elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) le chiavi periodicamente. È possibile gestire le chiavi sia per gli account di archiviazione che per gli account di archiviazione classici.

Quando si usa la funzionalità della chiave dell'account di archiviazione gestita, considerare i punti seguenti:When you use the managed storage account key feature, consider the following points:

- I valori delle chiavi non vengono mai restituiti in risposta a un chiamante.
- Solo l'insieme di credenziali delle chiavi deve gestire le chiavi dell'account di archiviazione. Non gestire le chiavi da solo ed evitare di interferire con i processi di Key Vault.
- Solo un singolo oggetto Key Vault deve gestire le chiavi dell'account di archiviazione. Non consentire la gestione delle chiavi da più oggetti.
- È possibile richiedere l'insieme di credenziali delle chiavi per gestire l'account di archiviazione con un'entità utente, ma non con un'entità servizio.
- Rigenerare le chiavi utilizzando solo l'insieme di credenziali delle chiavi. Non rigenerare manualmente le chiavi dell'account di archiviazione.

È consigliabile usare l'integrazione di Archiviazione di Azure con Azure Active Directory (Azure AD), il servizio di gestione delle identità e degli accessi basato su cloud di Microsoft.We recommend using Azure Storage integration with Azure Active Directory (Azure AD), s microsoft's cloud-based identity and access management service. L'integrazione di Azure AD è disponibile per [i BLOB e le code](../../storage/common/storage-auth-aad.md)di Azure e fornisce l'accesso basato su token OAuth2 ad Archiviazione di Azure (proprio come Archiviazione delle chiavi di Azure).

Azure AD consente di autenticare l'applicazione client usando un'identità dell'applicazione o dell'utente anziché le credenziali dell'account di archiviazione. È possibile usare [un'identità gestita di Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando si esegue in Azure.You can use an Azure AD managed identity when you run on Azure. Le identità gestite eliminano la necessità di autenticazione client e l'archiviazione delle credenziali all'utente o con l'applicazione.

Azure AD usa il controllo degli accessi in base al ruolo per gestire l'autorizzazione, supportata anche dall'insieme di credenziali delle chiavi.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID applicazione entità servizio

Un tenant di Azure AD fornisce a ogni applicazione registrata [un'entità servizio.](/azure/active-directory/develop/developer-glossary#service-principal-object) L'entità servizio funge da ID applicazione, usato durante la configurazione dell'autorizzazione per l'accesso ad altre risorse di Azure tramite il controllo degli accessi in base al ruolo.

Key Vault è un'applicazione Microsoft preregistrata in tutti i tenant di Azure AD. L'insieme di credenziali delle chiavi viene registrato nello stesso ID applicazione in ogni cloud di Azure.Key Vault is registered under the same Application ID in each Azure cloud.

| Tenant | Cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altri  | Qualsiasi | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è innanzitutto necessario eseguire le operazioni seguenti:To complete this guide, you must first do the following:

- [Installare il modulo di Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Creare un insieme di credenziali delle chiaviCreate a key vault](quick-create-powershell.md)
- Creare un account di archiviazione di [Azure.Create an Azure storage account](../../storage/common/storage-account-create.md?tabs=azure-powershell). Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.
      

## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazioneManage storage account keys

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione di PowerShell utilizzando il cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Se si dispone di più sottoscrizioni di Azure, è possibile elencarle usando il cmdlet Get-AzSubscription e specificare la sottoscrizione che si vuole usare con il cmdlet [Set-AzContext.If](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) you have multiple Azure subscriptions, you can list them using the [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) cmdlet, and specify the subscription you wish to use with the Set-AzContext cmdlet. 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Impostare variabili

Impostare innanzitutto le variabili che devono essere utilizzate dai cmdlet di PowerShell nei passaggi seguenti. Assicurarsi di <YourResourceGroupName>aggiornare i segnaposto <YourStorageAccountName>, e <YourKeyVaultName> e impostare $keyVaultSpAppId su `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (come specificato in ID applicazione [dell'entità servizio](#service-principal-application-id), sopra).

Utilizzeremo anche i cmdlet Get-AzContext e Get-AzStorageAccount di Azure PowerShell per ottenere l'ID utente e il contesto dell'account di archiviazione di Azure.We also use the Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) and [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) cmdlets to get your user ID and the context of your Azure storage account.

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

Assegnare questo ruolo all'entità servizio dell'insieme di credenziali delle chiavi, limitando l'ambito all'account di archiviazione usando il cmdlet New-AzRoleAssignment di Azure PowerShell.Assign this role to the Key Vault service principal, limiting scope to your storage account, using the Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0) cmdlet.

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

Usare il cmdlet Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) per aggiornare i criteri di accesso dell'insieme di credenziali delle chiavi e concedere le autorizzazioni dell'account di archiviazione all'account utente.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Tenere presente che le autorizzazioni per gli account di archiviazione non sono disponibili nella pagina "Criteri di accesso" dell'account di archiviazione nel portale di Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Aggiungere un account di archiviazione gestito all'istanza di Key Vault

Usare il cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) per creare un account di archiviazione gestito nell'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza dell'istanza della chiave. L'opzione `-DisableAutoRegenerateKey` specifica di NON rigenerare le chiavi dell'account di archiviazione.

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

Se si vuole che Key Vault rigeneri periodicamente le chiavi dell'account di archiviazione, è possibile usare il cmdlet Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) per impostare un periodo di rigenerazione. In questo esempio, abbiamo impostato un periodo di rigenerazione di tre giorni. Dopo tre giorni, Key Vault rigenererà 'key2' e scambia la chiave attiva da 'key2' a 'key1'.

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

## <a name="shared-access-signature-tokens"></a>Token di firma di accesso condivisoShared access signature tokens

È inoltre possibile chiedere a Key Vault di generare token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione senza condividere le chiavi dell'account. Una firma di accesso condiviso offre un modo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione completano le seguenti azioni:

- Impostare una definizione di firma di accesso condiviso dell'account. 
- Creare un token di firma di accesso condiviso dell'account per i servizi BLOB, file, tabella e coda. Il token viene creato per i tipi di risorse Service, Container e Object.The token is created for resource types Service, Container, and Object. Il token viene creato con tutte le autorizzazioni, su https e con le date di inizio e di fine specificate.
- Impostare una definizione di firma di accesso condiviso di archiviazione gestita dell'insieme di credenziali delle chiavi nell'insieme di credenziali. La definizione ha l'URI del modello del token di firma di accesso condiviso creato. La definizione ha il `account` tipo di firma di accesso condiviso ed è valida per N giorni.
- Verificare che la firma di accesso condiviso sia stata salvata nell'insieme di credenziali delle chiavi come segreto.
- 
### <a name="set-variables"></a>Impostare variabili

Impostare innanzitutto le variabili che devono essere utilizzate dai cmdlet di PowerShell nei passaggi seguenti. Assicurarsi di <YourStorageAccountName> aggiornare i segnaposto e <YourKeyVaultName> .

Utilizzeremo anche i cmdlet New-AzStorageContext di Azure PowerShell per ottenere il contesto dell'account di archiviazione di Azure.We also use the Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) cmdlets to get the context of your Azure storage account.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Creare un token di firma di accesso condivisoCreate a shared access signature token

Creare una definizione di firma di accesso condiviso usando i cmdlet New-AzStorageAccountSASToken di Azure PowerShell.Create a shared access signature definition using the Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0) cmdlets.
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Il valore di $sasToken sarà simile al seguente.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generare una definizione di firma di accesso condivisoGenerate a shared access signature definition

Utilizzare il cmdlet [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) di Azure PowerShell per creare una definizione di firma di accesso condiviso.  È possibile fornire il nome `-Name` desiderato al parametro.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificare la definizione della firma di accesso condivisoVerify the shared access signature definition

È possibile verificare che la definizione della firma di accesso condiviso sia stata archiviata nell'insieme di credenziali delle chiavi usando il cmdlet Get-AzKeyVaultSecret di Azure PowerShell.You can verify that the shared access signature definition has been stored in your key vault using the Azure PowerShell [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0) cmdlet.

Individuare innanzitutto la definizione della firma di accesso condiviso nell'insieme di credenziali delle chiavi.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Il segreto corrispondente alla definizione di sAS avrà queste proprietà:The secret corresponding to your SAS definition will have these properties:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

È ora possibile utilizzare il cmdlet [Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e la proprietà secret `Name` per visualizzare il contenuto di tale segreto.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

L'output di questo comando mostrerà la stringa di definizione sAS.


## <a name="next-steps"></a>Passaggi successivi

- [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Documentazione su PowerShell per Azure Key Vault)
