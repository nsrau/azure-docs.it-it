---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Eseguire l'esportazione da un archivio di Configurazione app di Azure | Microsoft Docs
description: Questo articolo fornisce informazioni e script di esempio per l'esportazione da un archivio di Configurazione app di Azure
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
ms.openlocfilehash: 3310dc5d72284e8d94b95b855fee90d560205fa4
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884388"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Esportare da un archivio di Configurazione app di Azure

Questo script di esempio esporta coppie chiave-valore da un archivio di Configurazione app di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È prima necessario installare l'estensione dell'interfaccia della riga di comando di Configurazione app di Azure eseguendo il comando seguente:

        az extension add -n appconfig

## <a name="sample-script"></a>Script di esempio

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per esportare un archivio di configurazione di app. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az appconfig export](/cli/azure/ext/appconfig/appconfig#ext-appconfig-az-appconfig-export) | Esegue l'esportazione da una risorsa dell'archivio di configurazione di app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di configurazione di app sono disponibili nella [documentazione relativa al servizio Configurazione app di Azure](../cli-samples.md).
