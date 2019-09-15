---
title: Gestire chiavi dell'account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure
description: Le chiavi dell'account di archiviazione garantiscono una perfetta integrazione tra Azure Key Vault e l'accesso basato su chiavi a un account di archiviazione di Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 03/01/2019
ms.openlocfilehash: 68c9279333e9ee92cbca1034f70801c153b6cdb8
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000542"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gestire chiavi dell'account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure 

Azure Key Vault gestisce le chiavi per gli account di archiviazione di Azure e gli account di archiviazione classici. Per completare diverse funzioni di gestione delle chiavi, è possibile usare la funzionalità dell'account di archiviazione gestito Key Vault.

Un'[account di Archiviazione Azure](/azure/storage/storage-create-storage-account) usa credenziali costituite da un nome account e una chiave. La chiave viene generata automaticamente e funge da password, anziché come chiave crittografica. Key Vault gestisce le chiavi degli account di archiviazione archiviando tali chiavi come [Key Vault segreti](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Le chiavi sono elencate (sincronizzate) con un account di archiviazione di Azure e vengono periodicamente rigenerate o _ruotate_. 

Quando si usa la funzionalità chiave dell'account di archiviazione gestita, prendere in considerazione i punti seguenti:

- I valori di chiave non vengono mai restituiti in risposta a un chiamante.
- Solo Key Vault deve gestire le chiavi dell'account di archiviazione. Non gestire personalmente le chiavi ed evitare di interferire con Key Vault processi.
- Solo un singolo oggetto Key Vault deve gestire le chiavi dell'account di archiviazione. Non consentire la gestione delle chiavi da più oggetti.
- È possibile richiedere Key Vault per gestire l'account di archiviazione con un'entità utente, ma non con un'entità servizio.
- Rigenerare le chiavi usando solo Key Vault. Non rigenerare manualmente le chiavi dell'account di archiviazione. 

> [!NOTE]
> L'integrazione di archiviazione di Azure con Azure Active Directory (Azure AD) è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud.
> L'integrazione di Azure AD è disponibile per i [BLOB e le code di Azure](../storage/common/storage-auth-aad.md).
> Usare Azure AD per l'autenticazione e l'autorizzazione.
> Azure AD fornisce l'accesso in base al token di OAuth2 ad archiviazione di Azure, proprio come Azure Key Vault.
>
> Azure AD consente di autenticare l'applicazione client usando un'identità dell'applicazione o dell'utente, anziché le credenziali dell'account di archiviazione.
> È possibile usare un' [identità gestita Azure ad](/azure/active-directory/managed-identities-azure-resources/) quando si esegue in Azure. Le identità gestite eliminano la necessità dell'autenticazione client e l'archiviazione delle credenziali in o con l'applicazione.
> Azure AD usa il controllo degli accessi in base al ruolo (RBAC) per gestire l'autorizzazione, supportata anche da Key Vault.

### <a name="service-principal-application-id"></a>ID applicazione dell'entità servizio

Un tenant Azure AD fornisce a ogni applicazione registrata un' [entità servizio](/azure/active-directory/develop/developer-glossary#service-principal-object). L'entità servizio funge da identità dell'applicazione (ID). L'ID applicazione viene usato durante la configurazione dell'autorizzazione per l'accesso ad altre risorse di Azure tramite RBAC.

Key Vault è un'applicazione Microsoft già registrata in tutti i tenant di Azure AD. Il Key Vault viene registrato con lo stesso ID applicazione e all'interno di ogni cloud di Azure.

| tenant | Cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altro  | Any | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Prerequisiti

Prima di usare Key Vault per gestire la chiave dell'account di archiviazione, esaminare i prerequisiti:

- Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Creare un [account di archiviazione di Azure](https://azure.microsoft.com/services/storage/). Attenersi alla [seguente procedura](../storage/index.yml).
- Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.        
      
## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazione

Per gestire le chiavi degli account di archiviazione, è necessario eseguire quattro passaggi Key Vault di base:

1. Ottenere un account di archiviazione esistente.
1. Recuperare un insieme di credenziali delle chiavi esistente.
1. Aggiungere un Key Vault account di archiviazione gestito all'insieme di credenziali. Imposta `key1` come chiave attiva con un periodo di rigenerazione di 90 giorni.
1. Usare `key1` per impostare un contesto di archiviazione per l'account di archiviazione specificato.

> [!NOTE]
> Key Vault come servizio vengono assegnate le autorizzazioni di operatore per l'account di archiviazione.
> 
> Dopo aver configurato Azure Key Vault chiavi dell'account di archiviazione gestita, modificare solo le chiavi usando Key Vault.
> Per le chiavi dell'account di archiviazione gestito, Key Vault gestisce la rotazione della chiave dell'account di archiviazione.

1. Dopo aver creato un account di archiviazione, eseguire il comando seguente per ottenere l'ID risorsa dell'account di archiviazione da gestire:
    ```
    az storage account show -n storageaccountname
    ```

    Copiare il valore di ID risorsa dall'output del comando:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Output di esempio:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Assegnare il ruolo "ruolo del servizio operatore chiave dell'account di archiviazione" per Key Vault. Questo ruolo limita l'ambito di accesso all'account di archiviazione. Per un account di archiviazione classico, usare il ruolo "ruolo del servizio operatore chiave dell'account di archiviazione classico".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>"
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74`è l'ID oggetto per Key Vault nel cloud pubblico di Azure. Per ottenere l'ID oggetto per Key Vault nel cloud di Azure per enti pubblici, vedere [ID applicazione dell'entità servizio](#service-principal-application-id).
    
1. Creare un account di archiviazione gestito Key Vault:

    Impostare un periodo di rigenerazione di 90 giorni. Dopo 90 giorni, Key Vault Rigenera `key1` e scambia la chiave attiva da `key2` a `key1`. `key1`viene quindi contrassegnato come chiave attiva. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Creazione e generazione di token

È anche possibile richiedere Key Vault per generare token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione senza condividere le chiavi dell'account. Una firma di accesso condiviso offre un modo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione completano le azioni seguenti:

- Impostare la definizione `<YourSASDefinitionName>`di una firma di accesso condiviso dell'account. La definizione viene impostata in un Key Vault account `<YourStorageAccountName>` di archiviazione gestito nell'insieme di credenziali delle `<VaultName>`chiavi.
- Creare un token di firma di accesso condiviso dell'account per i servizi BLOB, file, tabelle e di Accodamento. Il token viene creato per i tipi di risorse Service, container e Object. Il token viene creato con tutte le autorizzazioni, su HTTPS e con le date di inizio e di fine specificate.
- Impostare un Key Vault definizione di firma di accesso condiviso di archiviazione gestita nell'insieme di credenziali. La definizione ha l'URI del modello del token della firma di accesso condiviso che è stato creato. La definizione ha il tipo `account` di firma di accesso condiviso ed è valida per N giorni.
- Recuperare il token di accesso effettivo dal segreto Key Vault che corrisponde alla definizione della firma di accesso condiviso.

Dopo aver completato i passaggi descritti nella sezione precedente, eseguire i comandi seguenti per richiedere Key Vault di generare token di firma di accesso condiviso. 

1. Creare una definizione di firma di accesso condiviso. Dopo aver creato la definizione della firma di accesso condiviso, richiedere Key Vault per generare più token di firma di accesso condiviso. Questa operazione richiede le `storage` autorizzazioni `setsas` e.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Per informazioni sull'operazione, vedere la documentazione di riferimento [AZ storage account generate-SAS](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) .

    Quando l'operazione viene eseguita correttamente, copiare l'output.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Usare l' `$sasToken` oggetto generato dal comando precedente e creare una definizione di firma di accesso condiviso. Per altre informazioni sui parametri del comando, vedere la documentazione di riferimento per la creazione di informazioni di riferimento per il comando [AZ per Vault storage SAS-Definition](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) .
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Quando l'utente non dispone delle autorizzazioni per l'account di archiviazione, ottenere innanzitutto l'ID oggetto dell'utente:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Recuperare i token nel codice

Eseguire operazioni sull'account di archiviazione recuperando i [token di firma di accesso condiviso](../storage/common/storage-dotnet-shared-access-signature-part-1.md) da Key Vault.

Esistono tre modi per eseguire l'autenticazione a Key Vault:

- Usare un'identità del servizio gestito. Questo approccio è altamente consigliato.
- Usare un'entità servizio e un certificato. 
- Usare un'entità servizio e una password. Questo approccio non è consigliato.

Per ulteriori informazioni, vedere [Azure Key Vault: Concetti](basic-concepts.md)di base.

Nell'esempio seguente viene illustrato come recuperare i token della firma di accesso condiviso. I token vengono recuperati dopo la creazione di una definizione di firma di accesso condiviso. 

```cs
// After you get a security token, create KeyVaultClient with vault credentials.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a shared access signature token for your storage from Key Vault.
// The format for SecretUri is https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials by using the shared access signature token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se il token della firma di accesso condiviso sta per scadere, recuperare di nuovo il token di firma di accesso condiviso da Key Vault e aggiornare il codice.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Comandi dell'interfaccia della riga di comando di Azure

Per informazioni sui comandi dell'interfaccia della riga di comando di Azure rilevanti per gli account di archiviazione gestiti, vedere la documentazione di riferimento per l' [archiviazione AZ](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/).
- Esaminare gli articoli nel [Blog del team di Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
