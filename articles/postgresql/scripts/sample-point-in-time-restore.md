---
title: Script dell'interfaccia della riga di comando di Azure - Ripristinare un server Database di Azure per PostgreSQL
description: Questo script di esempio dell'interfaccia della riga di comando di Azure mostra come ripristinare un server Database di Azure per PostgreSQL e i relativi database a un momento precedente.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc
ms.date: 02/28/2018
ms.openlocfilehash: 721e385d7c8e6b401ce1fe6d3518006f3a96e4fe
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541099"
---
# <a name="restore-an-azure-database-for-postgresql-server-using-azure-cli"></a>Ripristinare un server di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure
Questo script di esempio dell'interfaccia della riga di comando di Azure ripristina a un momento precedente un singolo server di Database di Azure per PostgreSQL.

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, per questo articolo è necessaria l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Controllare la versione eseguendo `az --version`. Per installare l'interfaccia della riga di comando di Azure o aggiornarne la versione, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

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
| [az group create](/cli/azure/group#az_group_create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az postgresql server create](/cli/azure/postgres/server#az-postgres-server-create) | Crea un server PostgreSQL che ospita i database. |
| [az postgresql server restore](/cli/azure/postgres/server#az-postgres-server-restore) | Ripristina un server da un backup. |
| [az group delete](/cli/azure/group#az_group_delete) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sull'interfaccia della riga di comando di Azure: [Documentazione dell'interfaccia della riga di comando di Azure](/cli/azure).
- Provare altri script: [Azure CLI samples for Azure Database for PostgreSQL](../sample-scripts-azure-cli.md) (Esempi di interfaccia della riga di comando di Azure per Database di Azure per PostgreSQL)
- [Come eseguire la procedura di backup e ripristino di un server in Database di Azure per PostgreSQL usando il portale di Azure](../howto-restore-server-portal.md)
