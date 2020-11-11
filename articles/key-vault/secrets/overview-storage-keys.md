---
title: Gestire le chiavi degli account di archiviazione con Azure Key Vault e l'interfaccia della riga di comando di Azure
description: Le chiavi dell'account di archiviazione forniscono un'integrazione diretta tra Azure Key Vault e l'accesso basato sulla chiave di un account di archiviazione di Azure.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e8ae0463633940ba3192815af6a07a6356901044
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426987"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Gestire le chiavi degli account di archiviazione con Key Vault e l'interfaccia della riga di comando di Azure

Un account di archiviazione Azure usa credenziali costituite da un nome account e una chiave. La chiave viene generata automaticamente e viene usata come password, invece che come chiave crittografica. Key Vault gestisce le chiavi degli account di archiviazione rigenerandole periodicamente nell'account di archiviazione e fornisce token di firma di accesso condiviso per l'accesso delegato alle risorse nell'account di archiviazione.

È possibile usare la funzionalità di chiave dell'account di archiviazione gestita di Key Vault per elencare (sincronizzare) le chiavi con un account di archiviazione di Azure e rigenerare (ruotare) le chiavi periodicamente. È possibile gestire le chiavi per gli account di archiviazione e gli account di archiviazione classici.

Quando si usa la funzionalità di chiave dell'account di archiviazione gestita, è necessario prendere in considerazione gli aspetti seguenti:

- I valori di chiave non vengono mai restituiti in risposta a un chiamante.
- È necessario che solo Key Vault gestisca le chiavi dell'account di archiviazione. Non è possibile gestire autonomamente le chiavi e occorre evitare di interferire con i processi di Key Vault.
- È necessario che solo un singolo oggetto di Key Vault gestisca le chiavi dell'account di archiviazione. È necessario non consentire la gestione delle chiavi da più oggetti.
- È necessario rigenerare le chiavi solo tramite Key Vault. Non rigenerare manualmente le chiavi dell'account di archiviazione.

È consigliabile usare l'integrazione di Archiviazione di Azure con Azure Active Directory (Azure AD), il servizio Microsoft basato sul cloud per la gestione delle identità e dell'accesso. L'integrazione con Azure AD è disponibile per [BLOB e code di Azure](../../storage/common/storage-auth-aad.md) e offre l'accesso basato su token OAuth2 ad Archiviazione di Azure, analogamente ad Azure Key Vault.

Azure AD consente di autenticare l'applicazione client con un'identità di applicazione o utente, anziché con le credenziali dell'account di archiviazione. È possibile usare un'[identità gestita di Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) per l'esecuzione in Azure. Le identità gestite eliminano la necessità di eseguire l'autenticazione dei client e di archiviare le credenziali nell'applicazione.

Azure AD usa il controllo degli accessi in base al ruolo per gestire l'autorizzazione e questo approccio è supportato anche da Key Vault.

## <a name="service-principal-application-id"></a>ID applicazione dell'entità servizio

Un tenant di Azure AD assegna a ogni applicazione registrata un'[entità servizio](../../active-directory/develop/developer-glossary.md#service-principal-object). L'entità servizio fornisce l'ID applicazione, che viene usato durante la configurazione dell'autorizzazione per l'accesso ad altre risorse di Azure tramite il controllo degli accessi in base al ruolo.

Key Vault è un'applicazione Microsoft già registrata in tutti i tenant di Azure AD. Key Vault è registrato con lo stesso ID applicazione in ogni cloud di Azure.

| Tenant | Cloud | ID applicazione |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Pubblico di Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Altro  | Qualsiasi | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida, è necessario eseguire prima di tutto queste operazioni:

- [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)
- [Creare un insieme di credenziali delle chiavi](quick-create-cli.md)
- [Creare un account di Archiviazione di Azure](../../storage/common/storage-account-create.md?tabs=azure-cli). Il nome dell'account di archiviazione deve usare solo lettere minuscole e numeri. Il nome deve avere una lunghezza compresa tra 3 e 24 caratteri.
      
## <a name="manage-storage-account-keys"></a>Gestire le chiavi dell'account di archiviazione

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Autenticare la sessione dell'interfaccia della riga di comando di Azure usando i comandi [az login](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0).

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Concedere a Key Vault l'accesso all'account di archiviazione

Usare il comando [az role assignment create](/cli/azure/role/assignment?view=azure-cli-latest) dell'interfaccia della riga di comando di Azure per concedere a Key Vault l'accesso all'account di archiviazione. Fornire al comando i valori dei parametri seguenti:

- `--role`: passare il ruolo "Ruolo del servizio dell'operatore della chiave dell'account di archiviazione" di Azure. Questo ruolo limita l'ambito dell'accesso all'account di archiviazione. Per un account di archiviazione classico passare invece il "Ruolo del servizio dell'operatore della chiave dell'account di archiviazione classico".
- `--assignee`: passare il valore "https://vault.azure.net", ovvero l'URL per Key Vault nel cloud pubblico di Azure. Per il cloud di Azure per enti pubblici usare invece '--asingee-object-id'. Vedere [ID applicazione dell'entità servizio](#service-principal-application-id).
- `--scope`: passare l'ID della risorsa dell'account di archiviazione, con formato `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Per trovare l'ID sottoscrizione, usare il comando [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) dell'interfaccia della riga di comando di Azure. Per trovare il nome e il gruppo di risorse dell'account di archiviazione, usare il comando [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) dell'interfaccia della riga di comando di Azure.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Concedere all'account utente l'autorizzazione per gli account di archiviazione gestiti

Usare il cmdlet [az keyvault-set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) dell'interfaccia della riga di comando di Azure per aggiornare i criteri di accesso di Key Vault e concedere le autorizzazioni dell'account di archiviazione all'account utente.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Tenere presente che le autorizzazioni per gli account di archiviazione non sono disponibili nella pagina "Criteri di accesso" dell'account di archiviazione nel portale di Azure.
### <a name="create-a-key-vault-managed-storage-account"></a>Creare un account di archiviazione gestito da Key Vault

 Creare un account di archiviazione gestito da Key Vault usando il comando [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest#az-keyvault-storage-add) dell'interfaccia della riga di comando di Azure. Impostare un periodo di rigenerazione di 90 giorni. Quando è il momento di eseguire la rotazione, Key Vault rigenera la chiave che non è attiva e quindi imposta la chiave appena creata come attiva. Solo una delle chiavi viene usata per rilasciare token di firma di accesso condiviso in un momento specifico, ovvero la chiave attiva. Fornire al comando i valori dei parametri seguenti:

- `--vault-name`: passare il nome dell'insieme di credenziali delle chiavi. Per trovare il nome dell'insieme di credenziali delle chiavi, usare il comando [az keyvault list](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-list) dell'interfaccia della riga di comando di Azure.
- `-n`: passare il nome dell'account di archiviazione. Per trovare il nome dell'account di archiviazione, usare il comando [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) dell'interfaccia della riga di comando di Azure.
- `--resource-id`: passare l'ID della risorsa dell'account di archiviazione, con formato `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>`. Per trovare l'ID sottoscrizione, usare il comando [az account list](/cli/azure/account?view=azure-cli-latest#az-account-list) dell'interfaccia della riga di comando di Azure. Per trovare il nome e il gruppo di risorse dell'account di archiviazione, usare il comando [az storage account list](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-list) dell'interfaccia della riga di comando di Azure.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Token di firma di accesso condiviso

È anche possibile chiedere a Key Vault di generare i token di firma di accesso condiviso. Una firma di accesso condiviso fornisce accesso delegato controllato alle risorse dell'account di archiviazione. È possibile concedere ai client l'accesso alle risorse nell'account di archiviazione, senza condividere le chiavi dell'account. Una firma di accesso condiviso offre una modalità sicura per condividere le risorse di archiviazione senza compromettere le chiavi dell'account.

I comandi in questa sezione consentono di completare le azioni seguenti:

- Configurare una definizione di firma di accesso condiviso dell'account.`<YourSASDefinitionName>` La definizione viene configurata nell'account di archiviazione gestito da Key Vault `<YourStorageAccountName>` nell'insieme di credenziali delle chiavi `<YourKeyVaultName>`.
- Creare un token di firma di accesso condiviso per i servizi BLOB, file, tabella e coda. Il token viene creato per le risorse di tipo servizio, contenitore e oggetto. Il token viene creato con tutte le autorizzazioni su https e con le date di inizio e di fine specificate.
- Configurare una definizione di firma di accesso condiviso della risorsa di archiviazione gestita di Key Vault nell'insieme di credenziali. La definizione include l'URI del modello del token di firma di accesso condiviso creato. La definizione ha una firma di accesso condiviso di tipo `account` ed è valida per N giorni.
- Verificare che la firma di accesso condiviso sia stata salvata nell'insieme di credenziali delle chiavi come segreto.

### <a name="create-a-shared-access-signature-token"></a>Creare un token di firma di accesso condiviso

Creare una definizione di firma di accesso condiviso usando il comando [az storage account generate-sas](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-generate-sas) dell'interfaccia della riga di comando di Azure. Questa operazione richiede le autorizzazioni `storage` e `setsas`.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
Al termine dell'esecuzione corretta dell'operazione, copiare l'output.

```console
"se=2020-01-01&sp=***"
```

L'output verrà passato al parametro `--template-uri` nel passaggio successivo.

### <a name="generate-a-shared-access-signature-definition"></a>Generare una definizione di firma di accesso condiviso

Usare il comando [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?view=azure-cli-latest#az-keyvault-storage-sas-definition-create) dell'interfaccia della riga di comando di Azure passando l'output dal passaggio precedente al parametro `--template-uri` per creare la definizione di firma di accesso condiviso.  È possibile specificare il nome che si preferisce per il parametro `-n`.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>Verificare la definizione di firma di accesso condiviso

È possibile verificare che la definizione di firma di accesso condiviso sia stata archiviata nell'insieme di credenziali delle chiavi usando il comando [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) dell'interfaccia della riga di comando di Azure.

È ora possibile usare il comando [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) e la proprietà `id` per visualizzare il contenuto di tale segreto.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni su [chiavi, segreti e certificati](/rest/api/keyvault/).
- Vedere gli articoli nel [blog del team di Azure Key Vault](/archive/blogs/kv/).
- Vedere la documentazione di riferimento di [az keyvault storage](/cli/azure/keyvault/storage?view=azure-cli-latest).
