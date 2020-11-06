---
title: Gestire le repliche di lettura-interfaccia della riga di comando di Azure, API REST-database di Azure per PostgreSQL-server singolo
description: Informazioni su come gestire le repliche di lettura nel database di Azure per PostgreSQL-server singolo dall'interfaccia della riga di comando di Azure e dall'API REST
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 11/05/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2fa8794066739302d2f32acb13c936c524dc89a8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422349"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Creare e gestire le repliche di lettura dall'interfaccia della riga di comando di Azure, API REST

Questo articolo illustra come creare e gestire le repliche di lettura nel database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure e l'API REST. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="azure-replication-support"></a>Supporto della replica di Azure
Le [repliche](concepts-read-replicas.md) e la [decodifica logica](concepts-logical.md) dipendono entrambi dal log write-ahead di Postgres (WAL) per informazioni. Queste due funzionalità richiedono diversi livelli di registrazione da postgres. Per la decodifica logica è necessario un livello di registrazione più elevato rispetto alla lettura delle repliche.

Per configurare il livello di registrazione corretto, usare il parametro di supporto della replica di Azure. Il supporto per la replica di Azure offre tre opzioni di impostazione:

* **Off** : inserisce le informazioni minime nell'oggetto Wal. Questa impostazione non è disponibile nella maggior parte dei server di database di Azure per PostgreSQL.  
* **Replica** : più dettagliata di **off**. Questo è il livello minimo di registrazione necessario per il funzionamento delle [repliche di lettura](concepts-read-replicas.md) . Questa impostazione è quella predefinita nella maggior parte dei server.
* **Logica** : più dettagliata rispetto alla **replica**. Questo è il livello minimo di registrazione per il funzionamento della decodifica logica. Le repliche di lettura funzionano anche con questa impostazione.


> [!NOTE]
> Quando si distribuiscono le repliche di lettura per carichi di lavoro primari intensi a elevato utilizzo di scrittura, l'intervallo di replica può continuare ad aumentare e non può mai essere in grado di recuperare il database primario. Questo può anche aumentare l'utilizzo dell'archiviazione nel database primario perché i file WAL non vengono eliminati fino a quando non vengono ricevuti dalla replica.

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire le repliche di lettura usando l'interfaccia della riga di comando di Azure.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando Azure 2,0](/cli/azure/install-azure-cli)
- Un server di [database di Azure per PostgreSQL](quickstart-create-server-up-azure-cli.md) come server primario.


### <a name="prepare-the-primary-server"></a>Preparare il server primario

1. Controllare il valore del server primario `azure.replication_support` . Per il corretto funzionamento delle repliche di lettura deve essere almeno una REPLICA.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Se `azure.replication_support` non è almeno una replica, impostarla. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Riavviare il server per applicare la modifica.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Creare una replica in lettura

Il comando [AZ Postgres server replica create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Il gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoserver-replica | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID risorsa del server primario esistente da cui eseguire la replica. Usare l'ID risorsa se si vuole che la replica e i gruppi di risorse del master siano diversi. |

Nell'esempio dell'interfaccia della riga di comando riportata di seguito, la replica viene creata nella stessa area del database master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Per creare una replica di lettura tra aree, usare il `--location` parametro. L'esempio dell'interfaccia della riga di comando seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

Se il parametro non è stato impostato `azure.replication_support` su **replica** in un per utilizzo generico o in un server primario con ottimizzazione per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

> [!IMPORTANT]
> Vedere la [sezione Considerazioni relativa alla panoramica di Read replica](concepts-read-replicas.md#considerations).
>
> Prima che un'impostazione del server primario venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server primario usando il comando [AZ Postgres server replica list](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server primario e una replica di lettura usando il comando [AZ Postgres server replica stop](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) .

Una volta terminata la replica in un server primario e una replica di lettura, non è possibile annullarla. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>Eliminare un server primario o di replica
Per eliminare un server primario o di replica, usare il comando [AZ Postgres server delete](/cli/azure/postgres/server#az-postgres-server-delete) .

Quando si elimina un server primario, la replica in tutte le repliche di lettura viene arrestata. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>API REST
È possibile creare e gestire le repliche di lettura usando l' [API REST di Azure](/rest/api/azure/).

### <a name="prepare-the-primary-server"></a>Preparare il server primario

1. Controllare il valore del server primario `azure.replication_support` . Per il corretto funzionamento delle repliche di lettura deve essere almeno una REPLICA.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Se `azure.replication_support` non è almeno una replica, impostarla.

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
> Per altre informazioni sulle aree in cui è possibile creare una replica, vedere l'articolo [Concetti relativi alle repliche in lettura](concepts-read-replicas.md). 

Se il parametro non è stato impostato `azure.replication_support` su **replica** in un per utilizzo generico o in un server primario con ottimizzazione per la memoria e il server è stato riavviato, viene visualizzato un errore. Completare questi due passaggi prima di creare una replica.

Una replica viene creata usando le stesse impostazioni di calcolo e di archiviazione del database master. Dopo la creazione di una replica, è possibile modificare diverse impostazioni indipendentemente dal server primario: generazione di calcolo, Vcore, archiviazione e periodo di conservazione di backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.


> [!IMPORTANT]
> Prima che un'impostazione del server primario venga aggiornata a un nuovo valore, aggiornare l'impostazione della replica a un valore uguale o maggiore. Questa azione consente alla replica di rimanere al passo con le modifiche apportate al database master.

### <a name="list-replicas"></a>Elencare le repliche
È possibile visualizzare l'elenco delle repliche di un server primario usando l' [API di elenco delle repliche](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server primario e una replica di lettura usando l' [API di aggiornamento](/rest/api/postgresql/servers/update).

Una volta terminata la replica in un server primario e una replica di lettura, non è possibile annullarla. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

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

### <a name="delete-a-primary-or-replica-server"></a>Eliminare un server primario o di replica
Per eliminare un server primario o di replica, usare l' [API Delete](/rest/api/postgresql/servers/delete):

Quando si elimina un server primario, la replica in tutte le repliche di lettura viene arrestata. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Passaggi successivi
* Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).
* Informazioni su come [creare e gestire le repliche in lettura nel portale di Azure](howto-read-replicas-portal.md).