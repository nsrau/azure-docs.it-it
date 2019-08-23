---
title: Creare e gestire le repliche di lettura nel database di Azure per MariaDB
description: Questo articolo descrive come configurare e gestire le repliche di lettura nel database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 7944f985f2317690f3a13add783192c49acbe22f
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907652"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli"></a>Come creare e gestire le repliche di lettura nel database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure

In questo articolo si apprenderà come creare e gestire le repliche di lettura nella stessa area di Azure del master nel database di Azure per il servizio MariaDB usando l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> È possibile creare una replica di lettura nella stessa area del server master o in qualsiasi altra area di Azure di propria scelta. Le repliche di lettura (stessa area e tra più aree) sono attualmente in anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [database di Azure per il server MariaDB](quickstart-create-mariadb-server-database-using-azure-portal.md) che verrà usato come server master. 

> [!IMPORTANT]
> La funzionalità di lettura della replica è disponibile solo per i server di database di Azure per MariaDB nei piani tariffari per utilizzo generico o con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

È possibile creare un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Il comando `az mariadb server replica create` richiede i parametri seguenti:

| Impostazione | Valore di esempio | DESCRIZIONE  |
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

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

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

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica di lettura può essere eseguita eseguendo il comando **[AZ mariadb server delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master, è possibile eseguire il comando **[AZ mariadb server delete](/cli/azure/mariadb/server)** .

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Elencare le repliche di un server master

Per visualizzare tutte le repliche per un determinato server master, eseguire il comando seguente: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Il comando `az mariadb server replica list` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| server-name | mydemoserver | Nome o ID del server master. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
