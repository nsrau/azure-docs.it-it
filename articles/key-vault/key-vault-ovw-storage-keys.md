---
title: Gestire le chiavi dell'account di archiviazione con l'insieme di credenziali delle chiavi di Azure e l'interfaccia della riga di comando di AzureManage storage account keys with Azure Key Vault and the
description: Le chiavi dell'account di archiviazione forniscono una perfetta integrazione tra L'insieme di credenziali delle chiavi di Azure e l'accesso basato su chiave a un account di archiviazione di Azure.Storage account keys provide seamless integration between Azure Key Vault and key-based access to an Azure storage account.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 104f3423b07eaa3269ffccc054cd2f779bbdabf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78199819"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gestire le chiavi dell'account di archiviazione con Key Vault e l'interfaccia della riga di comando di AzureManage storage account keys with Key Vault and the Azure CLI

Un account di archiviazione di Azure usa credenziali che comprendono un nome di account e una chiave. La chiave viene generata automaticamente e funge da password, anziché come chiave crittografica. L'insieme di credenziali delle chiavi gestisce le chiavi dell'account di archiviazione archiviandole come [segreti dell'insieme di](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets)credenziali delle chiavi. 

È possibile usare la funzionalità chiave dell'account di archiviazione gestito dell'insieme di credenziali delle chiavi per elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) le chiavi periodicamente. È possibile gestire le chiavi sia per gli account di archiviazione che per gli account di archiviazione classici.

Quando si usa la funzionalità della chiave dell'account di archiviazione gestita, considerare i punti seguenti:When you use the managed storage account key feature, consider the following points:

- I valori delle chiavi non vengono mai restituiti in risposta a un chiamante.
- Solo l'insieme di credenziali delle chiavi deve gestire le chiavi dell'account di archiviazione. Non gestire le chiavi da solo ed evitare di interferire con i processi di Key Vault.
- Solo un singolo oggetto Key Vault deve gestire le chiavi dell'account di archiviazione. Non consentire la gestione delle chiavi da più oggetti.
- È possibile richiedere l'insieme di credenziali delle chiavi per gestire l'account di archiviazione con un'entità utente, ma non con un'entità servizio.
- Rigenerare le chiavi utilizzando solo l'insieme di credenziali delle chiavi. Non rigenerare manualmente le chiavi dell'account di archiviazione.

È consigliabile usare l'integrazione di Archiviazione di Azure con Azure Active Directory (Azure AD), il servizio di gestione delle identità e degli accessi basato su cloud di Microsoft.We recommend using Azure Storage integration with Azure Active Directory (Azure AD), s microsoft's cloud-based identity and access management service. L'integrazione di Azure AD è disponibile per [i BLOB e le code](../storage/common/storage-auth-aad.md)di Azure e fornisce l'accesso basato su token OAuth2 ad Archiviazione di Azure (proprio come Archiviazione delle chiavi di Azure).

Azure AD consente di autenticare l'applicazione client usando un'identità dell'applicazione o dell'utente anziché le credenziali dell'account di archiviazione. È possibile usare [un'identità gestita di Azure AD](/azure/active-directory/managed-identities-azure-resources/) quando si esegue in Azure.You can use an Azure AD managed identity when you run on Azure. Le identità gestite eliminano la necessità di autenticazione client e l'archiviazione delle credenziali all'utente o con l'applicazione.

Azure AD usa il controllo degli accessi in base al ruolo per gestire l'autorizzazione, supportata anche dall'insieme di credenziali delle chiavi.

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

- [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli)
- [Creare un insieme di credenziali delle chiaviCreate a key vault](quick-create-cli.md)
- Creare un account di archiviazione di [Azure.Create an Azure storage account](../storage/common/storage-account-create.md?tabs=azure-cli). Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.
      
## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazioneManage storage account keys

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione dell'interfaccia della riga di comando di Azure usando i comandi [di accesso az.Authenticate](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) your Azure CLI session using the az login commands.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Concedere a Key Vault l'accesso all'account di archiviazione

Usare il comando Azure CLI [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest) per concedere a Key Vault di accedere all'account di archiviazione. Fornire al comando i seguenti valori di parametro:

- `--role`: passare il ruolo RBAC "Ruolo del servizio operatore chiavi dell'account di archiviazione". Questo ruolo limita l'ambito di accesso all'account di archiviazione. Per un account di archiviazione classico, passare invece "Ruolo del servizio operatore di chiave dell'account di archiviazione classico".
- `--assignee-object-id`: passare il valore "93c27d83-f79b-4cb2-8dd4-4aa716542e74", ovvero l'ID oggetto per l'insieme di credenziali delle chiavi nel cloud pubblico di Azure. Per ottenere l'ID oggetto per l'insieme di credenziali delle chiavi nel cloud di Azure per enti pubblici, vedere [ID applicazione entità servizio.](#service-principal-application-id)
- `--scope`: consente di passare l'ID risorsa `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`dell'account di archiviazione, che è nel formato . Per trovare l'ID sottoscrizione, usare il comando Elenco account dell'interfaccia della riga di comando di [Azure.To](/cli/azure/account?view=azure-cli-latest#az-account-list) find your subscription ID, use the Azure CLI az account list command. Per trovare il nome dell'account di archiviazione e il gruppo di risorse dell'account di archiviazione, usare il comando elenco di account di archiviazione dell'interfaccia della riga di comando di [Azure.To](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) find your storage account name and storage account resource group, use the Azure CLI az storage account list command.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Creare un account di archiviazione gestito di Key VaultCreate a Key Vault Managed storage account

 Creare un account di archiviazione gestito dell'insieme di credenziali delle chiavi usando il comando di archiviazione keyvault dell'interfaccia della riga di comando di Azure.Create a Key Vault managed storage account using the Azure CLI [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) command. Impostare un periodo di rigenerazione di 90 giorni. Dopo 90 giorni, Key `key1` Vault rigenera e `key2` `key1`scambia la chiave attiva da a . `key1`viene quindi contrassegnato come tasto attivo. Fornire al comando i seguenti valori di parametro:

- `--vault-name`: consente di passare il nome dell'insieme di credenziali delle chiavi. Per trovare il nome dell'insieme di credenziali delle chiavi, usare il comando keyvault list dell'interfaccia della riga di comando di [Azure.To](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) find the name of your key vault, use the Azure CLI az keyvault list command.
- `-n`: consente di passare il nome dell'account di archiviazione. Per trovare il nome dell'account di archiviazione, usare il comando dell'elenco di account di archiviazione dell'interfaccia della riga di comando di [Azure.To](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) find the name of your storage account, use the Azure CLI az storage account list command.
- `--resource-id`: consente di passare l'ID risorsa `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`dell'account di archiviazione, che è nel formato . Per trovare l'ID sottoscrizione, usare il comando Elenco account dell'interfaccia della riga di comando di [Azure.To](/cli/azure/account?view=azure-cli-latest#az-account-list) find your subscription ID, use the Azure CLI az account list command. Per trovare il nome dell'account di archiviazione e il gruppo di risorse dell'account di archiviazione, usare il comando elenco di account di archiviazione dell'interfaccia della riga di comando di [Azure.To](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) find your storage account name and storage account resource group, use the Azure CLI az storage account list command.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Token di firma di accesso condivisoShared access signature tokens

È inoltre possibile chiedere a Key Vault di generare token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione senza condividere le chiavi dell'account. Una firma di accesso condiviso offre un modo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione completano le seguenti azioni:

- Impostare una definizione `<YourSASDefinitionName>`di firma di accesso condiviso dell'account . La definizione viene impostata su `<YourStorageAccountName>` un account `<YourKeyVaultName>`di archiviazione gestito dell'insieme di credenziali delle chiavi nell'insieme di credenziali delle chiavi.
- Creare un token di firma di accesso condiviso dell'account per i servizi BLOB, file, tabella e coda. Il token viene creato per i tipi di risorse Service, Container e Object.The token is created for resource types Service, Container, and Object. Il token viene creato con tutte le autorizzazioni, su https e con le date di inizio e di fine specificate.
- Impostare una definizione di firma di accesso condiviso di archiviazione gestita dell'insieme di credenziali delle chiavi nell'insieme di credenziali. La definizione ha l'URI del modello del token di firma di accesso condiviso creato. La definizione ha il `account` tipo di firma di accesso condiviso ed è valida per N giorni.
- Verificare che la firma di accesso condiviso sia stata salvata nell'insieme di credenziali delle chiavi come segreto.

### <a name="create-a-shared-access-signature-token"></a>Creare un token di firma di accesso condivisoCreate a shared access signature token

Creare una definizione di firma di accesso condiviso usando il comando [generate-sas dell'account di archiviazione](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) dell'interfaccia della riga di comando di Azure.Create a shared access signature definition using the Azure CLI az storage account generate-sas command. Questa operazione `storage` richiede `setsas` le autorizzazioni e .


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Dopo aver eseguito correttamente l'operazione, copiare l'output.

```console
"se=2020-01-01&sp=***"
```

Questo output sarà il `--template-id` passato al parametro nel passaggio successivo.

### <a name="generate-a-shared-access-signature-definition"></a>Generare una definizione di firma di accesso condivisoGenerate a shared access signature definition

Usare il comando [sas-definition create dell'interfaccia](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) della riga di comando `--template-id` az vault di Azure, passando l'output del passaggio precedente al parametro, per creare una definizione della firma di accesso condiviso.  È possibile fornire il nome `-n` desiderato al parametro.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificare la definizione della firma di accesso condivisoVerify the shared access signature definition

È possibile verificare che la definizione della firma di accesso condiviso sia stata archiviata nell'insieme di credenziali delle chiavi usando [l'elenco dei segreti keyvault dell'interfaccia](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) della riga di comando di Azure a keyvault e i comandi di visualizzazione dei segreti keyvault az.You can verify that the shared access signature definition has been stored in your key vault using the Azure CLI az keyvault secret list and az [keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) commands.

Individuare innanzitutto la definizione della firma di accesso condiviso nell'insieme di credenziali delle chiavi usando il comando [az keyvault secret list.](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list)

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Il segreto corrispondente alla definizione di sAS avrà queste proprietà:The secret corresponding to your SAS definition will have these properties:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

È ora possibile utilizzare il comando [az keyvault secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e la `id` proprietà per visualizzare il contenuto di tale segreto.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

L'output di questo comando mostrerà`value`la stringa di definizione della sAS come .


## <a name="next-steps"></a>Passaggi successivi

- Ulteriori informazioni su [chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/).
- Esaminare gli articoli sul blog del [team di Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Vedere la documentazione di riferimento [sull'archiviazione az keyvault.See](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) the az keyvault storage reference documentation.
