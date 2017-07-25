---
title: Script di esempio dell'interfaccia della riga di comando di Azure per lo spostamento di un database SQL in un pool elastico SQL | Microsoft Docs
description: Script di esempio dell'interfaccia della riga di comando di Azure per lo spostamento di un database SQL in un pool elastico SQL
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 07/05/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: 1dc31a0b20f36e28a58896ed63a5e0395ae1d3af
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="use-cli-to-move-an-azure-sql-database-in-a-sql-elastic-pool"></a>Usare l'interfaccia della riga di comando per spostare un database SQL di Azure in un pool elastico SQL

Questo script di esempio dell'interfaccia della riga di comando di Azure crea due pool elastici e sposta un database SQL di Azure da un pool elastico all'altro, quindi sposta un database all'esterno di un pool elastico in un livello di prestazioni a database singolo di Azure. 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo argomento è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure 2.0]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/move-database-between-pools/move-database-between-pools.sh "Spostare database tra pool")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crea un server logico che ospita un database o un pool elastico. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pool#create) | Crea un pool elastico all'interno di un server logico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Crea un database in un server logico come database singolo o in pool. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Aggiorna le proprietà del database o sposta un database all'interno, all'esterno o tra pool elastici. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../sql-database-cli-samples.md).



