---
title: Gestire le repliche di lettura per il Database di Azure per PostgreSQL dal comando di Azure
description: Informazioni su come gestire i Database di Azure per PostgreSQL leggere repliche della riga di comando di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: b5e0336a290090ed6bd7f5af508e691677780a80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60420241"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Creare e gestire le repliche di lettura della riga di comando di Azure

In questo articolo descrive come creare e gestire le repliche di lettura nel Database di Azure per PostgreSQL dal comando di Azure. Per altre informazioni sulle repliche in lettura, vedere la [panoramica](concepts-read-replicas.md).

## <a name="prerequisites"></a>Prerequisiti
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-up-azure-cli.md) che verrà usato come server master.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questo articolo è necessario eseguire la versione 2.0 o successiva dell'interfaccia della riga di comando di Azure. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli). 


## <a name="prepare-the-master-server"></a>Preparare il server master
È necessario seguire questi passaggi per preparare un server master nei livelli Utilizzo generico o Con ottimizzazione per la memoria.

Il `azure.replication_support`parametro deve essere impostato su **REPLICA** nel server master. Quando questo parametro statico viene modificato, riavviare il server è necessario rendere effettiva la modifica.

1. Impostare `azure.replication_support` alla REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Riavvio per applicare la modifica al server.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Creare una replica in lettura

Il [replica di az postgres server create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) comando richiede i parametri seguenti:

| Impostazione | Valore di esempio | DESCRIZIONE  |
| --- | --- | --- |
| resource-group | myresourcegroup |  Il gruppo di risorse in cui verrà creato il server di replica.  |
| name | mydemoserver-replica | Nome del nuovo server di replica creato. |
| source-server | mydemoserver | L'ID risorsa o nome del server master esistente per la replica da. |

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Se non è stata impostata la `azure.replication_support` parametro per **REPLICA** su un utilizzo generico o con ottimizzazione per la memoria server master e riavviare il server, viene visualizzato un errore. Completare i due passaggi prima di creare una replica.

Una replica viene creata usando la stessa configurazione server del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

## <a name="list-replicas"></a>Elenco repliche
È possibile visualizzare l'elenco di repliche di un server master tramite [elenco di az postgres server replica](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) comando.

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Arrestare la replica in un server di replica
È possibile arrestare la replica tra un server master e una replica di lettura usando [stop di az postgres server replica](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) comando.

L'arresto della replica in un server master e una replica in lettura è irreversibile. La replica in lettura diventa un server autonomo che supporta sia la lettura che la scrittura. Il server autonomo non può essere di nuovo impostato come replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Eliminare un server master o di replica
Per eliminare un server master o di replica, si utilizza il [delete di az postgres server](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) comando.

Quando viene eliminato un server master, la replica viene arrestata per tutte le repliche in lettura. Le repliche in lettura diventano server autonomi che supportano sia la lettura che la scrittura.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sulle [repliche in lettura in Database di Azure per PostgreSQL](concepts-read-replicas.md).