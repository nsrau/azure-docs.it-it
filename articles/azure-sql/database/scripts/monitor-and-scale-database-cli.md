---
title: 'Interfaccia della riga di comando di Azure: Monitorare e dimensionare un database singolo nel database SQL di Azure'
description: Usare uno script di esempio dell'interfaccia della riga di comando di Azure per monitorare e dimensionare un database singolo nel database SQL di Azure.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: juliemsft
ms.author: jrasnick
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: c4df9ecc025bbffb63730273be06f54cf46f613c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319398"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Usare l'interfaccia della riga di comando di Azure per monitorare e dimensionare un database singolo nel database SQL di Azure

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di script dell'interfaccia della riga di comando di Azure consente di dimensionare un database singolo nel database SQL di Azure in base a dimensioni di calcolo diverse dopo aver recuperato informazioni sulle dimensioni del database tramite query.

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Usare [az sql db op list](/cli/azure/sql/db/op?#az-sql-db-op-list) per ottenere un elenco delle operazioni eseguite sul database e usare [az sql db op cancel](/cli/azure/sql/db/op#az-sql-db-op-cancel) per annullare un'operazione di aggiornamento sul database.

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Script | Descrizione |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandi per il server. |
| [az sql db show-usage](/cli/azure/sql#az-sql-show-usage) | Mostra le informazioni sull'utilizzo delle dimensioni per un database. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando, vedere [Script di esempio dell'interfaccia della riga di comando di Azure](../az-cli-script-samples-content-guide.md).
