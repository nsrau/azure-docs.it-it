---
ms.assetid: 
title: Chiavi dell'account di archiviazione Key Vault
description: Le chiavi dell'account di archiviazione forniscono un'integrazione diretta tra Azure Key Vault e l'accesso basato sulla chiave dell'account di Archiviazione di Azure.
ms.topic: article
services: key-vault
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 10/12/2017
ms.openlocfilehash: 1d92ffc03b60695c5ff7b6c3d2ac54808c527efd
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-key-vault-storage-account-keys"></a>Chiavi dell'account di archiviazione Key Vault

Prima delle chiavi dell'account di archiviazione Key Vault gli sviluppatori dovevano gestire le proprie chiavi dell'account di archiviazione di Azure (ASA) e ruotarle manualmente o mediante automazione esterna. Al momento, le chiavi dell'account di archiviazione di Key Vault sono implementate come [segreti di Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) per l'autenticazione con un account di archiviazione di Azure. 

La funzionalità chiave di Account di Archiviazione di Azure (ASA) consente di gestire la rotazione dei segreti al posto dell'utente. Elimina anche la necessità di un contatto diretto con una chiave dell'account di archiviazione di Azure offrendo come metodo firme di accesso condiviso. 

Per informazioni più generali sugli account di archiviazione di Azure, vedere [Informazioni sugli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfacce di supporto

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
 
```powershell
//create an Azure Storage Account using a connection string containing an account name and a storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Dopo le chiavi di archiviazione Key Vault 

```powershell
//Make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Get secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  

-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get a SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If your SAS token is about to expire, Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Materiale sussidiario per sviluppatori

- Consentire solo a Key Vault di gestire le chiavi ASA. Non tentare di gestirle manualmente onde evitare di interferire con i processi di Key Vault. 
- Non consentire alle chiavi ASA di essere gestite da più di un oggetto di Key Vault. 
- Se è necessario rigenerare manualmente le chiavi ASA, è consigliabile rigenerarle tramite Key Vault. 

## <a name="getting-started"></a>Introduzione

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configurazione per le autorizzazioni di controllo degli accessi in base al ruolo (RBAC)

Per *elencare* e *rigenerare* le chiavi per un account di archiviazione, l'identità di applicazione di Azure Key Vault necessita di autorizzazioni. Impostare queste autorizzazioni usando la procedura seguente:

- Ottenere l'ObjectId dell'identità di Azure Key Vault: 

    `Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093`

- Assegnare il ruolo "Operatore chiave di archiviazione" all'identità di Azure Key Vault: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Per un tipo di account classico, impostare il parametro del ruolo su *"Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico"*.

## <a name="working-example"></a>Esempio di funzionamento

L'esempio seguente illustra la creazione di un account di archiviazione di Azure gestito da Key Vault e le definizioni di firma di accesso condiviso (SAS) associate.

### <a name="assumptions"></a>Presupposti

Per questo esempio di funzionamento, vengono fornite le istruzioni seguenti.

- La risorsa di archiviazione si trova in: */subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1*

- Il nome dell'insieme di credenziali delle chiavi è: *yourtest1*

### <a name="get-a-service-principal"></a>Ottenere un'entità servizio

```powershell
$yourKeyVaultServicePrincipalId = (Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

L'output del comando precedente includerà l'entità servizio, denominata *yourKeyVaultServicePrincipalId*. 

### <a name="set-permissions"></a>Impostare le autorizzazioni

Verificare che le autorizzazioni di archiviazione siano impostate su *tutte*. È possibile ottenere yourKeyVaultServicePrincipalId e impostare le autorizzazioni nell'insieme di credenziali tramite i comandi seguenti.

```powershell
Get-AzureRmADUser -SearchString "your name"
```
Cercare ora il nome e ottenere il relativo ObjectId da usare per l'impostazione delle autorizzazioni nell'insieme di credenziali.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourtest1' -ObjectId $yourKeyVaultServicePrincipalId -PermissionsToStorage all
```

### <a name="allow-access"></a>Consentire l'accesso

Prima di poter creare un account di archiviazione gestito e le definizioni di firma di accesso condiviso, è necessario concedere al servizio Key Vault l'accesso agli account di archiviazione.

```powershell
New-AzureRmRoleAssignment -ObjectId $yourKeyVaultServicePrincipalId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '/subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1'
```

### <a name="create-storage-account"></a>Crea account di archiviazione

Creare ora un account di archiviazione gestito e due definizioni di firma di accesso condiviso. La firma di accesso condiviso dell'account consente di accedere al servizio BLOB con autorizzazioni diverse.

```powershell
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="regeneration"></a>Rigenerazione

Impostazione del periodo di rigenerazione tramite i comandi seguenti.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName yourtest1 -Name msak01 -AccountResourceId /subscriptions/subscriptionId/resourceGroups/yourresgroup1/providers/Microsoft.Storage/storageAccounts/yourtest1 -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>Impostare le definizioni di firma di accesso condiviso

Impostare le definizioni di firma di accesso condiviso in Key Vault per l'account di archiviazione gestito.

```powershell
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourtest1  -AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List
Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName yourtest1  -AccountName msak01 -Name blobsas2 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List,Write
```

### <a name="get-token"></a>Ottenere un token

Ottenere i token SAS corrispondenti ed eseguire chiamate all'account di archiviazione.

```powershell
$sasToken1 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas1).SecretValueText
$sasToken2 = (Get-AzureKeyVaultSecret -VaultName yourtest1 -SecretName msak01-blobsas2).SecretValueText
```

### <a name="create-storage"></a>Creazione dell'account di archiviazione

Si noti che il tentativo di accesso con *$sastoken1* ha esito negativo, è possibile accedere con *$sastoken2*. 

```powershell
$context1 = New-AzureStorageContext -SasToken $sasToken1 -StorageAccountName yourtest1
$context2 = New-AzureStorageContext -SasToken $sasToken2 -StorageAccountName yourtest1
Set-AzureStorageBlobContent -Container containertest1 -File "abc.txt"  -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "file.txt"  -Context $context2
```

### <a name="example-summary"></a>Riepilogo dell'esempio

È possibile accedere al contenuto del BLOB di archiviazione con un token SAS che abbia accesso in scrittura.

### <a name="relevant-powershell-cmdlets"></a>Cmdlet PowerShell pertinenti

- [Get-AzureKeyVaultManagedStorageAccount ](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount?view=azurermps-4.3.1)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount?view=azurermps-4.3.1)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition?view=azurermps-4.3.1)

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
