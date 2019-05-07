---
title: Configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure della riga di comando di Azure
description: Informazioni su come usare Azure CLI per configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure. Chiavi gestite dall'utente consentono di creare, ruotare, disabilitare e revocare i controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: ca2cfc9369fd6fb001b2a2dc401c33e5ddfd38c8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142916"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-azure-cli"></a>Configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure della riga di comando di Azure

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

Questo articolo illustra come configurare un insieme di credenziali delle chiavi con chiavi gestite dal cliente tramite la CLI di Azure.

## <a name="assign-an-identity-to-the-storage-account"></a>Assegnare un'identità per l'account di archiviazione

Per abilitare le chiavi gestite dal cliente per l'account di archiviazione, assegnare un'identità gestita assegnato dal sistema all'account di archiviazione. Si userà questa identità gestita per concedere le autorizzazioni di account di archiviazione per l'insieme di credenziali delle chiavi di accesso.

Per assegnare un'identità gestita tramite la CLI di Azure, chiamare [aggiornamento di account di archiviazione di az](/cli/azure/storage/account#az-storage-account-update). Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi con i propri valori.

```azurecli-interactive
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

Per altre informazioni sulla configurazione di identità gestite assegnato dal sistema con la CLI di Azure, vedere [Configura gestiti le identità per le risorse di Azure in una VM di Azure usando Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Creare un nuovo insieme di credenziali delle chiavi

L'insieme di credenziali chiave che viene usato per archiviare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure deve disporre di due impostazioni di protezione con chiave abilitate, **eliminazione temporanea** e **eliminazione**. Per creare un nuovo key vault usando PowerShell o CLI di Azure con queste impostazioni abilitate, eseguire i comandi seguenti. Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi con i propri valori. 

Per creare un nuovo insieme di credenziali chiave tramite la CLI di Azure, chiamare [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi con i propri valori.

```azurecli-interactive
az keyvault create \
    --name <key-vault> \
    --resource-group <resource_group> \
    --location <region> \
    --enable-soft-delete \
    --enable-purge-protection
```

## <a name="configure-the-key-vault-access-policy"></a>Configurare i criteri di accesso dell'insieme di credenziali chiave

Successivamente, configurare i criteri di accesso per l'insieme di credenziali delle chiavi in modo che l'account di archiviazione disponga delle autorizzazioni per accedervi. In questo passaggio si userà l'identità gestita precedentemente assegnato all'account di archiviazione.

Per impostare i criteri di accesso per l'insieme di credenziali delle chiavi, chiamare [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy). Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi con i propri valori.

```azurecli-interactive
storage_account_principal=$(az storage account show \
    -name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)
az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group>
    --object-id $storage_account_principal \
    --key-permissions get recover unwrapKey wrapKey
```

## <a name="create-a-new-key"></a>Creare una nuova chiave

Successivamente, creare una chiave nell'insieme di credenziali chiave. Per creare una chiave, chiamare [chiave di az keyvault create](/cli/azure/keyvault/key#az-keyvault-key-create). Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi con i propri valori.

```azurecli-interactive
az keyvault key create
    --name <key> \
    --vault-name <key-vault>
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Configurare la crittografia con chiavi gestite dal cliente

Per impostazione predefinita, la crittografia di archiviazione di Azure Usa chiavi gestite da Microsoft. Configurare l'account di archiviazione di Azure per le chiavi gestite dal cliente e specificare la chiave da associare all'account di archiviazione.

Per aggiornare le impostazioni di crittografia dell'account di archiviazione, chiamare [aggiornamento di account di archiviazione di az](/cli/azure/storage/account#az-storage-account-update). In questo esempio esegue inoltre query per l'URI dell'insieme di credenziali chiave e versione della chiave, entrambi i valori che sono necessari per associare la chiave dell'account di archiviazione. Ricordarsi di sostituire i valori segnaposto racchiusi tra parentesi con i propri valori.

```azurecli-interactive
key_vault_uri=$(az keyvault show \
    --name <key-vault> \
    --resource-group <resource_group> \
    --query properties.vaultUri \
    --output tsv)
key_version=$(az keyvault key list-versions \
    --name <key> \
    --vault-name <key-vault> \
    --query [].kid \
    --output tsv | cut -d '/' -f 6)
az storage account update 
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $key_vault_uri
```

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si crea una nuova versione di una chiave, è necessario aggiornare l'account di archiviazione per usare la nuova versione. In primo luogo, eseguire una query per l'insieme di credenziali delle chiavi URI chiamando [show di az keyvault](/cli/azure/keyvault#az-keyvault-show)e per la versione della chiave chiamando [az keyvault chiave elenco le versioni](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Quindi chiamare [aggiornamento di account di archiviazione di az](/cli/azure/storage/account#az-storage-account-update) per aggiornare le impostazioni di crittografia dell'account di archiviazione per usare la nuova versione della chiave, come illustrato nella sezione precedente.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia di archiviazione di Azure per dati inattivi](storage-service-encryption.md) 
- [Che cos'è Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
