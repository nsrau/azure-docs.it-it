---
title: Creare un account che supporti le chiavi gestite dal cliente per tabelle e codeCreate an account that supports customer-managed keys for tables and queues
titleSuffix: Azure Storage
description: Informazioni su come creare un account di archiviazione che supporti la configurazione di chiavi gestite dal cliente per tabelle e code. Usare l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account per la crittografia di Archiviazione di Azure.Use the Azure CLI or an Azure Resource Manager template to create a storage account that relies on the account encryption key for Azure Storage encryption. È quindi possibile configurare le chiavi gestite dal cliente per l'account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/05/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 09558a8d1e4e2dc68cefd2c870f54e008d10b97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083565"
---
# <a name="create-an-account-that-supports-customer-managed-keys-for-tables-and-queues"></a>Creare un account che supporti le chiavi gestite dal cliente per tabelle e codeCreate an account that supports customer-managed keys for tables and queues

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivi. By default, Queue storage and Table storage use a key that is scoped to the service and managed by Microsoft. È inoltre possibile scegliere di utilizzare chiavi gestite dal cliente per crittografare i dati della coda o della tabella. Per usare le chiavi gestite dal cliente con code e tabelle, è innanzitutto necessario creare un account di archiviazione che usa una chiave di crittografia con ambito per l'account, anziché per il servizio. Dopo aver creato un account che usa la chiave di crittografia dell'account per i dati di code e tabelle, è possibile configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per tale account di archiviazione.

Questo articolo descrive come creare un account di archiviazione che si basa su una chiave con ambito per l'account. Quando l'account viene creato per la prima volta, Microsoft utilizza la chiave dell'account per crittografare i dati nell'account e Microsoft gestisce la chiave. Successivamente è possibile configurare le chiavi gestite dal cliente per l'account per sfruttare tali vantaggi, tra cui la possibilità di fornire chiavi personalizzate, aggiornare la versione della chiave, ruotare le chiavi e revocare i controlli di accesso.

## <a name="about-the-feature"></a>Informazioni sulla funzionalità

To create a storage account that relies on the account encryption key for Queue and Table storage, you must first register to use this feature with Azure. A causa della capacità limitata, tenere presente che potrebbero essere necessari diversi mesi prima che le richieste di accesso vengano approvate.

È possibile creare un account di archiviazione che si basa sulla chiave di crittografia dell'account per l'archiviazione di code e tabelle nelle aree seguenti:You can create a storage account that relies on the account encryption key for Queue and Table storage in the following regions:

- Stati Uniti orientali
- Stati Uniti centro-meridionali
- Stati Uniti occidentali 2  

### <a name="register-to-use-the-account-encryption-key"></a>Registrati per utilizzare la chiave di crittografia dell'account

To register to use the account encryption key with Queue or Table storage, use PowerShell or Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature.To](/powershell/module/az.resources/get-azproviderfeature) register with PowerShell, call the Get-AzProviderFeature command.

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [az feature register.](/cli/azure/feature#az-feature-register)

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature register --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

### <a name="check-the-status-of-your-registration"></a>Controlla lo stato della tua registrazione

Per controllare lo stato della registrazione per l'archiviazione della coda o della tabella, usare PowerShell o l'interfaccia della riga di comando di Azure.To check the status of your registration for Queue or Table storage, use PowerShell or Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per controllare lo stato della registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature.](/powershell/module/az.resources/get-azproviderfeature)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForQueues
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowAccountEncryptionKeyForTables
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per controllare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [az feature.](/cli/azure/feature#az-feature-show)

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForQueues
az feature show --namespace Microsoft.Storage \
    --name AllowAccountEncryptionKeyForTables
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrare nuovamente il provider di risorse di Archiviazione di AzureRe-register the Azure Storage resource provider

Dopo l'approvazione della registrazione, è necessario registrare nuovamente il provider di risorse di Archiviazione di Azure.After your registration is approved, you must re-register the Azure Storage resource provider. Usare PowerShell o l'interfaccia della riga di comando di Azure per registrare nuovamente il provider di risorse.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per registrare nuovamente il provider di risorse con PowerShell, chiamare il comando [Register-AzResourceProvider.To](/powershell/module/az.resources/register-azresourceprovider) re-register the resource provider with PowerShell, call the Register-AzResourceProvider command.

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per registrare nuovamente il provider di risorse con l'interfaccia della riga di comando di Azure, chiamare il comando [az provider register.](/cli/azure/provider#az-provider-register)

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="create-an-account-that-uses-the-account-encryption-key"></a>Creare un account che utilizza la chiave di crittografia dell'accountCreate an account that uses the account encryption key

È necessario configurare un nuovo account di archiviazione per usare la chiave di crittografia dell'account per le code e le tabelle al momento della creazione dell'account di archiviazione. L'ambito della chiave di crittografia non può essere modificato dopo la creazione dell'account.

L'account di archiviazione deve essere di tipo general purpose v2. È possibile creare l'account di archiviazione e configurarlo in base alla chiave di crittografia dell'account usando l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager.You can create the storage account and configure it to rely on the account encryption key by using either Azure CLI or an Azure Resource Manager template.

> [!NOTE]
> Solo l'archiviazione di code e tabelle può essere configurato facoltativamente per crittografare i dati con la chiave di crittografia dell'account quando viene creato l'account di archiviazione. L'archiviazione BLOB e i file di Azure usano sempre la chiave di crittografia dell'account per crittografare i dati.

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per usare PowerShell per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account, assicurarsi di aver installato il modulo di Azure PowerShell, versione 3.4.0 o successiva. Per altre informazioni, vedere [Installare il modulo di Azure PowerShell.For](/powershell/azure/install-az-ps)more information, see Install the Azure PowerShell module .

Successivamente, creare un account di archiviazione v2 generico chiamando il comando [New-AzStorageAccount,](/powershell/module/az.storage/new-azstorageaccount) con i parametri appropriati:Next, create a general-purpose v2 storage account by calling the New-AzStorageAccount command, with the appropriate parameters:

- Includere `-EncryptionKeyTypeForQueue` l'opzione e `Account` impostarne il valore su di utilizzare la chiave di crittografia dell'account per crittografare i dati nell'archivio code.
- Includere `-EncryptionKeyTypeForTable` l'opzione e `Account` impostarne il valore su di utilizzare la chiave di crittografia dell'account per crittografare i dati nell'archiviazione tabelle.

Nell'esempio seguente viene illustrato come creare un account di archiviazione generico v2 configurato per l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione di code che per quella tabelle. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -EncryptionKeyTypeForTable Account `
    -EncryptionKeyTypeForQueue Account
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per usare l'interfaccia della riga di comando di Azure per creare un account di archiviazione che si basa sulla chiave di crittografia dell'account, assicurarsi di aver installato l'interfaccia della riga di comando di Azure versione 2.0.80 o successiva. Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Successivamente, creare un account di archiviazione v2 generico chiamando il comando [az storage account create,](/cli/azure/storage/account#az-storage-account-create) con i parametri appropriati:Next, create a general-purpose v2 storage account by calling the az storage account create command, with the appropriate parameters:

- Includere `--encryption-key-type-for-queue` l'opzione e `Account` impostarne il valore su di utilizzare la chiave di crittografia dell'account per crittografare i dati nell'archivio code.
- Includere `--encryption-key-type-for-table` l'opzione e `Account` impostarne il valore su di utilizzare la chiave di crittografia dell'account per crittografare i dati nell'archiviazione tabelle.

Nell'esempio seguente viene illustrato come creare un account di archiviazione generico v2 configurato per l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione di code che per quella tabelle. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --encryption-key-type-for-table Account \
    --encryption-key-type-for-queue Account
```

# <a name="template"></a>[Modello](#tab/template)

L'esempio JSON seguente crea un account di archiviazione v2 generico configurato per l'archiviazione con ridondanza geografica di accesso in lettura (RA-GRS) e che usa la chiave di crittografia dell'account per crittografare i dati sia per l'archiviazione della coda che per quella delle tabelle. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "services": {
                    "queue": {
                        "keyType": "Account"
                    },
                    "table": {
                        "keyType": "Account"
                    }
                },
                "keySource": "Microsoft.Storage"
            }
        }
    }
],
```

---

Dopo aver creato un account che si basa sulla chiave di crittografia dell'account, vedere uno degli articoli seguenti per configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure:After you have created an account that relies on the account encryption key, see one of the following articles to configure customer-managed keys with Azure Key Vault:

- [Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure tramite il portale di AzureConfigure customer-managed keys with Azure Key Vault by using the Azure portal](storage-encryption-keys-portal.md)
- [Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure tramite PowerShellConfigure customer-managed keys with Azure Key Vault by using PowerShell](storage-encryption-keys-powershell.md)
- [Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure usando l'interfaccia della riga di comando di AzureConfigure customer-managed keys with Azure Key Vault by using Azure CLI](storage-encryption-keys-cli.md)

## <a name="verify-the-account-encryption-key"></a>Verificare la chiave di crittografia dell'account

Per verificare che un servizio in un account di archiviazione usi la chiave di crittografia dell'account, chiamare il comando Dell'account di archiviazione dell'interfaccia della riga di comando di [Azure.To](/cli/azure/storage/account#az-storage-account-show) verify that a service in a storage account is using the account encryption key, call the Azure CLI az storage account command. Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `keyType` campo per ogni servizio all'interno della `Account`proprietà di crittografia e verificare che sia impostato su .

# <a name="powershell"></a>[Powershell](#tab/powershell)

Per verificare che un servizio in un account di archiviazione usi la chiave di crittografia dell'account, chiamare il comando [Get-AzStorageAccount.To](/powershell/module/az.storage/get-azstorageaccount) verify that a service in a storage account is using the account encryption key, call the Get-AzStorageAccount command. Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `KeyType` campo per ogni `Encryption` servizio all'interno della `Account`proprietà e verificare che sia impostato su .

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.Services.Queue
$account.Encryption.Services.Table
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Per verificare che un servizio in un account di archiviazione usi la chiave di crittografia dell'account, chiamare il comando [az storage account.](/cli/azure/storage/account#az-storage-account-show) Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `keyType` campo per ogni servizio all'interno della `Account`proprietà di crittografia e verificare che sia impostato su .

```azurecli
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](storage-service-encryption.md) 
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
