---
title: Gestire server-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL
description: Informazioni su come gestire un server di database di Azure per PostgreSQL dall'interfaccia della riga di comando di Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 9/22/2020
ms.openlocfilehash: d99634388b9c4db99c996cfccb9bb5f12682f217
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490119"
---
# <a name="manage-an-azure-database-for-postgresql-single-server-using-the-azure-cli"></a>Gestire un server singolo di database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure

Questo articolo illustra come gestire i singoli server distribuiti in Azure. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](/cli/azure/reference-index#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

Se non è ancora stato creato un server, fare riferimento a questa [Guida introduttiva](quickstart-create-server-database-azure-cli.md) per crearne uno.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

È possibile aumentare facilmente il piano tariffario, il calcolo e l'archiviazione usando il comando seguente. È possibile visualizzare tutte le operazioni del server che è possibile eseguire [AZ Postgres Server Overview](/cli/azure/mysql/server)

```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --sku-name GP_Gen5_4 --storage-size 6144
```

Ecco i dettagli per gli argomenti indicati:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il database di Azure per il server PostgreSQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|GP_Gen5_2|Immettere il nome del piano tariffario e della configurazione delle risorse di calcolo. Segue la convenzione {piano tariffario} _{generazione di calcolo}_ {Vcore} in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](./concepts-pricing-tiers.md).
storage-size | 6144 | Capacità di archiviazione del server (l'unità è MB). Il valore minimo 5120 e aumenta in 1024 incrementi.

> [!Important]
> - L'archiviazione può essere aumentata (Tuttavia, non è possibile ridimensionare lo spazio di archiviazione)
> - La scalabilità verticale dal piano tariffario Basic a quello per utilizzo generico o con ottimizzazione per la memoria non è supportata. È possibile eseguire manualmente la scalabilità verticale con  [uno script bash](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404) o [usando PostgreSQL Workbench](https://techcommunity.microsoft.com/t5/azure-database-support-blog/how-to-scale-up-azure-database-for-mysql-from-basic-tier-to/ba-p/369134)


## <a name="manage-postgresql-databases-on-a-server"></a>Gestire i database PostgreSQL in un server.
È possibile usare uno di questi comandi per creare, eliminare, elencare e visualizzare le proprietà del database di un database nel server

| Cmdlet | Utilizzo| Descrizione |
| --- | ---| --- |
|[AZ Postgres DB create](/cli/azure/sql/db#az-mysql-db-create)|```az postgres db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Crea un database|
|[AZ Postgres DB Delete](/cli/azure/sql/db#az-mysql-db-delete)|```az postgres db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Eliminare il database dal server. Questo comando non elimina il server. |
|[elenco AZ Postgres DB](/cli/azure/sql/db#az-mysql-db-list)|```az postgres db list -g myresourcegroup -s mydemoserver```|Elenca tutti i database nel server|
|[AZ Postgres DB Show](/cli/azure/sql/db#az-mysql-db-show)|```az postgres db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra altri dettagli del database|

## <a name="update-admin-password"></a>Aggiorna password amministratore
È possibile modificare la password del ruolo amministratore con questo comando
```azurecli-interactive
az postgres server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Verificare che la password abbia una lunghezza minima di 8 caratteri e un massimo di 128 caratteri.
> La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici in lingua inglese.

## <a name="delete-a-server"></a>Eliminazione di un server
Se si vuole solo eliminare il server PostgreSQL singolo, è possibile eseguire il comando [AZ Postgres server delete](/cli/azure/mysql/server#az-mysql-server-delete) .

```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- [Riavviare un server](howto-restart-server-cli.md)
- [Ripristinare un server in uno stato non valido](howto-restore-server-cli.md)
- [Monitorare e ottimizzare il server](concepts-monitoring.md)