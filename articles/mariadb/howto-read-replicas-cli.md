---
title: Gestire le repliche di lettura - interfaccia della riga di comando di Azure, API REST - Database di Azure per MariaDBManage read replicas - Azure CLI, REST API - Azure Database for MariaDB
description: Questo articolo descrive come configurare e gestire le repliche di lettura nel database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure e l'API REST.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abf80e98881b73bed53c5a939a79bc8b3a9de2db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530581"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure e l'API RESTHow to create and manage read replicas in Azure Database for MariaDB using the Azure CLI and REST API

In questo articolo si apprenderà come creare e gestire le repliche di lettura nel servizio Database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure e l'API REST.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire repliche di lettura usando l'interfaccia della riga di comando di Azure.You can create and manage read replicas using the Azure CLI.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0Install Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Database [di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) che verrà usato come server master. 

> [!IMPORTANT]
> La funzionalità di replica di lettura è disponibile solo per i server Database di Azure per MariaDB nei piani dei prezzi Scopo generale o Ottimizzato per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

### <a name="create-a-read-replica"></a>Creare una replica in lettura

È possibile creare un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Il comando `az mariadb server replica create` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoreplicaserver | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID del server master esistente in base al quale eseguire la replica. |

Per creare una replica di `--location` lettura tra aree, utilizzare il parametro . 

> [!NOTE]
> La replica tra aree è in anteprima.

L'esempio CLI seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo sui concetti relativi alla replica di [lettura.](concepts-read-replicas.md) 

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

### <a name="list-replicas-for-a-master-server"></a>Elencare le repliche di un server master

Per visualizzare tutte le repliche per un determinato server master, eseguire il comando seguente: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Il comando `az mariadb server replica list` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| server-name | mydemoserver | Nome o ID del server master. |

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Dopo che la replica tra un master e una replica è stata arrestata, non è possibile annullare l'operazione. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

È possibile interrompere la replica su un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Il comando `az mariadb server replica stop` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui si trova il server di replica.  |
| name | mydemoreplicaserver | Nome del server di replica su cui si vuole arrestare la replica. |

### <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica di lettura può essere eseguita eseguendo il comando **[az mariadb server delete.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master, è possibile eseguire il comando **[az mariadb server delete.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>API REST
È possibile creare e gestire repliche di lettura usando l'API REST di Azure.You can create and manage read replicas using the [Azure REST API](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Creare una replica in lettura
È possibile creare una replica di lettura utilizzando [l'API di creazione:](/rest/api/mariadb/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
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
È possibile visualizzare l'elenco delle repliche di un server master utilizzando [l'API dell'elenco di replica:](/rest/api/mariadb/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile interrompere la replica tra un server master e una replica di lettura utilizzando l'API di [aggiornamento.](/rest/api/mariadb/servers/update)

L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replicaDelete a master or replica server
Per eliminare un server master o di replica, utilizzare [l'API di eliminazione:](/rest/api/mariadb/servers/delete)

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
