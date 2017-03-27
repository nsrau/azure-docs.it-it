---
title: 'Script dell&quot;interfaccia della riga di comando di Azure: monitorare e ridimensionare un database SQL singolo | Microsoft Docs'
description: 'Esempio di script dell&quot;interfaccia della riga di comando di Azure: monitorare e ridimensionare un database SQL singolo usando l&quot;interfaccia della riga di comando di Azure'
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
ms.openlocfilehash: 4cfc4ab09f7adead289cb949373730bffcfa13ec
ms.lasthandoff: 03/10/2017

---

# <a name="monitor-and-scale-a-single-sql-database-using-the-azure-cli"></a>Monitorare e ridimensionare un singolo database SQL usando l'interfaccia della riga di comando di Azure

Questo esempio di script dell'interfaccia della riga di comando ridimensiona un database SQL di Azure singolo per ottenere un livello di prestazioni diverso dopo l'esecuzione di query sulle dimensioni del database. 

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il comando `az login`. 

Questo esempio funziona in una shell Bash. Per le opzioni sull'esecuzione di script dell'interfaccia della riga di comando di Azure in Windows, vedere [Running the Azure CLI in Windows](../../virtual-machines/virtual-machines-windows-cli-options.md) (Esecuzione dell'interfaccia della riga di comando di Azure in Windows).


## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitorare e ridimensionare database SQL singoli")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo l'esecuzione dello script di esempio, è possibile usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse ad esso associate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az sql server create](https://docs.microsoft.com/cli/azure/sql/server#create) | Crea un server logico che ospita un database. |
| [az sql db show-usage](https://docs.microsoft.com/cli/azure/sql/db#show-usage) | Mostra le informazioni sull'utilizzo delle dimensioni per un database. |
| [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#update) | Aggiorna le proprietà del database, come il livello di servizio o il livello di prestazioni, oppure sposta un database all'interno, all'esterno o tra pool elastici. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Per altri esempi di script dell'interfaccia della riga di comando per database SQL, vedere la [documentazione del database SQL di Azure](../sql-database-cli-samples.md).

