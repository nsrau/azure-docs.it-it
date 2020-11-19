---
title: Script dell'interfaccia della riga di comando di Azure - Ripristinare un server Database di Azure per PostgreSQL
description: Questo script di esempio dell'interfaccia della riga di comando di Azure mostra come ripristinare un server Database di Azure per PostgreSQL e i relativi database a un momento precedente.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 02/28/2018
ms.openlocfilehash: 9af4a3d4a484ef22e147640add455741c533e9b7
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660475"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Ripristinare un server di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo script di esempio dell'interfaccia della riga di comando di Azure ripristina a un momento precedente un singolo server di Database di Azure per PostgreSQL.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede la versione 2.0 dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="sample-script"></a>Script di esempio
In questo script di esempio modificare le righe evidenziate per aggiornare il nome utente e la password amministratore con i propri valori. Sostituire l'ID sottoscrizione usato nei comandi `az monitor` con il proprio ID sottoscrizione.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/backup-restore.sh?highlight=15-16 "Restore Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione
Dopo l'esecuzione dello script, usare il comando seguente per rimuovere il gruppo di risorse e tutte le risorse a esso associate. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/backup-restore/delete-postgresql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Spiegazione dello script
Questo script usa i comandi illustrati nella tabella seguente:

| **Comando** | **Note** |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgresql server create](/cli/azure/postgres/server#az-postgres-server-create) | Crea un server PostgreSQL che ospita i database. |
| [az postgresql server restore](/cli/azure/postgres/server#az-postgres-server-restore) | Ripristina un server da un backup. |
| [az group delete](/cli/azure/group) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni sull'interfaccia della riga di comando di Azure: [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
- Provare altri script: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)
- [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](../howto-restore-server-portal.md)
