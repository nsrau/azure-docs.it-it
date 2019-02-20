---
ms.assetid: ''
title: Account di archiviazione gestita di Azure Key Vault - Interfaccia della riga di comando
description: Le chiavi dell'account di archiviazione forniscono un'integrazione diretta tra Azure Key Vault e l'accesso basato sulla chiave dell'account di Archiviazione di Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: prashanthyv
ms.author: pryerram
manager: barbkess
ms.date: 10/03/2018
ms.openlocfilehash: 9b1a4e23ed0da0637b44ac52dd4d1baeb22cd6ce
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56118055"
---
# <a name="azure-key-vault-managed-storage-account---cli"></a>Account di archiviazione gestita di Azure Key Vault - Interfaccia della riga di comando

> [!NOTE]
> [L'integrazione di Archiviazione di Azure con Azure Active Directory (Azure AD) è ora disponibile in anteprima](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Si consiglia di usare Azure AD per le fasi di autenticazione e autorizzazione, poiché offre l'accesso basato sul token OAuth2 ad Archiviazione di Azure, proprio come Azure Key Vault. In questo modo è possibile:
> - Autenticare l'applicazione client con un'identità di applicazione o utente, anziché con le credenziali dell'account di archiviazione. 
> - Usare un'[identità gestita di Azure AD](/azure/active-directory/managed-identities-azure-resources/) per l'esecuzione in Azure. Le identità gestite eliminano la necessità di eseguire l'autenticazione di tutti i client e di archiviare le credenziali nell'applicazione.
> - Usare il controllo degli accessi in base al ruolo per la gestione delle autorizzazioni, che è supportato anche da Key Vault.

- Azure Key Vault gestisce le chiavi di un account di archiviazione di Azure.
    - Internamente Key Vault consente di elencare (sincronizzazione) le chiavi con un account di archiviazione di Azure.    
    - Key Vault rigenera (ruota) le chiavi periodicamente.
    - I valori di chiave non vengono mai restituiti in risposta al chiamante.
    - Key Vault gestisce le chiavi sia degli account di archiviazione che degli account di archiviazione classici.
    
> [!IMPORTANT]
> Un tenant Azure AD assegna ad ogni applicazione registrata un'**[entità servizio](/azure/active-directory/develop/developer-glossary#service-principal-object)**, che svolge la funzione di identità dell'applicazione. Viene usato l'ID di applicazione dell'entità servizio per concedere a tale entità l'autorizzazione ad accedere ad altre risorse di Azure tramite il controllo degli accessi in base al ruolo. Key Vault è un'applicazione Microsoft pre-registrata in tutti i tenant di Azure AD con lo stesso ID di applicazione all'interno di ogni cloud di Azure:
> - I tenant di Azure AD nel cloud di Azure per enti pubblici usano l'ID di applicazione `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - I tenant di Azure AD nel cloud pubblico di Azure e in tutti gli altri usano l'ID di applicazione `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

<a name="prerequisites"></a>Prerequisiti
--------------
1. [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) Installare l'interfaccia della riga di comando di Azure   
2. [Creare un account di archiviazione](https://azure.microsoft.com/services/storage/)
    - Seguire i passaggi in questo [documento](https://docs.microsoft.com/azure/storage/) per creare un account di archiviazione  
    - **Linee guida sulla denominazione:** I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Istruzioni dettagliate su come usare Key Vault per gestire le chiavi dell'account di archiviazione
--------------------------------------------------------------------------------
A livello concettuale i passaggi seguiti sono
- Si ottiene prima un account di archiviazione (pre-esistente)
- Quindi si recupera un insieme di credenziali delle chiavi (pre-esistente)
- Quindi si aggiunge un account di archiviazione gestito da KeyVault per l'insieme di credenziali, impostando Key1 come chiave attiva, e con un periodo di rigenerazione di 180 giorni
- Infine si imposta un contesto di archiviazione per l'account di archiviazione specificato, con Key1

Nelle istruzioni seguenti si assegna Key Vault come servizio per avere autorizzazioni di operatore per l'account di archiviazione

> [!NOTE]
> Si noti che dopo aver configurato le chiavi dell'account di archiviazione gestita di Azure Key Vault, tali chiavi possono essere modificate **SOLO** tramite Key Vault. Le chiavi dell'account di archiviazione gestita consentono a Key Vault di gestire la rotazione della chiave dell'account di archiviazione.

1. Dopo aver creato un account di archiviazione, eseguire il comando seguente per ottenere l'ID risorsa dell'account di archiviazione da gestire

    ```
    az storage account show -n storageaccountname 
    ```
    Copiare il campo ID dal risultato del comando precedente
    
2. Ottenere l'ID oggetto dell'entità servizio di Azure Key Vault eseguendo il comando seguente

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
    Al termine di questo comando trovare l'ID oggetto nel risultato:
    ```console
        {
            ...
            "objectId": "93c27d83-f79b-4cb2-8dd4-4aa716542e74"
            ...
        }
    ```
    
3. Assegnare il ruolo Operatore chiave di archiviazione all'identità di Azure Key Vault.

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ObjectIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Creare un account di archiviazione gestito da Key Vault.     <br /><br />
   Il periodo di rigenerazione impostato di seguito è di 90 giorni. Dopo 90 giorni, Key Vault rigenera 'key1' e passa l'impostazione di chiave attiva da 'key2' a 'key1'. A questo punto contrassegnerà Key1 come chiave attiva. 
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id <Id-of-storage-account>
    ```
    Nel caso in cui l'utente non abbia creato l'account di archiviazione e non abbia le autorizzazioni per tale account, la procedura seguente consente di impostare le autorizzazioni per l'account, in modo da garantire la possibilità di gestire tutte le autorizzazioni di archiviazione in Key Vault.
    

<a name="step-by-step-instructions-on-how-to-use-key-vault-to-create-and-generate-sas-tokens"></a>Istruzioni dettagliate su come usare Key Vault per creare e gestire i token di firma di accesso condiviso
--------------------------------------------------------------------------------
È anche possibile chiedere a Key Vault di generare i token di firma di accesso condiviso (SAS). Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. Con una firma di accesso condiviso è possibile concedere ai client l'accesso alle risorse nell'account di archiviazione, senza condividere le chiavi dell'account. Questo è il punto chiave associato all'uso delle firme di accesso condiviso nelle applicazioni: una firma di accesso condiviso rappresenta un metodo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

Dopo aver completato i passaggi elencati sopra, è possibile eseguire i comandi seguenti per chiedere a Key Vault di generare i token di firma di accesso condiviso per l'utente. 

L'elenco di cose che verranno eseguite nei passaggi seguenti sono
- Imposta una definizione di firma di accesso condiviso dell'account denominato "<YourSASDefinitionName>" in un account di archiviazione gestito da KeyVault "<YourStorageAccountName>" nell'insieme di credenziali "<VaultName>". 
- Crea un token di firma di accesso condiviso dell'account per i servizi BLOB, File, Tabella e Coda, per i tipi di risorsa Servizio, Contenitore e Oggetto, con tutte le autorizzazioni, tramite https e con le date di inizio e fine specificate
- Imposta una definizione di firma di accesso condiviso di archiviazione gestita da KeyVault nell'insieme di credenziali, con l'uri del modello come token di firma di accesso condiviso creato in precedenza, del tipo di firma di accesso condiviso "account" e valido per N giorni
- Recupera il token di accesso effettivo dal segreto di Key Vault corrispondente alla definizione di firma di accesso condiviso

1. In questo passaggio si creerà una definizione di firma di accesso condiviso. Dopo aver creato la definizione di firma di accesso condiviso, è possibile chiedere a Key Vault di generare più token di firma di accesso condiviso per l'utente. Questa operazione richiede l'autorizzazione di archiviazione/setsas.

```
$sastoken = az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name storageacct --account-key 00000000
```
È possibile visualizzare altre informazioni sull'operazione precedente [qui](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas)

Quando questa operazione viene eseguita correttamente, si dovrebbe visualizzare un output simile a quello illustrato di seguito. Copiarlo

```console
   "se=2020-01-01&sp=***"
```

2. In questo passaggio si userà l'output ($sasToken) generato in precedenza per creare una definizione di firma di accesso condiviso. Per altre informazioni, leggere [qui](https://docs.microsoft.com/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#required-parameters)   

```
az keyvault storage sas-definition create --vault-name <YourVaultName> --account-name <YourStorageAccountName> -n <NameOfSasDefinitionYouWantToGive> --validity-period P2D --sas-type account --template-uri $sastoken
```
                        

 > [!NOTE] 
 > Nel caso in cui l'utente non abbia le autorizzazioni per l'account di archiviazione, prima si ottiene l'ID oggetto dell'utente

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    
    ```
    
## <a name="fetch-sas-tokens-in-code"></a>Recuperare i token di firma di accesso condiviso nel codice

In questa sezione verrà illustrato come poter eseguire operazioni sull'account di archiviazione recuperando [token di firma di accesso condiviso](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) da Key Vault

Nella sezione seguente si illustra come recuperare i token di firma di accesso condiviso dopo aver creato una definizione di firma di accesso condiviso, come illustrato in precedenza.

> [!NOTE] 
  Come descritto nell'articolo [Concetti di base](key-vault-whatis.md#basic-concepts), esistono tre modi per eseguire l'autenticazione in Key Vault:
> - Usando l'identità del servizio gestita (altamente consigliato)
> - Usando l'entità servizio e il certificato 
> - Usando l'entità servizio e la password (NON consigliato)

```cs
// Once you have a security token from one of the above methods, then create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault. SecretUri is of the format https://<VaultName>.vault.azure.net/secrets/<ExamplePassword>
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();
```

Se il token di firma di accesso condiviso è prossimo alla scadenza, è opportuno recuperare di nuovo il token di firma di accesso condiviso da Key Vault e aggiornare il codice

```cs
// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

### <a name="relevant-azure-cli-commands"></a>Comandi rilevanti dell'interfaccia della riga di comando di Azure

[Comandi di archiviazione per l'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

## <a name="see-also"></a>Vedere anche 

- [Informazioni su chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog del team di Key Vault](https://blogs.technet.microsoft.com/kv/)
