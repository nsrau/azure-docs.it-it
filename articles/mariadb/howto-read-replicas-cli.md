---
title: 'Creare e gestire le repliche di lettura nel database di Azure per MariaDB: interfaccia della riga di comando di AZURE, API REST'
description: Questo articolo descrive come configurare e gestire le repliche di lettura nel database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure e l'API REST.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 2f5029ccbf80551721ecc363aa4c3930961d9154
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993065"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure e l'API REST

In questo articolo si apprenderà come creare e gestire le repliche di lettura nel database di Azure per il servizio MariaDB usando l'interfaccia della riga di comando di Azure e l'API REST.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire le repliche di lettura usando l'interfaccia della riga di comando di Azure.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) che verrà usato come server master. 

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

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

Per creare una replica di lettura tra aree, usare `--location` il parametro. L'esempio dell'interfaccia della riga di comando seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l' [articolo leggere i concetti relativi alla replica](concepts-read-replicas.md). 

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

### <a name="list-replicas-for-a-master-server"></a>Elencare le repliche di un server master

Per visualizzare tutte le repliche per un determinato server master, eseguire il comando seguente: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Il comando `az mariadb server replica list` richiede i parametri seguenti:

| Impostazione | Valore di esempio | DESCRIZIONE  |
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

L'eliminazione di un server di replica di lettura può essere eseguita eseguendo il comando **[AZ mariadb server delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master, è possibile eseguire il comando **[AZ mariadb server delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>API REST
È possibile creare e gestire le repliche di lettura usando l' [API REST di Azure](/rest/api/azure/).

### <a name="create-a-read-replica"></a>Creare una replica in lettura
È possibile creare una replica di lettura tramite l' [API di creazione](/rest/api/mariadb/servers/create):

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
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l' [articolo leggere i concetti relativi alla replica](concepts-read-replicas.md). 

Se il `azure.replication_support` parametro non è stato impostato su **replica** in un per utilizzo generico o in un server master con ottimizzazione per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.


> [!IMPORTANT]
> Prima che un'impostazione del server master venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server master usando l' [API dell'elenco di repliche](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server master e una replica di lettura usando l' [API di aggiornamento](/rest/api/mariadb/servers/update).

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

### <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replica
Per eliminare un server master o di replica, usare l' [API Delete](/rest/api/mariadb/servers/delete):

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
