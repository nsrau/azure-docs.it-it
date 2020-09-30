---
title: "L'interfaccia della riga di comando di Azure: Spostare un database tra pool elastici"
description: Usare uno script di esempio dell'interfaccia della riga di comando di Azure per creare due pool elastici e spostare un database nel database SQL da un pool elastico all'altro.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: a71e3e05e486c09d148062eed210c9f4b21e8226
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91319351"
---
# <a name="use-the-azure-cli-to-move-a-database-in-sql-database-in-a-sql-elastic-pool"></a>Usare l'interfaccia della riga di comando di Azure per spostare un database nel database SQL in un pool elastico SQL

Questo script di esempio dell'interfaccia della riga di comando di Azure crea due pool elastici, sposta un database in pool nel database SQL da un pool elastico SQL a un altro e quindi sposta il database in pool all'esterno del pool elastico SQL in modo che sia un database singolo nel database SQL.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Move database between pools")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Descrizione |
|---|---|
| [az sql server](/cli/azure/sql/server) | Comandi per il server. |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | Comandi per il pool elastico. |
| [az sql db](/cli/azure/sql/db) | Comandi per il database. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../az-cli-script-samples-content-guide.md).
