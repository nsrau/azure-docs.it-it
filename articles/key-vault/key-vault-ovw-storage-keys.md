---
ms.assetid: ''
title: Chiavi dell'account di archiviazione Key Vault
description: Le chiavi dell'account di archiviazione forniscono un'integrazione diretta tra Azure Key Vault e l'accesso basato sulla chiave dell'account di Archiviazione di Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: lleonard-msft
ms.author: alleonar
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: a3f8d540c7e4c8a86b151540980724777fd150fd
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="azure-key-vault-storage-account-keys"></a>Chiavi dell'account di archiviazione Key Vault

Prima delle chiavi dell'account di archiviazione Key Vault gli sviluppatori dovevano gestire le proprie chiavi dell'account di archiviazione di Azure (ASA) e ruotarle manualmente o mediante automazione esterna. Al momento, le chiavi dell'account di archiviazione di Key Vault sono implementate come [segreti di Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) per l'autenticazione con un account di archiviazione di Azure.

La funzionalità chiave di Account di Archiviazione di Azure (ASA) consente di gestire la rotazione dei segreti al posto dell'utente. Elimina anche la necessità di un contatto diretto con una chiave dell'account di archiviazione di Azure offrendo come metodo firme di accesso condiviso.

Per informazioni più generali sugli account di archiviazione di Azure, vedere [Informazioni sugli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Supporto di interfacce

L'elenco completo delle interfacce di programmazione e di script Microsoft e i collegamenti a queste sono disponibili in [Guida per gli sviluppatori di Key Vault](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>Ciò che Key Vault gestisce

Key Vault esegue diverse funzioni di gestione interne per conto dell'utente quando si usano chiavi dell'account di archiviazione gestite.

- Azure Key Vault gestisce le chiavi di un account di archiviazione di Azure.
    - Internamente Key Vault consente di elencare (sincronizzazione) le chiavi con un account di archiviazione di Azure.
    - Key Vault rigenera (ruota) le chiavi periodicamente.
    - I valori di chiave non vengono mai restituiti in risposta al chiamante.
    - Key Vault gestisce le chiavi sia degli account di archiviazione che degli account di archiviazione classici.
- Key Vault consente al proprietario dell'insieme di credenziali/oggetto di creare definizioni SAS (SAS di account o di servizio).
    - Il valore SAS, creato utilizzando la definizione SAS, viene restituito come una chiave privata tramite il percorso dell'URI REST. Per altre informazioni, vedere [Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations) (Operazioni dell'account di archiviazione di Azure Key Vault).

## <a name="naming-guidance"></a>Linee guida sulla denominazione

- I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
- Un nome di una definizione SAS deve avere da 1 a 102 caratteri e contenere solo i caratteri da 0 a 9, da a a z e da A a Z.

## <a name="developer-experience"></a>Esperienza per sviluppatori

### <a name="before-azure-key-vault-storage-keys"></a>Prima delle chiavi di archiviazione Key Vault

Gli sviluppatori in precedenza dovevano eseguire le seguenti procedure con una chiave di account di archiviazione per ottenere l'accesso all'archiviazione di Azure.
1. Archiviare la stringa di connessione o il token di firma di accesso condiviso nelle impostazioni dell'applicazione di Servizio app di Azure o in un'altra risorsa di archiviazione.
1. All'avvio dell'applicazione, recuperare la stringa di connessione o il token di firma di accesso condiviso.
1. Creare [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) per interagire con la risorsa di archiviazione.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Dopo le chiavi di archiviazione Key Vault

Gli sviluppatori creano un [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) e lo sfruttano per ottenere il token di firma di accesso condiviso per la loro risorsa archiviazione. Successivamente, creano [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) con tale token.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Materiale sussidiario per sviluppatori

- Consentire solo a Key Vault di gestire le chiavi ASA. Non tentare di gestirle manualmente onde evitare di interferire con i processi di Key Vault.
- Non consentire alle chiavi ASA di essere gestite da più di un oggetto di Key Vault.
- Se è necessario rigenerare manualmente le chiavi ASA, è consigliabile rigenerarle tramite Key Vault.

## <a name="getting-started"></a>Introduzione

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configurazione per le autorizzazioni di controllo degli accessi in base al ruolo (RBAC)

Per *elencare* e *rigenerare* le chiavi per un account di archiviazione, l'identità di applicazione di Azure Key Vault necessita di autorizzazioni. Impostare queste autorizzazioni usando la procedura seguente:

```powershell
# Get the resource ID of the Azure Storage Account you want to manage.
# Below, we are fetching a storage account using Azure Resource Manager
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure Key Vault Identity
$servicePrincipal = Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ObjectId $servicePrincipal.Id -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

    >[!NOTE]
    > For a classic account type, set the role parameter to *"Classic Storage Account Key Operator Service Role."*

## <a name="working-example"></a>Esempio di funzionamento

L'esempio seguente illustra la creazione di un account di archiviazione di Azure gestito da Key Vault e le definizioni di firma di accesso condiviso (SAS) associate.

### <a name="prerequisite"></a>Prerequisito

Assicurarsi di avere completato la [Configurazione per le autorizzazioni di controllo degli accessi in base al ruolo (RBAC)](#setup-for-role-based-access-control-rbac-permissions).

### <a name="setup"></a>Configurazione

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Successivamente, impostare le autorizzazioni per l'**account personale** per assicurarsi di potere gestire tutte le autorizzazioni di archiviazione in Key Vault. Nell'esempio seguente, l'account di Azure è _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Creare un account di archiviazione gestito da Key Vault

Creare ora un account di archiviazione gestito in Azure Key Vault e usare una chiave di accesso dall'account di archiviazione per creare il token di firma di accesso condiviso.
- `-ActiveKeyName` usa 'key2' per generare i token di firma di accesso condiviso.
- `-AccountName` consente di identificare l'account di archiviazione gestito. Di seguito viene usato il nome dell'account di archiviazione per semplicità, ma può essere qualsiasi nome.
- `-DisableAutoRegenerateKey` specifica di non rigenerare le chiavi dell'account di archiviazione.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Rigenerazione delle chiavi

Se si vuole che Key Vault rigeneri periodicamente le chiavi di accesso dell'archiviazione, è possibile impostare un periodo di rigenerazione. Di seguito viene impostato un periodo di rigenerazione di 3 giorni. Dopo 3 giorni, Key Vault rigenera 'key1' e cambia l'impostazione della chiave attiva da 'key2' a 'key1'.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Impostare le definizioni di firma di accesso condiviso

La firma di accesso condiviso dell'account consente di accedere al servizio BLOB con autorizzazioni diverse.
Impostare le definizioni di firma di accesso condiviso in Key Vault per l'account di archiviazione gestito.
- `-AccountName` è il nome dell'account di archiviazione gestito in Key Vault.
- `-Name` è l'identificatore per il token di firma di accesso condiviso nell'archiviazione.
- `-ValidityPeriod` imposta la data di scadenza per il token di firma di accesso condiviso generato.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>Ottenere i token di firma di accesso condiviso

Ottenere i token SAS corrispondenti ed eseguire chiamate all'account di archiviazione. `-SecretName` viene costruito usando l'input dai parametri `AccountName` e `Name` quando si esegue [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/en-us/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Creazione dell'account di archiviazione

Si noti che il tentativo di accesso con *$readSasToken* ha esito negativo ma che è possibile accedere con *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt" -Context $context2
```

È possibile accedere al contenuto del BLOB di archiviazione con un token SAS che abbia accesso in scrittura.

### <a name="relevant-powershell-cmdlets"></a>Cmdlet PowerShell pertinenti

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Onboarding dell'account di archiviazione

Esempio: il proprietario di un oggetto Key Vault aggiunge un oggetto account di archiviazione in Azure Key Vault per eseguire l'onboarding di un account di archiviazione.

Durante il caricamento, Key Vault deve verificare che l'identità dell'account onboarding abbia le autorizzazioni per accedere agli *elenchi* e *rigenerare* le chiavi di archiviazione. Per verificare queste autorizzazioni, Key Vault ottiene un token OBO (per conto di) dal servizio di autenticazione, pubblico impostato su Gestione Risorse di Azure e crea una chiamata chiave dell'*elenco* al servizio di archiviazione di Azure. Se la chiamata di *elenco* ha esito negativo, la creazione dell'oggetto Key Vault ha esito negativo con il codice di stato HTTP *Forbidden*. Le chiavi elencate in questo modo vengono memorizzate nella cache con l'archiviazione di entità key vault.

Key Vault deve verificare che l'identità disponga delle autorizzazioni *rigenera* prima di acquisire la proprietà della rigenerazione delle chiavi. Per verificare che l'identità, tramite il token OBO, nonché l'identità Key Vault propria possieda queste autorizzazioni:

- Key Vault elenca le autorizzazioni RBAC per la risorsa dell'account di archiviazione.
- Key Vault convalida la risposta tramite la corrispondenza mediante espressioni regolari di azioni e non-azioni.

Trovare alcuni esempi di supporto in [Key Vault - Managed Storage Account Keys Samples](https://github.com/Azure/azure-sdk-for-net/blob/psSdkJson6/src/SDKs/KeyVault/dataPlane/Microsoft.Azure.KeyVault.Samples/samples/HelloKeyVault/Program.cs#L167) (Key Vault - Esempi di chiavi dell'account di archiviazione gestito).

Se l'identità non dispone dell'autorizzazione *rigenera* o l'identità Key Vault propria non dispone dell'autorizzazione *elenca* o *rigenera*, la richiesta di onboarding ha esito negativo restituendo un messaggio e codice di errore appropriati.

Il token OBO funziona solo quando si usano applicazioni client native e proprie di PowerShell o dell'interfaccia della riga di comando.

## <a name="other-applications"></a>Altre applicazioni

- I token SAS, costruiti usando le chiavi dell'account di archiviazione Key Vault, forniscono un accesso ancora più controllato a un account di archiviazione di Azure. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Vedere anche 

- [Informazioni su chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog del team di Key Vault](https://blogs.technet.microsoft.com/kv/)
