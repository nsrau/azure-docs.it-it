---
title: Gestire chiavi di account di archiviazione con Azure Key Vault e l'interfaccia CLI di Azure
description: Chiavi dell'account di archiviazione forniscono un'integrazione perfetta tra Azure Key Vault e basato su chiavi di accesso a un account di archiviazione di Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 91cc3f96f9cdd231c38232c972c2628d12b9f4b3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476160"
---
# <a name="manage-storage-account-keys-with-azure-key-vault-and-the-azure-cli"></a>Gestire chiavi di account di archiviazione con Azure Key Vault e l'interfaccia CLI di Azure 

Azure Key Vault gestisce le chiavi per gli account di archiviazione di Azure e account di archiviazione classici. È possibile usare la funzionalità di account di archiviazione gestito di Key Vault per completare diverse funzioni di gestione delle chiavi per l'utente.

Un'[account di Archiviazione Azure](/azure/storage/storage-create-storage-account) usa credenziali costituite da un nome account e una chiave. La chiave viene generata automaticamente e viene usato come una password, anziché un oggetto come una chiave di crittografia. Key Vault gestisce le chiavi di account di archiviazione archiviandoli come [segreti di Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). Le chiavi sono elencate (sincronizzate) con un account di archiviazione di Azure e periodicamente vengono rigenerate oppure _ruotata_. 

Quando si usa la funzionalità di chiavi di account di archiviazione gestita, tenere presente quanto segue:

- I valori di chiave non vengono mai restituiti in risposta a un chiamante.
- Solo a Key Vault deve gestire le chiavi di account di archiviazione. Non gestire personalmente le chiavi ed evitare di interferire con i processi di Key Vault.
- Solo un singolo oggetto Key Vault deve gestire le chiavi di account di archiviazione. Non consentire la gestione delle chiavi da più oggetti.
- È possibile richiedere Key Vault per gestire l'account di archiviazione con un'entità utente, ma non con un'entità servizio.
- Rigenera chiavi solo tramite Key Vault. Don ' t regenerate manualmente le chiavi dell'account di archiviazione. 

> [!NOTE]
> Integrazione di archiviazione di Azure con Azure Active Directory (Azure AD) è basata sul cloud identità e accesso servizio di gestione. di Microsoft
> Integrazione di Azure AD è disponibile per [code e BLOB di Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad).
> Usare Azure AD per l'autenticazione e autorizzazione.
> Azure AD fornisce l'accesso basato su token OAuth2 in archiviazione di Azure come Azure Key Vault.
>
> Azure AD consente di autenticare l'applicazione client usando un'identità utente o dell'applicazione, anziché le credenziali dell'account di archiviazione.
> È possibile usare un [identità di Azure AD gestito](/azure/active-directory/managed-identities-azure-resources/) quando si esegue in Azure. Identità gestite è evitare la necessità per l'autenticazione client e l'archiviazione delle credenziali in o con l'applicazione.
> Azure AD Usa il controllo di accesso basato sui ruoli (RBAC) per gestire l'autorizzazione, che è anche supportato da Key Vault.

### <a name="service-principal-application-id"></a>ID applicazione dell'entità servizio

Un tenant di Azure AD offre ogni applicazione registrata con un [entità servizio](/azure/active-directory/develop/developer-glossary#service-principal-object). L'entità servizio viene utilizzato come identità dell'applicazione (ID). L'ID dell'applicazione viene utilizzato durante l'installazione di autorizzazione per l'accesso alle altre risorse di Azure tramite RBAC.

Key Vault è un'applicazione Microsoft pre-registrato nel tenant di tutte le Azure AD. Insieme di credenziali chiave è registrato con lo stesso ID applicazione e all'interno di ogni cloud Azure.

| Tenant | Cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Peering pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altri  | Qualsiasi | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="prerequisites"></a>Prerequisiti

Prima di usare Key Vault di gestire la chiave di account di archiviazione, esaminare i prerequisiti:

- Installare l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
- Creare un [account di archiviazione Azure](https://azure.microsoft.com/services/storage/). Seguire [questi passaggi](https://docs.microsoft.com/azure/storage/).
- Il nome di account di archiviazione deve usare solo lettere minuscole e numeri. La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.        
      
## <a name="manage-storage-account-keys"></a>Gestione delle chiavi di account di archiviazione

Esistono quattro passaggi di base per usare Key Vault per gestire le chiavi di account di archiviazione:

1. Ottenere un account di archiviazione.
1. Recuperare un insieme di credenziali chiave esistente.
1. Aggiungere un account di archiviazione Key Vault gestita nell'insieme di credenziali. Impostare `key1` come chiave attiva con un periodo di rigenerazione di 180 giorni.
1. Usare `key1` per impostare un contesto di archiviazione per l'account di archiviazione specificato.

> [!NOTE]
> Key Vault come un servizio sono assegnate le autorizzazioni di operatore nell'account di archiviazione.
> 
> Dopo aver impostato le chiavi dell'account di archiviazione gestita di Azure Key Vault, modificare solo le chiavi con Key Vault.
> Per le chiavi di account di archiviazione gestita, Key Vault gestisce la rotazione della chiave dell'account di archiviazione.

1. Dopo aver creato un account di archiviazione, eseguire il comando seguente per ottenere l'ID di risorsa dell'account di archiviazione da gestire:
    ```
    az storage account show -n storageaccountname
    ```

    Copiare il valore dell'ID risorsa dell'output del comando:
    ```
    /subscriptions/<subscription ID>/resourceGroups/ResourceGroup/providers/Microsoft.Storage/storageAccounts/StorageAccountName
    ```

    Output di esempio:
    ```
    "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
    ```
    
1. Assegnare il ruolo RBAC "Ruolo servizio operatore della chiave Account archiviazione" in Key Vault. Questo ruolo limita l'ambito di accesso all'account di archiviazione. Per un account di archiviazione classici, usare il ruolo "Classic archiviazione Account ruolo operatore della chiave del servizio".

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope 93c27d83-f79b-4cb2-8dd4-4aa716542e74
    ```
    
    `93c27d83-f79b-4cb2-8dd4-4aa716542e74` è l'ID oggetto per Key Vault nel cloud pubblico di Azure. Per ottenere l'ID oggetto per Key Vault nel cloud Azure per enti pubblici, vedere [ID applicazione di un'entità servizio](#service-principal-application-id).
    
1. Creare un account di archiviazione gestito insieme di credenziali delle chiavi:

    Impostare un periodo di rigenerazione di 90 giorni. Dopo 90 giorni, Key Vault Rigenera `key1` e la chiave attiva da Scambia `key2` a `key1`. `key1` viene quindi contrassegnato come chiave attiva. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="create-and-generate-tokens"></a>Creare e generare i token

È anche possibile porre Key Vault per generare token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere l'accesso alle risorse client nell'account di archiviazione senza condividere le chiavi dell'account. Una firma di accesso condiviso offre un modo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione completare le azioni seguenti:

- Impostare una definizione di firma di accesso account condiviso `<YourSASDefinitionName>`. La definizione è impostata su un account di archiviazione Key Vault gestita `<YourStorageAccountName>` nell'insieme di credenziali delle chiavi `<VaultName>`.
- Creare un token di firma di accesso condiviso di account per i servizi Blob, File, tabelle e code. Il token viene creato per i tipi di risorse il servizio, contenitore e oggetto. Il token viene creato con tutte le autorizzazioni, tramite https e con le date di inizio e fine specificate.
- Imposta una risorsa di archiviazione di Key Vault gestita condivisi definizione di firma di accesso nell'insieme di credenziali. La definizione contiene il modello URI del token di firma di accesso condiviso che è stato creato. La definizione ha il tipo di firma di accesso condiviso `account` ed è valido per N giorni.
- Recuperare il token di accesso effettivo dal segreto di Key Vault che corrisponde alla definizione della firma di accesso condiviso.

Dopo aver completato i passaggi nella sezione precedente, eseguire i comandi seguenti per chiedere a Key Vault per generare token di firma di accesso condiviso. 

1. Creare una definizione di firma di accesso condiviso. Dopo aver creata la definizione di firma di accesso condiviso, chiedere a Key Vault per generare più token di firma accesso condiviso. Questa operazione richiede la `storage` e `setsas` autorizzazioni.
    ```
    $sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
    ```

    Per altre informazioni sull'operazione, vedere la [az storage account genera sas](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) documentazione di riferimento.

    Dopo l'operazione viene eseguita correttamente, copiare l'output.
    ```console
       "se=2020-01-01&sp=***"
    ```

1. Usare il `$sasToken` generati dal comando precedente e creare una definizione di firma di accesso condiviso. Per altre informazioni sui parametri del comando, vedere la [az keyvault archiviazione sas-definition create](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters) documentazione di riferimento.
    ```
    az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
    ```

    Quando l'utente non dispone delle autorizzazioni per l'account di archiviazione, prima di tutto ottenere l'ID oggetto dell'utente:
    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

## <a name="fetch-tokens-in-code"></a>Recuperare i token nel codice

Eseguire operazioni sull'account di archiviazione recuperando [token di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) da Key Vault.

Esistono tre modi per eseguire l'autenticazione in Key Vault:

- Usare un'identità del servizio gestito. Questo approccio è altamente consigliato.
- Usare un'entità servizio e il certificato. 
- Usare un'entità servizio e la password. Questo approccio non è consigliato.

Per altre informazioni, vedere [Azure Key Vault: Concetti di base](key-vault-whatis.md#basic-concepts).

Nell'esempio seguente viene illustrato come recuperare i token di firma di accesso condiviso. Recuperare i token dopo aver creato una definizione di firma di accesso condiviso. 

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

Se il token di firma di accesso condiviso è sta per scadere, recuperare nuovamente il token di firma di accesso condiviso da Key Vault e aggiornare il codice.

```cs
// If your shared access signature token is about to expire,
// get the shared access signature token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

<!-- Add closing sentences to summarize what the user accomplished in this section. -->

### <a name="azure-cli-commands"></a>Comandi dell'interfaccia della riga di comando di Azure

Per informazioni sui comandi della CLI di Azure rilevanti per gli account di archiviazione gestita, vedere la [archiviazione di az keyvault](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) documentazione di riferimento.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/).
- Esaminare gli articoli nella [blog del team di Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
