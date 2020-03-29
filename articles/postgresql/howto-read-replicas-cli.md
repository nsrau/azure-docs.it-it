---
title: Gestire le repliche di lettura - interfaccia della riga di comando di Azure, API REST - Database di Azure per PostgreSQL - Server singoloManage read replicas - Azure CLI, REST API - Azure Database for PostgreSQL - Single Server
description: Informazioni su come gestire le repliche di lettura nel database di Azure per PostgreSQL - Singolo server dall'interfaccia della riga di comando di Azure e dall'API REST
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774796"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Creare e gestire repliche di lettura dall'interfaccia della riga di comando di Azure, l'API RESTCreate and manage read replicas from the Azure CLI, REST API

In questo articolo viene illustrato come creare e gestire repliche di lettura nel database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure e l'API REST. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire repliche di lettura usando l'interfaccia della riga di comando di Azure.You can create and manage read replicas using the Azure CLI.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-up-azure-cli.md) che verrà usato come server master.


### <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Quando questo parametro statico viene modificato, è necessario riavviare il server per rendere effettiva la modifica.

1. Impostare su `azure.replication_support` REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Se viene visualizzato l'errore "Valore non valido specificato" durante il tentativo di impostare azure.replication_support dall'interfaccia della riga di comando di Azure, è probabile che nel server sia già impostato REPLICA per impostazione predefinita. Un bug impedisce che questa impostazione venga riflessa correttamente nei server più recenti in cui REPLICA è l'impostazione predefinita interna. <br><br>
> È possibile ignorare i passaggi di preparazione master e passare alla creazione della replica. <br><br>
> Se si vuole verificare che il server sia in questa categoria, visitare la pagina di replica del server nel portale di Azure.If you would would to confirm your server is in this category, visit the server's replication page in the Azure portal. "Disabilita replica" verrà visualizzato in grigio e "Aggiungi replica" sarà attivo nella barra degli strumenti.

2. Riavviare il server per applicare la modifica.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Creare una replica in lettura

Il comando [az postgres server replica create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoserver-replica | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID risorsa del server master esistente da cui eseguire la replica. |

Nell'esempio CLI riportato di seguito, la replica viene creata nella stessa area del master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Per creare una replica di `--location` lettura tra aree, utilizzare il parametro . L'esempio CLI seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo sui concetti relativi alla replica di [lettura.](concepts-read-replicas.md) 

Se il `azure.replication_support` parametro non è stato impostato su **REPLICA** su un server master Master Master master di master utilizzo software generico o Ottimizzato per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata utilizzando le stesse impostazioni di calcolo e archiviazione del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica su un valore uguale o maggiore. Questa azione consente alla replica di tenere il passo con le modifiche apportate al master.

### <a name="list-replicas"></a>Replica di elenchi
È possibile visualizzare l'elenco delle repliche di un server master utilizzando il comando [az postgres server replica list.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile interrompere la replica tra un server master e una replica di lettura utilizzando il comando [az postgres server replica stop.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replicaDelete a master or replica server
Per eliminare un server master o di replica, utilizzare il comando [az postgres server delete.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
È possibile creare e gestire repliche di lettura usando l'API REST di Azure.You can create and manage read replicas using the [Azure REST API](/rest/api/azure/).

### <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Quando questo parametro statico viene modificato, è necessario riavviare il server per rendere effettiva la modifica.

1. Impostare su `azure.replication_support` REPLICA.

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
È possibile creare una replica di lettura utilizzando [l'API di creazione:](/rest/api/postgresql/servers/create)

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
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo sui concetti relativi alla replica di [lettura.](concepts-read-replicas.md) 

Se il `azure.replication_support` parametro non è stato impostato su **REPLICA** su un server master Master Master master di master utilizzo software generico o Ottimizzato per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata utilizzando le stesse impostazioni di calcolo e archiviazione del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.


> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica su un valore uguale o maggiore. Questa azione consente alla replica di tenere il passo con le modifiche apportate al master.

### <a name="list-replicas"></a>Replica di elenchi
È possibile visualizzare l'elenco delle repliche di un server master utilizzando [l'API dell'elenco di replica:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile interrompere la replica tra un server master e una replica di lettura utilizzando l'API di [aggiornamento.](/rest/api/postgresql/servers/update)

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

### <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replicaDelete a master or replica server
Per eliminare un server master o di replica, utilizzare [l'API di eliminazione:](/rest/api/postgresql/servers/delete)

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passaggi successivi
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).
