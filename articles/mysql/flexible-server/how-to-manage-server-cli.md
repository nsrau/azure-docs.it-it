---
title: Gestire server-interfaccia della riga di comando di Azure-server flessibile per database di Azure per MySQL
description: Informazioni su come gestire un server di database di Azure per MySQL flessibile dall'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: 7701fe91d0e3f78f9596687bf945ba4b11c2d199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331693"
---
# <a name="manage-an-azure-database-for-mysql---flexible-server-preview-using-the-azure-cli"></a>Gestire un database di Azure per MySQL-server flessibile (anteprima) con l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo articolo illustra come gestire il server flessibile (anteprima) distribuito in Azure. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="prerequisites"></a>Prerequisiti
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. Per questo articolo è necessario eseguire in locale l'interfaccia della riga di comando di Azure versione 2.0 o successiva. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

È necessario accedere all'account con il comando [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login). Annotare la proprietà **id** che fa riferimento all'**ID sottoscrizione** per l'account Azure.

```azurecli-interactive
az login
```

Selezionare la sottoscrizione specifica nell'account tramite il comando [az account set](/cli/azure/account). Annotare il valore **id** dall'output **az login** da usare come valore per l'argomento **subscription** nel comando. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui verrà fatturata la risorsa. Per ottenere tutte le sottoscrizioni, usare [az account list](https://docs.microsoft.com/cli/azure/account#az-account-list).

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se non è ancora stato creato un server flessibile, crearne uno per iniziare a usare questa guida.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

È possibile aumentare facilmente il livello di calcolo, Vcore e archiviazione usando il comando seguente. È possibile visualizzare tutte le operazioni del server che è possibile eseguire [AZ MySQL flexible-Server Update](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update)

```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v4 --storage-size 6144
```

Ecco i dettagli per gli argomenti indicati:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server di Database di Azure per MySQL. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|Standard_D4ds_v4|Immettere il nome del livello di calcolo e le dimensioni. Segue la convenzione Standard_ {VM size} in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](../concepts-pricing-tiers.md).
storage-size | 6144 | Capacità di archiviazione del server (l'unità è MB). Il valore minimo 5120 e aumenta in 1024 incrementi.

> [!Important]
> - L'archiviazione può essere aumentata (Tuttavia, non è possibile ridimensionare lo spazio di archiviazione)


## <a name="manage-mysql-databases-on-a-server"></a>Gestire i database MySQL in un server.
È possibile usare uno di questi comandi per creare, eliminare, elencare e visualizzare le proprietà del database di un database nel server

| Cmdlet | Utilizzo| Descrizione |
| --- | ---| --- |
|[AZ MySQL flexible-server DB create](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_create)|```az mysql flexible-server db create -g myresourcegroup -s mydemoserver -n mydatabasename``` |Crea un database|
|[AZ MySQL flexible-server DB Delete](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_delete)|```az mysql flexible-server db delete -g myresourcegroup -s mydemoserver -n mydatabasename```|Eliminare il database dal server. Questo comando non elimina il server. |
|[AZ MySQL flexible-server DB List](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_list)|```az mysql flexible-server db list -g myresourcegroup -s mydemoserver```|Elenca tutti i database nel server|
|[AZ MySQL flexible-server DB Show](/cli/azure/mysql/flexible-server/db#az_mysql_flexible_server_db_show)|```az mysql flexible-server db show -g myresourcegroup -s mydemoserver -n mydatabasename```|Mostra altri dettagli del database|

## <a name="update-admin-password"></a>Aggiorna password amministratore
È possibile modificare la password del ruolo amministratore con questo comando
```azurecli-interactive
az mysql flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!Important]
>  Verificare che la password abbia una lunghezza minima di 8 caratteri e un massimo di 128 caratteri.
> La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici in lingua inglese.

## <a name="delete-a-server"></a>Eliminazione di un server
Se si desidera eliminare il server MySQL flessibile, è possibile eseguire il comando [AZ MySQL flexible-server server delete](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_delete) .

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi
- [Informazioni su come avviare o arrestare un server](how-to-stop-start-server-portal.md)
- [Informazioni su come gestire una rete virtuale](how-to-manage-virtual-network-cli.md)
- [Risolvere i problemi di connessione](how-to-troubleshoot-common-connection-issues.md)
- [Creare e gestire un firewall](how-to-manage-firewall-cli.md)
