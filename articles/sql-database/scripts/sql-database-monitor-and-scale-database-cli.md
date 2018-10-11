---
title: Esempio dell'interfaccia della riga di comando - Monitorare e ridimensionare un singolo database SQL di Azure | Microsoft Docs
description: Script di esempio dell'interfaccia della riga di comando di Azure per monitorare e ridimensionare un singolo database SQL di Azure
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: azurecli
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/02/2018
ms.openlocfilehash: 9021f04dad41f81b935b9412900336687011e87a
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249265"
---
# <a name="use-cli-to-monitor-and-scale-a-single-sql-database"></a>Usare l'interfaccia della riga di comando per monitorare e ridimensionare un singolo database SQL

Questo esempio di script dell'interfaccia della riga di comando di Azure ridimensiona un singolo database SQL di Azure per ottenere una dimensione di calcolo diversa dopo aver recuperato informazioni sulle dimensioni del database tramite query. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale single SQL Database")]

> [!TIP]
> Usare [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) per ottenere un elenco delle operazioni eseguite sul database e usare [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) per annullare un'operazione di aggiornamento sul database.

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#az-sql-server-create) | Crea un server logico che ospita un database. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-show-usage) | Mostra le informazioni sull'utilizzo delle dimensioni per un database. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update) | Aggiorna le proprietà del database, come il livello di servizio o la dimensione di calcolo, oppure sposta un database all'interno o all'esterno di un pool elastico o tra pool elastici diversi. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../sql-database-cli-samples.md).
