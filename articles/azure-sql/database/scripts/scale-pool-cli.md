---
title: 'Interfaccia della riga di comando di Azure: Dimensionare un pool elastico'
description: Usare uno script di esempio dell'interfaccia della riga di comando di Azure per dimensionare un pool elastico nel database SQL di Azure.
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
ms.openlocfilehash: 01ccf7117cd17118941a00cc0480216bc436bc40
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514765"
---
# <a name="use-the-azure-cli-to-scale-an-elastic-pool-in-azure-sql-database"></a>Usare l'interfaccia della riga di comando di Azure per dimensionare un pool elastico nel database SQL di Azure

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo script di esempio dell'interfaccia della riga di comando di Azure crea pool elastici nel database SQL di Azure, sposta i database in pool e modifica le dimensioni di calcolo del pool elastico.

Se si sceglie di installare e usare l'interfaccia della riga di comando di Azure in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Move database between pools")]

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
| [az sql db](/cli/azure/sql/db) | Comandi per il database. |
| [az sql elastic-pools](/cli/azure/sql/elastic-pool) | Comandi per il pool elastico. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../az-cli-script-samples-content-guide.md).
