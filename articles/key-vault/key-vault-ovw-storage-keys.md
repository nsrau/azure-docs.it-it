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
ms.date: 07/25/2017
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3148088c88236c64e089fd25c98eb8ac7cdcbfea
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---
# <a name="azure-key-vault-storage-account-keys"></a>Chiavi dell'account di archiviazione Key Vault

Prima delle chiavi dell'account di archiviazione Key Vault gli sviluppatori dovevano gestire le proprie chiavi dell'account di archiviazione di Azure (ASA) e ruotarle manualmente o mediante automazione esterna. Al momento, le chiavi dell'account di archiviazione di Key Vault sono implementate come [segreti di Key Vault](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) per l'autenticazione con un account di archiviazione di Azure. 

La funzionalità di chiave ASA gestisce la rotazione segreta per conto dell'utente e rimuove la necessità di un contatto diretto con una chiave SA, offrendo le firme di accesso condiviso (SAS) come metodo. 

Per informazioni più generali sugli account di archiviazione di Azure, vedere [Informazioni sugli account di archiviazione di Azure](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Interfacce di supporto

La funzionalità chiavi dell'account di archiviazione di Azure è inizialmente disponibile attraverso le interfacce REST, .NET/C# e PowerShell. Per altre informazioni, vedere [Documentazione sull'insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/).


## <a name="storage-account-keys-behavior"></a>Ottenere il comportamento delle chiavi dell'account di archiviazione

### <a name="what-key-vault-manages"></a>Ciò che Key Vault gestisce

Key Vault esegue diverse funzioni di gestione interne per conto dell'utente quando si usano chiavi dell'account di archiviazione.

1. Azure Key Vault gestisce le chiavi di un account di archiviazione di Azure. 
    - Internamente Key Vault consente di elencare (sincronizzazione) le chiavi con un account di archiviazione di Azure.  
    - Key Vault rigenera (ruota) le chiavi periodicamente. 
    - I valori di chiave non vengono mai restituiti in risposta al chiamante. 
    - Key Vault gestisce le chiavi sia degli account di archiviazione che degli account di archiviazione classici. 
2. Key Vault consente al proprietario dell'insieme di credenziali/oggetto di creare definizioni SAS (SAS di account o di servizio). 
    - Il valore SAS, creato utilizzando la definizione SAS, viene restituito come una chiave privata tramite il percorso dell'URI REST. Per altre informazioni, vedere [Azure Key Vault storage account operations](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations) (Operazioni dell'account di archiviazione di Azure Key Vault).

### <a name="naming-guidance"></a>Linee guida sulla denominazione

I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.  
 
Un nome di una definizione SAS deve avere da 1 a 102 caratteri e contenere solo i caratteri da 0 a 9, da a a z e da A a Z.

## <a name="developer-experience"></a>Esperienza per sviluppatori 

### <a name="before-azure-key-vault-storage-keys"></a>Prima delle chiavi di archiviazione Key Vault 

Gli sviluppatori in precedenza dovevano eseguire le seguenti procedure con una chiave di account di archiviazione per ottenere l'accesso all'archiviazione di Azure. 
 
 ```
//create storage account using connection string containing account name 
// and the storage key 

var storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
var blobClient = storageAccount.CreateCloudBlobClient();
 ```
 
### <a name="after-azure-key-vault-storage-keys"></a>Dopo le chiavi di archiviazione Key Vault 

```
//Please make sure to set storage permissions appropriately on your key vault
Set-AzureRmKeyVaultAccessPolicy -VaultName 'yourVault' -ObjectId yourObjectId -PermissionsToStorage all

//Use PowerShell command to get Secret URI 

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName yourKV  
-AccountName msak01 -Name blobsas1 -Protocol HttpsOnly -ValidityPeriod ([System.Timespan]::FromDays(1)) -Permission Read,List

//Get SAS token from Key Vault

var secret = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token. 

var accountSasCredential = new StorageCredentials(secret.Value); 

// Use credentials and the Blob storage endpoint to create a new Blob service client. 

var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null); 

var blobClientWithSas = accountWithSas.CreateCloudBlobClient(); 
 
// If SAS token is about to expire then Get sasToken again from Key Vault and update it.

accountSasCredential.UpdateSASToken(sasToken);

  ```
 
 ### <a name="developer-best-practices"></a>Procedure consigliate per lo sviluppatore 

- Consentire solo a Key Vault di gestire le chiavi ASA. Non tentare di gestirle manualmente onde evitare di interferire con i processi di Key Vault. 
- Non consentire alle chiavi ASA di essere gestite da più di un oggetto di Key Vault. 
- Se è necessario rigenerare manualmente le chiavi ASA, è consigliabile rigenerarle tramite Key Vault. 

## <a name="getting-started"></a>Introduzione

### <a name="setup-for-role-based-access-control-rbac-permissions"></a>Configurazione per le autorizzazioni di controllo degli accessi in base al ruolo (RBAC)

Key Vault necessita di autorizzazioni per *elencare* e *rigenerare* le chiavi per un account di archiviazione. Impostare queste autorizzazioni usando la procedura seguente:

- Ottenere ObjectId di Key Vault: 

    `Get-AzureRmADServicePrincipal -SearchString "AzureKeyVault"`

- Assegnare il ruolo "Operatore chiave di archiviazione" all'identità di Azure Key Vault: 

    `New-AzureRmRoleAssignment -ObjectId <objectId of AzureKeyVault from previous command> -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope '<azure resource id of storage account>'`

    >[!NOTE]
    > Per un tipo di account classico, impostare il parametro del ruolo su *"Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico"*.

### <a name="storage-account-onboarding"></a>Onboarding dell'account di archiviazione 

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

