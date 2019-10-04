---
title: Script dell'interfaccia della riga di comando di Azure - Ridimensionare un server Database di Azure per MySQL
description: Questo esempio di script dell'interfaccia della riga di comando di Azure scala un database di Azure per il MySQL a un diverso livello di prestazioni dopo le query sulle metriche.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 08/07/2019
ms.openlocfilehash: f54b3f6fa8bb37f57479d6a9e7bc05340e411a48
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882933"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Monitorare a scalare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure
Questo esempio di script dell'interfaccia della riga di comando ridimensiona le risorse di calcolo e archiviazione per un singolo database di Azure per il server MySQL dopo le query sulle metriche. È possibile aumentare o ridurre le prestazioni di calcolo. Le prestazioni di archiviazione possono solo essere aumentate.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Script di esempio
Aggiornare lo script con l'ID sottoscrizione.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Crea un server MySQL che ospita i database. |
| [az mysql server update](/cli/azure/mysql/server#az-mysql-server-update) | Aggiorna le proprietà del server MySQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Elencare il valore metrico per le risorse. |
| [az group delete](/cli/azure/group#az-group-delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [risorse di calcolo e archiviazione del database di Azure per MySQL](../concepts-pricing-tiers.md)
- Provare altri script: [Esempi dell'interfaccia della riga di comando di Azure per il database di Azure per MySQL](../sample-scripts-azure-cli.md)
- Altre informazioni sull'[interfaccia della riga di comando di Azure](/cli/azure)