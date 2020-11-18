---
title: Configurare la crittografia con chiavi gestite dal cliente archiviate nel modulo di protezione hardware Azure Key Vault gestito (anteprima)
titleSuffix: Azure Storage
description: Informazioni su come configurare la crittografia di archiviazione di Azure con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima) usando l'interfaccia della riga di comando di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/21/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2f57e801720c6b546a58b216422629d192e8d2e6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94843316"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Configurare la crittografia con chiavi gestite dal cliente archiviate nel modulo di protezione hardware Azure Key Vault gestito (anteprima)

Archiviazione di Azure crittografa tutti i dati in un account di archiviazione inattivo. Per impostazione predefinita, i dati vengono crittografati con chiavi gestite da Microsoft. Per un maggiore controllo sulle chiavi di crittografia, è possibile gestire le proprie chiavi. Le chiavi gestite dal cliente devono essere archiviate in Azure Key Vault o Key Vault modello di protezione hardware (HSM) gestito (anteprima). Un modulo di protezione hardware gestito da Azure Key Vault è un modulo di protezione hardware FIPS 140-2 di livello 3.

Questo articolo illustra come configurare la crittografia con chiavi gestite dal cliente archiviate in un modulo di protezione hardware gestito usando l'interfaccia della riga di comando di Azure. Per informazioni su come configurare la crittografia con chiavi gestite dal cliente archiviate in un insieme di credenziali delle chiavi, vedere [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!NOTE]
> Azure Key Vault e Azure Key Vault HSM gestito supportano le stesse API e le stesse interfacce di gestione per la configurazione.

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità all'account di archiviazione

Prima di tutto, assegnare all'account di archiviazione un'identità gestita assegnata dal sistema. Questa identità gestita verrà usata per concedere le autorizzazioni dell'account di archiviazione per accedere al modulo di protezione hardware gestito. Per altre informazioni sulle identità gestite assegnate dal sistema, vedere informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Per assegnare un'identità gestita usando l'interfaccia della riga di comando di Azure, chiamare [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update). Ricordarsi di sostituire i valori segnaposto tra parentesi con valori personalizzati:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Assegnare un ruolo all'account di archiviazione per l'accesso al modulo di protezione hardware gestito

Assegnare quindi il ruolo di crittografia del servizio di crittografia **HSM gestito** all'identità gestita dell'account di archiviazione in modo che l'account di archiviazione disponga delle autorizzazioni per il modulo di protezione hardware gestito. Microsoft consiglia di definire l'ambito dell'assegnazione di ruolo al livello della singola chiave per concedere il minor numero possibile di privilegi all'identità gestita.

Per creare l'assegnazione di ruolo per l'account di archiviazione, chiamare [AZ Key Vault Role Assignment create](/cli/azure/role/assignment#az_role_assignment_create). Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Configurare la crittografia con una chiave nel modulo di protezione hardware gestito

Infine, configurare la crittografia di archiviazione di Azure con chiavi gestite dal cliente per usare una chiave archiviata nel modulo di protezione hardware gestito. I tipi di chiave supportati includono chiavi RSA-HSM di dimensioni 2048, 3072 e 4096. Installare l'interfaccia della riga di comando di Azure 2.12.0 o versione successiva per configurare la crittografia per l'uso di una chiave gestita dal cliente in un modulo HSM gestito Per altre informazioni, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Per aggiornare automaticamente la versione della chiave per una chiave gestita dal cliente, omettere la versione della chiave quando si configura la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Chiamare [AZ storage account Update](/cli/azure/storage/account#az_storage_account_update) per aggiornare le impostazioni di crittografia dell'account di archiviazione, come illustrato nell'esempio seguente. Includere `--encryption-key-source parameter` e impostarlo su `Microsoft.Keyvault` per abilitare le chiavi gestite dal cliente per l'account. Ricordarsi di sostituire i valori segnaposto tra parentesi quadre con valori personalizzati.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Per aggiornare manualmente la versione per una chiave gestita dal cliente, includere la versione della chiave quando si configura la crittografia per l'account di archiviazione:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Quando si aggiorna manualmente la versione della chiave, è necessario aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione. Per prima cosa, eseguire una query per l'URI dell'insieme di credenziali delle chiavi chiamando [AZ Key Vault Show](/cli/azure/keyvault#az-keyvault-show)e per la versione della chiave chiamando [AZ Key Vault Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Chiamare quindi il comando [AZ storage account Update](/cli/azure/storage/account#az-storage-account-update) per aggiornare le impostazioni di crittografia dell'account di archiviazione in modo da usare la nuova versione della chiave, come illustrato nell'esempio precedente.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Chiavi gestite dal cliente per la crittografia di archiviazione di Azure](customer-managed-keys-overview.md)
