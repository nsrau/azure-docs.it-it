---
title: 'Interfaccia della riga di comando di Azure: Aggiungere un database a un gruppo di failover'
description: Usare lo script di esempio dell'interfaccia della riga di comando di Azure per creare un database nel database SQL di Azure, aggiungerlo a un gruppo di failover automatico e testare il failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 8a8aac276617e97c26d7b05e6fe2518e5d575fab
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86514782"
---
# <a name="use-the-azure-cli-to-add-a-database-to-a-failover-group"></a>Usare l'interfaccia della riga di comando di Azure per aggiungere un database a un gruppo di failover

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Questo esempio di script dell'interfaccia della riga di comando di Azure crea un database nel database SQL di Azure, crea un gruppo di failover, aggiunge il database al gruppo di failover e testa il failover.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio

### <a name="sign-in-to-azure"></a>Accedere ad Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Eseguire lo script

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add Azure SQL Database to failover group")]

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Informazioni di riferimento per l'esempio

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Descrizione |
|---|---|
| [az sql db](/cli/azure/sql/db) | Comandi per il database. |
| [az sql failover-group](/cli/azure/sql/failover-group) | Comandi per il gruppo di failover. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../az-cli-script-samples-content-guide.md).
