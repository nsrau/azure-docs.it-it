---
title: Creare e gestire le repliche in lettura in Database di Azure per MySQL
description: Questo articolo descrive come configurare e gestire le repliche in lettura in Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: ba8af55f7467e361136e4b0c57c97b4fa187cec0
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304953"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli"></a>Come creare e gestire le repliche in lettura in Database di Azure per MySQL con l'interfaccia della riga di comando di Azure

Questo articolo illustra come creare e gestire le repliche in lettura nella stessa area di Azure del master nel servizio Database di Azure per MySQL usando l'interfaccia della riga di comando di Azure.

> [!IMPORTANT]
> È possibile creare una replica di lettura nella stessa area del server master o in altre aree di Azure di propria scelta. La replica tra aree è attualmente in anteprima pubblica.

## <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando di Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Istanza di [Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) che verrà usata come server master. 

> [!IMPORTANT]
> La funzionalità relativa alle repliche in lettura è disponibile solo per i server di Database di Azure per MySQL nei piani tariffari Utilizzo generico o Con ottimizzazione per la memoria. Verificare che il server master sia incluso in uno di questi piani tariffari.

## <a name="create-a-read-replica"></a>Creare una replica in lettura

È possibile creare un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

Il comando `az mysql server replica create` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoreplicaserver | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | Nome o ID del server master esistente in base al quale eseguire la replica. |

Per creare una croce area leggere replica, usare il `--location` parametro. L'esempio di riga di comando seguente crea la replica negli Stati Uniti occidentali.

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Le repliche in lettura vengono create con la stessa configurazione server del master. La configurazione del server di replica può essere modificata dopo la creazione. È consigliabile mantenere nella configurazione del server di replica valori maggiori o uguali a quelli del master affinché la replica possa restare al passo con il master.

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Dopo che la replica tra un master e una replica è stata arrestata, non è possibile annullare l'operazione. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

È possibile interrompere la replica su un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

Il comando `az mysql server replica stop` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui si trova il server di replica.  |
| name | mydemoreplicaserver | Nome del server di replica su cui si vuole arrestare la replica. |

## <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica in lettura può essere effettuata eseguendo il comando **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Eliminare un server master

> [!IMPORTANT]
> Eliminando un server master si arresta la replica in tutti i server di replica, oltre a eliminare il server master stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server master, è possibile eseguire il comando **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Elencare le repliche di un server master

Per visualizzare tutte le repliche per un determinato server master, eseguire il comando seguente: 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

Il comando `az mysql server replica list` richiede i parametri seguenti:

| Impostazione | Valore di esempio | Descrizione  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  Gruppo di risorse in cui verrà creato il server di replica.  |
| server-name | mydemoserver | Nome o ID del server master. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
