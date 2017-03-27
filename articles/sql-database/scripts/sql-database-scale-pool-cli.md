---
title: Script dell&quot;interfaccia della riga di comando di Azure - Ridimensionare un pool elastico | Microsoft Docs
description: Esempio di script dell&quot;interfaccia della riga di comando di Azure - Ridimensionare un pool di database elastico
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: CLI
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/14/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 01e0c45556b67b844a4e4ab5e0eb8644e8673dd1
ms.lasthandoff: 03/10/2017

---

# <a name="scale-an-elastic-pool-in-azure-sql-database-using-the-azure-cli"></a>Ridimensionare un pool elastico in un database SQL di Azure usando l'interfaccia della riga di comando di Azure

Questo script di esempio dell'interfaccia della riga di comando crea pool elastici, sposta i database in pool e modifica i livelli di prestazioni. 

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`. 

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure in Windows, vedere [Running the Azure CLI in Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/sql-database/scale-pool/scale-pool.sh "Spostare database tra pool")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un server logico, un database SQL e le regole del firewall. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Consente di creare un server logico che ospita il database SQL. |
| [az sql elastic-pools create](https://docs.microsoft.com/cli/azure/sql/elastic-pools#create) | Consente di creare un pool di database elastico all'interno del server logico. |
| [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#create) | Consente di creare il database SQL nel server logico. |
| [az sql elastic-pools update](https://docs.microsoft.com/cli/azure/sql/elastic-pools#update) | Consente di aggiornare un pool di database elastico, in questo esempio modifica l'eDTU assegnato. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../sql-database-cli-samples.md).

