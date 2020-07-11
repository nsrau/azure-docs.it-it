---
title: Creare un account di archiviazione con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati
titleSuffix: Azure Storage
description: I clienti che necessitano di livelli più elevati di garanzia che i dati sono sicuri possono anche abilitare la crittografia AES a 256 bit a livello di infrastruttura di archiviazione di Azure. Quando è abilitata la crittografia dell'infrastruttura, i dati in un account di archiviazione vengono crittografati due volte con due algoritmi di crittografia diversi e due chiavi diverse.
services: storage
author: tamram
ms.service: storage
ms.date: 07/08/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: references_regions
ms.openlocfilehash: edeb184af1c1260a456ed3de7064805526629de8
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86225270"
---
# <a name="create-a-storage-account-with-infrastructure-encryption-enabled-for-double-encryption-of-data"></a>Creare un account di archiviazione con la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati

Archiviazione di Azure crittografa automaticamente tutti i dati in un account di archiviazione a livello di servizio usando la crittografia AES a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. I clienti che necessitano di livelli più elevati di garanzia che i dati sono sicuri possono anche abilitare la crittografia AES a 256 bit a livello di infrastruttura di archiviazione di Azure. Quando è abilitata la crittografia dell'infrastruttura, i dati in un account di archiviazione vengono crittografati due volte &mdash; a livello di servizio e una volta a livello di infrastruttura &mdash; con due diversi algoritmi di crittografia e due chiavi diverse. La doppia crittografia dei dati di archiviazione di Azure protegge da uno scenario in cui uno degli algoritmi o delle chiavi di crittografia potrebbe essere compromesso. In questo scenario, il livello di crittografia aggiuntivo continua a proteggere i dati.

La crittografia a livello di servizio supporta l'utilizzo di chiavi gestite da Microsoft o chiavi gestite dal cliente con Azure Key Vault. La crittografia a livello di infrastruttura si basa su chiavi gestite da Microsoft e usa sempre una chiave separata. Per altre informazioni sulla gestione delle chiavi con la crittografia di archiviazione di Azure, vedere [informazioni sulla gestione delle chiavi di crittografia](storage-service-encryption.md#about-encryption-key-management).

Per crittografare i dati in modo doppiato, è prima necessario creare un account di archiviazione configurato per la crittografia dell'infrastruttura. Questo articolo descrive come creare un account di archiviazione che Abilita la crittografia dell'infrastruttura.

## <a name="about-the-feature"></a>Informazioni sulla funzionalità

Per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, è necessario prima registrarsi per usare questa funzionalità con Azure. A causa della capacità limitata, tenere presente che l'approvazione delle richieste di accesso potrebbe richiedere alcuni mesi.

È possibile creare un account di archiviazione con la crittografia dell'infrastruttura abilitata nelle aree seguenti:

- Stati Uniti orientali
- Stati Uniti centro-meridionali
- Stati Uniti occidentali 2

### <a name="register-to-use-infrastructure-encryption"></a>Eseguire la registrazione per usare la crittografia dell'infrastruttura

Per eseguire la registrazione per usare la crittografia dell'infrastruttura con archiviazione di Azure, usare PowerShell o l'interfaccia della riga di comando

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per eseguire la registrazione con PowerShell, chiamare il comando [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) .

```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per eseguire la registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature Register](/cli/azure/feature#az-feature-register) .

```azurecli
az feature register --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Modello](#tab/template)

N/A

---

### <a name="check-the-status-of-your-registration"></a>Verificare lo stato della registrazione

Per verificare lo stato della registrazione per la crittografia dell'infrastruttura, usare PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare lo stato della registrazione con PowerShell, chiamare il comando [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) .

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowRequireInfraStructureEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare lo stato della registrazione con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ feature](/cli/azure/feature#az-feature-show) .

```azurecli
az feature show --namespace Microsoft.Storage \
    --name AllowRequireInfraStructureEncryption
```

# <a name="template"></a>[Modello](#tab/template)

N/A

---

### <a name="re-register-the-azure-storage-resource-provider"></a>Registrare di nuovo il provider di risorse di archiviazione di Azure

Dopo che la registrazione è stata approvata, è necessario registrare di nuovo il provider di risorse di archiviazione di Azure. Usare PowerShell o l'interfaccia della riga di comando di Azure per registrare nuovamente il provider di risorse.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per registrare di nuovo il provider di risorse con PowerShell, chiamare il comando [Register-AzResourceProvider](/powershell/module/az.resources/register-azresourceprovider) .

```powershell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Storage'
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per registrare di nuovo il provider di risorse con l'interfaccia della riga di comando di Azure, chiamare il comando [AZ provider Register](/cli/azure/provider#az-provider-register) .

```azurecli
az provider register --namespace 'Microsoft.Storage'
```

# <a name="template"></a>[Modello](#tab/template)

N/A

---

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Creare un account con la crittografia dell'infrastruttura abilitata

È necessario configurare un account di archiviazione per usare la crittografia dell'infrastruttura al momento della creazione dell'account. Non è possibile abilitare o disabilitare la crittografia dell'infrastruttura dopo la creazione dell'account.

L'account di archiviazione deve essere di tipo generico V2. È possibile creare l'account di archiviazione e configurarlo per abilitare la crittografia dell'infrastruttura usando PowerShell, l'interfaccia della riga di comando di Azure o un modello di Azure Resource Manager.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per usare PowerShell per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, verificare di aver installato il [modulo AZ. storage PowerShell](https://www.powershellgallery.com/packages/Az.Storage), versione 2.2.0 o successiva. Per altre informazioni, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

Successivamente, creare un account di archiviazione per utilizzo generico V2 chiamando il comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) . Includere l' `-RequireInfrastructureEncryption` opzione per abilitare la crittografia dell'infrastruttura.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico V2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per usare l'interfaccia della riga di comando di Azure per creare un account di archiviazione con la crittografia dell'infrastruttura abilitata, verificare di avere installato l'interfaccia della riga di comando di Azure versione 2.8.0 Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Successivamente, creare un account di archiviazione per utilizzo generico V2 chiamando il comando [AZ storage account create](/cli/azure/storage/account#az-storage-account-create) e includere `--require-infrastructure-encryption option` per abilitare la crittografia dell'infrastruttura.

L'esempio seguente illustra come creare un account di archiviazione per utilizzo generico V2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) e la crittografia dell'infrastruttura abilitata per la doppia crittografia dei dati. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

# <a name="template"></a>[Modello](#tab/template)

Nell'esempio JSON seguente viene creato un account di archiviazione per utilizzo generico V2 configurato per l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) ed è abilitata la crittografia dell'infrastruttura per la doppia crittografia dei dati. Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

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
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

## <a name="verify-that-infrastructure-encryption-is-enabled"></a>Verificare che la crittografia dell'infrastruttura sia abilitata

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Per verificare che la crittografia dell'infrastruttura sia abilitata per un account di archiviazione, chiamare il comando [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) . Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Recuperare il `RequireInfrastructureEncryption` campo all'interno della `Encryption` proprietà e verificare che sia impostato su `True` .

Nell'esempio seguente viene recuperato il valore della `RequireInfrastructureEncryption` Proprietà. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per verificare che la crittografia dell'infrastruttura sia abilitata per un account di archiviazione, chiamare il comando [AZ storage account Show](/cli/azure/storage/account#az-storage-account-show) . Questo comando restituisce un set di proprietà dell'account di archiviazione e i relativi valori. Cercare il `requireInfrastructureEncryption` campo all'interno della `encryption` proprietà e verificare che sia impostato su `true` .

Nell'esempio seguente viene recuperato il valore della `requireInfrastructureEncryption` Proprietà. Ricordarsi di sostituire i valori segnaposto tra parentesi angolari con valori personalizzati:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Modello](#tab/template)

N/D

---

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Usare chiavi gestite dal cliente con Azure Key Vault per gestire la crittografia di archiviazione di Azure](encryption-customer-managed-keys.md)