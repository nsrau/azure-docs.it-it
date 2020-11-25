---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Usare coppie chiave-valore in un archivio di Configurazione app
titleSuffix: Azure App Configuration
description: Usare lo script dell'interfaccia della riga di comando di Azure per creare, visualizzare, aggiornare ed eliminare coppie chiave-valore dall'archivio di Configurazione app
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: lcozzens
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2610d0ee448e274b9bdeb7396393ce47e27e65ef
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565756"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Usare coppie chiave-valore in un archivio di Configurazione app di Azure

Questo script di esempio mostra come:
* Crea una nuova coppia chiave-valore
* Elencare tutte le coppie chiave-valore esistenti
* Aggiornare il valore di una chiave appena creata
* Eliminare la nuova coppia chiave-valore

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Questa esercitazione richiede la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.
## <a name="sample-script"></a>Script di esempio

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set --name $appConfigName --key $refKey --content-type "application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8" --value "{\"uri\":\"$uri\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set --name $appConfigName --key $refKey --value "{\"uri\":\"$uri2\"}"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questa tabella elenca i comandi usati nello script di esempio. 

| Comando | Note |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az-appconfig-kv-set) | Crea o aggiorna una coppia chiave-valore. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az-appconfig-kv-list) | Elenca le coppie chiave-valore in un archivio di Configurazione app. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az-appconfig-kv-delete) | Elimina una coppia chiave-valore. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script sono disponibili negli [esempi dell'interfaccia della riga di comando di Configurazione app di Azure](../cli-samples.md).
