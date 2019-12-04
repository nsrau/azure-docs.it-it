---
title: Gestire le repliche di lettura-interfaccia della riga di comando di Azure, API REST-database di Azure per PostgreSQL-server singolo
description: Informazioni su come gestire le repliche di lettura nel database di Azure per PostgreSQL-server singolo dall'interfaccia della riga di comando di Azure e dall'API REST
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: fb0803987428ced688e83a37fae36c61b63a28a8
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74770119"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Creare e gestire le repliche di lettura dall'interfaccia della riga di comando di Azure, API REST

Questo articolo illustra come creare e gestire le repliche di lettura nel database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure e l'API REST. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire le repliche di lettura usando l'interfaccia della riga di comando di Azure.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-up-azure-cli.md) che verrà usato come server master.


### <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Quando questo parametro statico viene modificato, è necessario riavviare il server per rendere effettive le modifiche.

1. Impostare `azure.replication_support` su REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Riavviare il server per applicare la modifica.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Creare una replica in lettura

Il comando [AZ Postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) richiede i parametri seguenti:

| Impostazione | Valore di esempio | Description  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Il gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoserver-replica | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID risorsa del server master esistente da cui eseguire la replica. |

Nell'esempio dell'interfaccia della riga di comando riportata di seguito, la replica viene creata nella stessa area del database master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Per creare una replica di lettura tra aree, usare il parametro `--location`. L'esempio dell'interfaccia della riga di comando seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l' [articolo leggere i concetti relativi alla replica](concepts-read-replicas.md). 

Se il parametro `azure.replication_support` non è stato impostato su **replica** in un per utilizzo generico o in un server master con ottimizzazione per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server master usando il comando [AZ Postgres server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server master e una replica di lettura usando il comando [AZ Postgres server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replica
Per eliminare un server master o di replica, è necessario usare il comando [AZ Postgres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
È possibile creare e gestire le repliche di lettura usando l' [API REST di Azure](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Quando questo parametro statico viene modificato, è necessario riavviare il server per rendere effettive le modifiche.

1. Impostare `azure.replication_support` su REPLICA.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Riavviare il server](/rest/api/postgresql/servers/restart) per applicare la modifica.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Creare una replica in lettura
È possibile creare una replica di lettura tramite l' [API di creazione](/rest/api/postgresql/servers/create):

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l' [articolo leggere i concetti relativi alla replica](concepts-read-replicas.md). 

Se il parametro `azure.replication_support` non è stato impostato su **replica** in un per utilizzo generico o in un server master con ottimizzazione per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.


> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server master usando l' [API dell'elenco di repliche](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server master e una replica di lettura usando l' [API di aggiornamento](/rest/api/postgresql/servers/update).

L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replica
Per eliminare un server master o di replica, usare l' [API Delete](/rest/api/postgresql/servers/delete):

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passaggi successivi
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).