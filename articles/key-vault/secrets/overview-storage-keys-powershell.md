---
title: Account di archiviazione gestita di Azure Key Vault - Versione PowerShell
description: Un account di archiviazione gestito consente un'integrazione diretta tra Azure Key Vault e un account di archiviazione di Azure.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3bced101516e91259ea9018fe3c4aa44f867cbe6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "96023109"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Gestire le chiavi degli account di archiviazione con Key Vault e Azure PowerShell
> [!IMPORTANT]
> È consigliabile usare l'integrazione di Archiviazione di Azure con Azure Active Directory (Azure AD), il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso. L'integrazione con Azure AD è disponibile per [BLOB e code di Azure](../../storage/common/storage-auth-aad.md) e offre l'accesso basato su token OAuth2 ad Archiviazione di Azure, analogamente ad Azure Key Vault.
> Azure AD consente di autenticare l'applicazione client con un'identità di applicazione o utente, anziché con le credenziali dell'account di archiviazione. È possibile usare un'[identità gestita di Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) per l'esecuzione in Azure. Le identità gestite eliminano la necessità di eseguire l'autenticazione dei client e di archiviare le credenziali nell'applicazione. Usare la soluzione seguente solo nei casi in cui l'autenticazione di Azure AD non è possibile.

Un account di archiviazione Azure usa credenziali costituite da un nome account e una chiave. La chiave viene generata automaticamente e viene usata come password, invece che come chiave crittografica. Key Vault gestisce le chiavi degli account di archiviazione rigenerandole periodicamente nell'account di archiviazione e fornisce token di firma di accesso condiviso per l'accesso delegato alle risorse nell'account di archiviazione.

È possibile usare la funzionalità di chiave dell'account di archiviazione gestita di Key Vault per elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) le chiavi periodicamente. È possibile gestire le chiavi per gli account di archiviazione e gli account di archiviazione classici.

Quando si usa la funzionalità di chiave dell'account di archiviazione gestita, è necessario prendere in considerazione gli aspetti seguenti:

- I valori di chiave non vengono mai restituiti in risposta a un chiamante.
- È necessario che solo Key Vault gestisca le chiavi dell'account di archiviazione. Non è possibile gestire autonomamente le chiavi e occorre evitare di interferire con i processi di Key Vault.
- È necessario che solo un singolo oggetto di Key Vault gestisca le chiavi dell'account di archiviazione. È necessario non consentire la gestione delle chiavi da più oggetti.
- È necessario rigenerare le chiavi solo tramite Key Vault. Non rigenerare manualmente le chiavi dell'account di archiviazione.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID applicazione dell'entità servizio

Un tenant di Azure AD assegna a ogni applicazione registrata un'[entità servizio](../../active-directory/develop/developer-glossary.md#service-principal-object). L'entità servizio fornisce l'ID applicazione, che viene usato durante la configurazione dell'autorizzazione per l'accesso ad altre risorse di Azure tramite il controllo degli accessi in base al ruolo di Azure.

Key Vault è un'applicazione Microsoft già registrata in tutti i tenant di Azure AD. Key Vault è registrato con lo stesso ID applicazione in ogni cloud di Azure.

| Tenant | Cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altro  | Qualsiasi | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario eseguire prima di tutto queste operazioni:

- [Installare il modulo Azure PowerShell](/powershell/azure/install-az-ps).
- [Creare un insieme di credenziali delle chiavi](quick-create-powershell.md)
- [Creare un account di Archiviazione di Azure](../../storage/common/storage-account-create.md?tabs=azure-powershell). Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri.


## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazione

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione di PowerShell usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).

```azurepowershell-interactive
Connect-AzAccount
```
Se sono presenti più sottoscrizioni di Azure, è possibile elencarle mediante il cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) e quindi specificare la sottoscrizione da usare con il cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Impostare variabili

Impostare prima di tutto le variabili che devono essere usate dai cmdlet di PowerShell nei passaggi seguenti. Assicurarsi di aggiornare i segnaposto "YourResourceGroupName", "YourStorageAccountName" e "YourKeyVaultName" e di impostare $keyVaultSpAppId su `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`, come specificato in precedenza in [ID applicazione dell'entità servizio](#service-principal-application-id).

Verranno usati anche i cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext) e [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) di Azure PowerShell per ottenere l'ID utente e il contesto dell'account di archiviazione di Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> Per un account di archiviazione classico è necessario usare i valori "primary" e "secondary" per $storageAccountKey <br>
> Usare 'Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName' invece di 'Get-AzStorageAccount' per l'account di archiviazione classico

### <a name="give-key-vault-access-to-your-storage-account"></a>Autenticare l'accesso di Key Vault all'account di archiviazione

Prima che Key Vault possa accedere e gestire le chiavi dell'account di archiviazione, è necessario autorizzarlo ad accedere all'account di archiviazione. L'applicazione Key Vault richiede inoltre apposite autorizzazioni per *elencare* e *rigenerare* le chiavi di un account di archiviazione. Queste autorizzazioni vengono abilitate tramite il ruolo predefinito [Ruolo del servizio dell'operatore della chiave dell'account di archiviazione](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role) di Azure.

Assegnare questo ruolo all'entità servizio di Key Vault, limitandone l'ambito al proprio account di archiviazione, usando il cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) di Azure PowerShell.

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
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

Se Key Vault era già stato aggiunto a questo ruolo nel proprio account di archiviazione, si riceverà l'errore *"L'assegnazione di ruolo esiste già"* errore. È possibile anche verificare l'assegnazione del ruolo consultando la pagina "Controllo di accesso (IAM)" dell'account di archiviazione nel portale di Azure.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Concedere all'account utente l'autorizzazione per gli account di archiviazione gestiti

Usare il cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) di Azure PowerShell per aggiornare i criteri di accesso di Key Vault e concedere le autorizzazioni dell'account di archiviazione all'account utente.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Tenere presente che le autorizzazioni per gli account di archiviazione non sono disponibili nella pagina "Criteri di accesso" dell'account di archiviazione nel portale di Azure.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Aggiungere un account di archiviazione gestito all'istanza di Key Vault

Usare il cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) di Azure PowerShell per creare un account di archiviazione gestito nell'istanza di Key Vault. L'opzione `-DisableAutoRegenerateKey` specifica di NON rigenerare le chiavi dell'account di archiviazione.

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

Se si vuole che Key Vault rigeneri periodicamente le chiavi dell'account di archiviazione, è possibile usare il cmdlet [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) di Azure PowerShell per configurare un periodo di rigenerazione. In questo esempio viene impostato un periodo di rigenerazione di tre giorni. Quando è il momento di eseguire la rotazione, Key Vault rigenera la chiave che non è attiva e quindi imposta la chiave appena creata come attiva. Solo una delle chiavi viene usata per rilasciare token di firma di accesso condiviso in un momento specifico. Si tratta della chiave attiva.

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

È anche possibile chiedere a Key Vault di generare i token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione, senza condividere le chiavi dell'account. Una firma di accesso condiviso offre una modalità sicura per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione consentono di completare le azioni seguenti:

- Configurare una definizione di firma di accesso condiviso dell'account.
- Creare un token di firma di accesso condiviso per i servizi BLOB, file, tabella e coda. Il token viene creato per le risorse di tipo servizio, contenitore e oggetto. Il token viene creato con tutte le autorizzazioni su https e con le date di inizio e di fine specificate.
- Configurare una definizione di firma di accesso condiviso della risorsa di archiviazione gestita di Key Vault nell'insieme di credenziali. La definizione include l'URI del modello del token di firma di accesso condiviso creato. La definizione ha una firma di accesso condiviso di tipo `account` ed è valida per N giorni.
- Verificare che la firma di accesso condiviso sia stata salvata nell'insieme di credenziali delle chiavi come segreto.
-
### <a name="set-variables"></a>Impostare variabili

Impostare prima di tutto le variabili che devono essere usate dai cmdlet di PowerShell nei passaggi seguenti. Assicurarsi di aggiornare i segnaposto <YourStorageAccountName> e <YourKeyVaultName>.

Verranno usati anche i cmdlet [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) di Azure PowerShell per ottenere il contesto dell'account di archiviazione di Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Creare un token di firma di accesso condiviso

Creare una definizione di firma di accesso condiviso usando i cmdlet [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) di Azure PowerShell.

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Il valore di $sasToken sarà analogo al seguente.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generare una definizione di firma di accesso condiviso

Usare il cmdlet [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) di Azure PowerShell per creare una definizione di firma di accesso condiviso.  È possibile specificare il nome che si preferisce per il parametro `-Name`.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificare la definizione di firma di accesso condiviso

È possibile verificare che la definizione di firma di accesso condiviso sia stata archiviata nell'insieme di credenziali delle chiavi usando il cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) di Azure PowerShell.

Trovare prima di tutto la definizione di firma di accesso condiviso nell'insieme di credenziali delle chiavi.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Il segreto corrispondente alla definizione di firma di accesso condiviso avrà le proprietà seguenti:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

È ora possibile usare il cmdlet [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) con i parametri `VaultName` e `Name` per visualizzare i contenuti di tale segreto.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

L'output di questo comando mostrerà la stringa della definizione di firma di accesso condiviso.

## <a name="next-steps"></a>Passaggi successivi

- [Esempi di chiavi di account di archiviazione gestiti](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Azure Key Vault PowerShell reference](/powershell/module/az.keyvault/#key_vault) (Documentazione su PowerShell per Azure Key Vault)
