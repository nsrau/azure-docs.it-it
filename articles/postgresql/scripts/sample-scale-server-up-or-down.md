---
title: Script dell'interfaccia della riga di comando di Azure - Ridimensionare Database di Azure per PostgreSQL
description: Esempio di script dell'interfaccia della riga di comando di Azure - Scalare il database di Azure per il server PostgreSQL a un diverso livello di prestazioni dopo le query sulle metriche.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc
ms.topic: sample
ms.date: 04/05/2018
ms.openlocfilehash: 37b7364d48508ad557e6118487c0cb88f9df8e4f
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548756"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Monitorare e scalare un singolo server PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo esempio di script dell'interfaccia della riga di comando di Azure scala un singolo database di Azure per il server PostgreSQL a un diverso livello di prestazioni dopo le query sulle metriche. 

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori. Sostituire l'ID sottoscrizione usato nei comandi `az monitor` con il proprio ID sottoscrizione.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh?highlight=15-16 "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgres server create](/cli/azure/postgres/server#az_postgres_server_create) | Crea un server PostgreSQL che ospita i database. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Elencare il valore metrico per le risorse. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sull'interfaccia della riga di comando di Azure: [Documentazione dell'interfaccia della riga di comando di Azure](/cli/azure)
- Provare altri script: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)
- Altre informazioni sul ridimensionamento: [Livelli di servizio](../concepts-service-tiers.md) e [Unità di archiviazione e unità di calcolo](../concepts-compute-unit-and-storage.md)
