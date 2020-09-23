---
title: Gestire server-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server flessibile
description: Informazioni su come gestire un server di database di Azure per PostgreSQL-flessibile dall'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 8f078c2c7b22a27c1fd87030361fc56d4495177c
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90940731"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-using-the-azure-cli"></a>Gestire un database di Azure per PostgreSQL-server flessibile usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Questo articolo illustra come gestire il server flessibile distribuito in Azure. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

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

È possibile aumentare facilmente il livello di calcolo, Vcore e archiviazione usando il comando seguente. È possibile visualizzare tutte le operazioni del server che è possibile eseguire [AZ Postgres flexible-Server Server Overview](/cli/azure/PostgreSQL/server)

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Ecco i dettagli per gli argomenti indicati:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|Standard_D4ds_v3|Immettere il nome del livello di calcolo e le dimensioni. Segue la convenzione Standard_ {VM size} in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](../concepts-pricing-tiers.md).
storage-size | 6144 | Capacità di archiviazione del server (l'unità è MB). Il valore minimo 5120 e aumenta in 1024 incrementi.

> [!IMPORTANT]
> Non è possibile ridimensionare l'archiviazione. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gestire database PostgreSQL in un server

Sono diverse le applicazioni che è possibile usare per connettere il database di Azure per il server PostgreSQL. Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/current/static/app-psql.html) per connettersi a un server PostgreSQL Azure. È ora possibile usare l'utilità della riga di comando psql per connettersi al server PostgreSQL Azure.

1. Eseguire il comando psql seguente per connettersi a un database di Azure per il server PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **postgres** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Immettere il valore di `<server_admin_password>` scelto quando viene chiesta la password.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Dopo che è stata stabilita la connessione, l'utilità psql visualizza un prompt postgres in cui è possibile digitare comandi sql. Nell'output della connessione iniziale potrebbe essere visualizzato un avviso perché la versione di psql in uso potrebbe essere diversa dalla versione del server Database di Azure per PostgreSQL.

   Output di psql esempio:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se il firewall non è configurato per accettare l'indirizzo IP del client, verrà visualizzato l'errore seguente:
   >
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Specify SSL options and retry.
   >
   > Verificare che l'IP del cliente sia consentito nella fase precedente delle regole del firewall.

2. Creare un database vuoto denominato "postgresdb" al prompt digitando il comando seguente:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Al prompt, eseguire il comando seguente per passare le connessioni al database appena creato **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Digitare `\q` e quindi premere INVIO per uscire da psql.

Ora che si è connessi al server Database di Azure per PostgreSQL tramite psql e si è creato un database utente vuoto,

## <a name="reset-admin-password"></a>Reimposta password amministratore
È possibile modificare la password del ruolo amministratore con questo comando
```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Verificare che la password abbia una lunghezza minima di 8 caratteri e un massimo di 128 caratteri.
> La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri e caratteri non alfanumerici in lingua inglese.

## <a name="delete-a-server"></a>Eliminazione di un server

Se si vuole solo eliminare il server PostgreSQL flessibile, è possibile eseguire il comando [AZ Postgres flexible-server delete](/cli/azure/PostgreSQL/server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti di backup e ripristino](concepts-backup-restore.md)
- [Ottimizzazione e monitoraggio del server](concepts-monitoring.md)
