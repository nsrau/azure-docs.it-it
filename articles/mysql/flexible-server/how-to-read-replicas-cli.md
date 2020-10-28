---
title: Gestire le repliche di lettura nel database di Azure per MySQL server flessibile usando l'interfaccia della riga di comando di Azure
description: Informazioni su come configurare e gestire le repliche di lettura nel database di Azure per il server flessibile MySQL usando l'interfaccia della riga di comando di Azure.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 10/23/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: acbb0d5b643919d7fa1bf3966532ebd83129fc2a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92795270"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>Come creare e gestire le repliche di lettura nel database di Azure per il server flessibile MySQL usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Leggere repliche nel database di Azure per MySQL: il server flessibile è in anteprima.

In questo articolo si apprenderà come creare e gestire le repliche di lettura nel database di Azure per il server flessibile MySQL usando l'interfaccia della riga di comando di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

> [!Note]
> La replica non è supportata nel server abilitato per la disponibilità elevata. 

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
È possibile creare e gestire le repliche di lettura usando l'interfaccia della riga di comando di Azure.

### <a name="prerequisites"></a>Prerequisiti

- [Installare l'interfaccia della riga di comando Azure 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Un [server di database di Azure per MySQL flessibile](quickstart-create-server-cli.md) che verrà usato come server di origine.

### <a name="create-a-read-replica"></a>Creare una replica in lettura

> [!IMPORTANT]
> Quando si crea una replica per un'origine senza repliche, l'origine viene innanzitutto riavviata per prepararsi per la replica. Tenere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di scarso traffico.

È possibile creare un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> Le repliche di lettura vengono create con la stessa configurazione del server dell'origine. La configurazione del server di replica può essere modificata dopo la creazione. Il server di replica viene sempre creato nello stesso gruppo di risorse, nello stesso percorso e nella stessa sottoscrizione del server di origine. Per creare un server di replica in un gruppo di risorse diverso o in una sottoscrizione diversa, è possibile [spostare il server di replica](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) dopo averlo creato. È consigliabile mantenere la configurazione del server di replica con valori uguali o superiori a quelli dell'origine per assicurarsi che la replica sia in grado di rimanere al passo con l'origine.


### <a name="list-replicas-for-a-source-server"></a>Elencare le repliche per un server di origine

Per visualizzare tutte le repliche per un determinato server di origine, eseguire il comando seguente: 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica

> [!IMPORTANT]
> L'arresto della replica in un server è irreversibile. Una volta interrotta la replica tra un'origine e una replica, non è possibile annullarla. Il server di replica diventa quindi un server autonomo che supporta sia la lettura che la scrittura. Questo server non può essere di nuovo impostato come replica.

È possibile interrompere la replica su un server di replica in lettura usando il comando seguente:

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>Eliminare un server di replica

L'eliminazione di un server di replica in lettura può essere effettuata eseguendo il comando **[az mysql server delete](/cli/azure/mysql/server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>Eliminare un server di origine

> [!IMPORTANT]
> Eliminando un server di origine si arresta la replica in tutti i server di replica, oltre a eliminare il server di origine stesso. I server di replica diventano server autonomi che supportano sia la lettura che la scrittura.

Per eliminare un server di origine, è possibile eseguire il comando **[AZ MySQL flexible-server delete](/cli/azure/mysql/flexible-server)** .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle [repliche in lettura](concepts-read-replicas.md)
