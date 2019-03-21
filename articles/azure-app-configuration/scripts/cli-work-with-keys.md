---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Usare coppie chiave-valore in un archivio di Configurazione app di Azure | Microsoft Docs
description: Fornisce informazioni sull'uso di coppie chiave-valore in un archivio di Configurazione app di Azure
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: azure-app-configuration
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9288ea08da6335dd29e7a15a9bc871b76c1ce7e9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57438432"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Usare coppie chiave-valore in un archivio di Configurazione app di Azure

Questo script di esempio crea un nuova coppia chiave-valore in un archivio di Configurazione app di Azure, elenca tutte le coppie chiave-valore esistenti, aggiorna il valore della chiave appena creata e infine la elimina.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È prima necessario installare l'estensione dell'interfaccia della riga di comando di Configurazione app di Azure eseguendo il comando seguente:

        az extension add -n appconfig

## <a name="sample-script"></a>Script di esempio

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per eseguire operazioni sulle coppie chiave-valore in un archivio di configurazione di app. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az appconfig kv set](/cli/azure/ext/appconfig/appconfig) | Crea o aggiorna una coppia chiave-valore. |
| [az appconfig kv list](/cli/azure/ext/appconfig/appconfig) | Elenca le coppie chiave-valore in un archivio di configurazione di app. |
| [az appconfig kv delete](/cli/azure/ext/appconfig/appconfig) | Elimina una coppia chiave-valore. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di configurazione di app sono disponibili nella [documentazione relativa al servizio Configurazione app di Azure](../cli-samples.md).
