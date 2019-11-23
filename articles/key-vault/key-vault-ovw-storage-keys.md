---
title: Gestire chiavi dell'account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure
description: Le chiavi dell'account di archiviazione garantiscono una perfetta integrazione tra Azure Key Vault e l'accesso basato su chiavi a un account di archiviazione di Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.openlocfilehash: 62faf33dc8b3690036407972e12633e741a85d78
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176758"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gestire chiavi dell'account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure

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

## <a name="service-principal-application-id"></a>ID applicazione dell'entità servizio

Un tenant Azure AD fornisce a ogni applicazione registrata un' [entità servizio](/azure/active-directory/develop/developer-glossary#service-principal-object). L'entità servizio funge da ID applicazione, che viene usato durante la configurazione dell'autorizzazione per l'accesso ad altre risorse di Azure tramite RBAC.

Key Vault è un'applicazione Microsoft già registrata in tutti i tenant di Azure AD. Key Vault viene registrato con lo stesso ID applicazione in ogni cloud di Azure.

| Tenant | Cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altre  | Qualsiasi | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>prerequisiti

Per completare questa guida, è innanzitutto necessario eseguire le operazioni seguenti:

- [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- [Creare un insieme di credenziali delle chiavi](quick-create-cli.md)
- [Creare un account di Archiviazione di Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-cli). Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. La lunghezza del nome deve essere compresa tra 3 e 24 caratteri.
      
## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazione

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione dell'interfaccia della riga di comando di Azure usando i comandi [AZ login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) .

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Concedere a Key Vault l'accesso all'account di archiviazione

Usare l'interfaccia della riga di comando di Azure [AZ Role Assignment create](/cli/azure/role/assignment?view=azure-cli-latest) per concedere a Key Vault l'accesso all'account di archiviazione. Fornire al comando i valori di parametro seguenti:

- `--role`: passare il ruolo "ruolo del servizio operatore chiave dell'account di archiviazione". Questo ruolo limita l'ambito di accesso all'account di archiviazione. Per un account di archiviazione classico, passare invece "ruolo del servizio operatore chiave account di archiviazione classico".
- `--assignee-object-id`: passare il valore "93c27d83-f79b-4CB2-8DD4-4aa716542e74", che è l'ID oggetto per Key Vault nel cloud pubblico di Azure. Per ottenere l'ID oggetto per Key Vault nel cloud di Azure per enti pubblici, vedere [ID applicazione dell'entità servizio](#service-principal-application-id).
- `--scope`: passare l'ID risorsa dell'account di archiviazione, che è nel formato `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Per trovare l'ID sottoscrizione, usare il comando [AZ account list](/cli/azure/account?view=azure-cli-latest#az-account-list) per l'interfaccia della riga di comando di Azure. per trovare il nome dell'account di archiviazione e il gruppo di risorse dell'account di archiviazione, usare l'interfaccia della riga di comando di Azure [AZ storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee-object-id 93c27d83-f79b-4cb2-8dd4-4aa716542e74 --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

### <a name="create-a-key-vault-managed-storage-account"></a>Creare un account di archiviazione gestito Key Vault

 Creare un account di archiviazione gestito Key Vault usando l'interfaccia della riga di comando di Azure AZ le credenziali di [archiviazione](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) . Impostare un periodo di rigenerazione di 90 giorni. Dopo 90 giorni, Key Vault rigenera `key1` e scambia la chiave attiva da `key2` a `key1`. `key1` viene quindi contrassegnato come chiave attiva. Fornire al comando i valori di parametro seguenti:

- `--vault-name`: passare il nome dell'insieme di credenziali delle chiavi. Per trovare il nome dell'insieme di credenziali delle chiavi, usare il comando [AZ Key Vault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) dell'interfaccia della riga di comando di Azure.
- `-n`: passare il nome dell'account di archiviazione. Per trovare il nome dell'account di archiviazione, usare il comando [AZ storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) dell'interfaccia della riga di comando di Azure.
- `--resource-id`: passare l'ID risorsa dell'account di archiviazione, che è nel formato `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Per trovare l'ID sottoscrizione, usare il comando [AZ account list](/cli/azure/account?view=azure-cli-latest#az-account-list) per l'interfaccia della riga di comando di Azure. per trovare il nome dell'account di archiviazione e il gruppo di risorse dell'account di archiviazione, usare l'interfaccia della riga di comando di Azure [AZ storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) .
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Token di firma di accesso condiviso

È anche possibile richiedere Key Vault per generare token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione senza condividere le chiavi dell'account. Una firma di accesso condiviso offre un modo sicuro per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione completano le azioni seguenti:

- Impostare una definizione della firma di accesso condiviso dell'account `<YourSASDefinitionName>`. La definizione viene impostata in un Key Vault account di archiviazione gestito `<YourStorageAccountName>` nell'insieme di credenziali delle chiavi `<YourKeyVaultName>`.
- Creare un token di firma di accesso condiviso dell'account per i servizi BLOB, file, tabelle e di Accodamento. Il token viene creato per i tipi di risorse Service, container e Object. Il token viene creato con tutte le autorizzazioni, su HTTPS e con le date di inizio e di fine specificate.
- Impostare un Key Vault definizione di firma di accesso condiviso di archiviazione gestita nell'insieme di credenziali. La definizione ha l'URI del modello del token della firma di accesso condiviso che è stato creato. La definizione ha il tipo di firma di accesso condiviso `account` ed è valida per N giorni.
- Verificare che la firma di accesso condiviso sia stata salvata nell'insieme di credenziali delle chiavi come segreto.

### <a name="create-a-shared-access-signature-token"></a>Creazione di un token di firma di accesso condiviso

Creare una definizione di firma di accesso condiviso usando l'interfaccia della riga di comando di Azure [AZ storage account generate-SAS](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) . Questa operazione richiede le autorizzazioni `storage` e `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Quando l'operazione viene eseguita correttamente, copiare l'output.

```console
"se=2020-01-01&sp=***"
```

Questo output sarà il passato al parametro `--template-id` nel passaggio successivo.

### <a name="generate-a-shared-access-signature-definition"></a>Generare una definizione di firma di accesso condiviso

Usare l'interfaccia della riga di comando di Azure AZ le credenziali di [archiviazione SAS-Definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) , passando l'output del passaggio precedente al parametro `--template-id`, per creare una definizione di firma di accesso condiviso.  È possibile specificare il nome desiderato per il parametro `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificare la definizione della firma di accesso condiviso

È possibile verificare che la definizione della firma di accesso condiviso sia stata archiviata nell'insieme di credenziali delle chiavi usando l'interfaccia della riga di comando di Azure [AZ Key Vault Secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) e [AZ Key Vault Secret Show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) .

Per prima cosa, trovare la definizione della firma di accesso condiviso nell'insieme di credenziali delle chiavi usando il comando [AZ Key Vault Secret list](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-list) .

```azurecli-interactive
az keyvault secret list --vault-name <YourKeyVaultName>
```

Il segreto che corrisponde alla definizione SAS avrà queste proprietà:

```console
    "contentType": "application/vnd.ms-sastoken-storage",
    "id": "https://<YourKeyVaultName>.vault.azure.net/secrets/<YourStorageAccountName>-<YourSASDefinitionName>",
```

A questo punto è possibile usare il comando [AZ chiave Vault Secret Show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) e la proprietà `id` per visualizzare il contenuto del segreto.

```azurecli-interactive
az keyvault secret show --vault-name <YourKeyVaultName> --id <SasDefinitionID>
```

L'output di questo comando visualizzerà la stringa di definizione SAS come`value`.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [chiavi, segreti e certificati](https://docs.microsoft.com/rest/api/keyvault/).
- Esaminare gli articoli nel [Blog del team di Azure Key Vault](https://blogs.technet.microsoft.com/kv/).
- Vedere la documentazione di riferimento per l' [archiviazione AZ](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest) .
