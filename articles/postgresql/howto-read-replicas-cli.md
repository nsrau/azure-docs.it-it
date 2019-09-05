---
title: Gestire le repliche di lettura per il database di Azure per PostgreSQL-server singolo dall'interfaccia della riga di comando di Azure
description: Informazioni su come gestire le repliche di lettura nel database di Azure per PostgreSQL-server singolo dall'interfaccia della riga di comando di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: 5946c74d0075e04112e840d78dd9f5f57bec7475
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309388"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Creare e gestire le repliche di lettura dall'interfaccia della riga di comando di Azure

Questo articolo illustra come creare e gestire le repliche di lettura in database di Azure per PostgreSQL dall'interfaccia della riga di comando di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="prerequisites"></a>Prerequisiti
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-up-azure-cli.md) che verrà usato come server master.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Quando questo parametro statico viene modificato, è necessario riavviare il server per rendere effettive le modifiche.

1. Impostare `azure.replication_support` su replica.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Riavviare per applicare la modifica al server.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Creare una replica in lettura

Il comando [AZ Postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Il gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoserver-replica | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID risorsa del server master esistente da cui eseguire la replica. |

Nell'esempio dell'interfaccia della riga di comando riportata di seguito, la replica viene creata nella stessa area del database master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Per creare una replica di lettura tra aree, usare `--location` il parametro. L'esempio dell'interfaccia della riga di comando seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l' [articolo leggere i concetti relativi alla replica](concepts-read-replicas.md). 

Se il `azure.replication_support` parametro non è stato impostato su **replica** in un per utilizzo generico o in un server master con ottimizzazione per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

## <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server master usando il comando [AZ Postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server master e una replica di lettura usando il comando [AZ Postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replica
Per eliminare un server master o di replica, è necessario usare il comando [AZ Postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).