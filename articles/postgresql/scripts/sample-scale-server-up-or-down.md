---
title: Script dell'interfaccia della riga di comando di Azure - Ridimensionare e monitorare il database di Azure per PostgreSQL
description: Esempio di script dell'interfaccia della riga di comando di Azure - Scalare il database di Azure per il server PostgreSQL a un diverso livello di prestazioni dopo le query sulle metriche.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 6bbf5f3a0a7d32425f80687de10444ee0819b9df
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660458"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorare e scalare un singolo server PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo esempio di script dell'interfaccia della riga di comando ridimensiona le risorse di calcolo e archiviazione per un singolo database di Azure per il server PostgreSQL dopo le query sulle metriche. È possibile aumentare o ridurre le prestazioni di calcolo. Le prestazioni di archiviazione possono solo essere aumentate. 

> [!IMPORTANT] 
> L'archiviazione può essere solo aumentata, non ridotta.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio
Aggiornare lo script con l'ID sottoscrizione.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Crea un server PostgreSQL che ospita i database. |
| [az postgres server update](/cli/azure/postgres/server#az-postgres-server-update) | Aggiorna le proprietà del server PostgreSQL. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Elencare il valore metrico per le risorse. |
| [az group delete](/cli/azure/group) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sulle [risorse di calcolo e archiviazione del database di Azure per PostgreSQL](../concepts-pricing-tiers.md)
- Provare altri script: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)
- Altre informazioni sull'[interfaccia della riga di comando di Azure](/cli/azure)
