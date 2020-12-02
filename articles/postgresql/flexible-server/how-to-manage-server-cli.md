---
title: Gestire server-interfaccia della riga di comando di Azure-database di Azure per PostgreSQL-server flessibile
description: Informazioni su come gestire un server di database di Azure per PostgreSQL-flessibile dall'interfaccia della riga di comando di Azure.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: 278f8f816909a7e365d7e45d04c5169950e79a65
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493679"
---
# <a name="manage-an-azure-database-for-postgresql---flexible-server-by-using-the-azure-cli"></a>Gestire un database di Azure per PostgreSQL-server flessibile usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> Database di Azure per PostgreSQL: il server flessibile è in versione di anteprima.

Questo articolo illustra come gestire il server flessibile distribuito in Azure. Le attività di gestione includono il ridimensionamento di calcolo e archiviazione, la reimpostazione della password amministratore e la visualizzazione dei dettagli del server.

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare. 

È necessario eseguire l'interfaccia della riga di comando di Azure versione 2,0 o successiva localmente. Per vedere la versione installata, eseguire il comando `az --version`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

Accedere al proprio account con il comando [AZ login](/cli/azure/reference-index#az-login) . 

```azurecli-interactive
az login
```

Per selezionare la sottoscrizione, usare il comando [AZ account set](/cli/azure/account) . Prendere nota del valore **ID** dell'output **AZ login** da usare come valore per l'argomento **Subscription** nel comando seguente. Se si dispone di più sottoscrizioni, scegliere la sottoscrizione a cui deve essere addebitata la risorsa. Per identificare tutte le sottoscrizioni, usare il comando [AZ account list](/cli/azure/account#az-account-list) .

```azurecli
az account set --subscription <subscription id>
```

> [!Important]
> Se non è ancora stato creato un server flessibile, è necessario eseguire questa operazione per seguire questa guida.

## <a name="scale-compute-and-storage"></a>Ridimensionare le risorse di calcolo e archiviazione

È possibile scalare facilmente il livello di calcolo, Vcore e l'archiviazione usando il comando seguente. Per un elenco di tutte le operazioni server che è possibile eseguire, vedere la panoramica [AZ Postgres flexible server](/cli/azure/postgres/flexible-server) .

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --sku-name Standard_D4ds_v3 --storage-size 6144
```

Di seguito sono riportati i dettagli per gli argomenti del codice precedente:

**Impostazione** | **Valore di esempio** | **Descrizione**
---|---|---
name | mydemoserver | Immettere un nome univoco per il server. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) Deve contenere da 3 a 63 caratteri.
resource-group | myresourcegroup | Specificare il nome del gruppo di risorse di Azure.
sku-name|Standard_D4ds_v3|Immettere il nome del livello di calcolo e le dimensioni. Il valore segue la convenzione *Standard_ {VM size}* in sintassi abbreviata. Per altre informazioni, vedere i [piani tariffari](../concepts-pricing-tiers.md).
storage-size | 6144 | Immettere la capacità di archiviazione del server in megabyte. Il valore minimo è 5120, aumentando con incrementi di 1024.

> [!IMPORTANT]
> Non è possibile ridimensionare l'archiviazione. 

## <a name="manage-postgresql-databases-on-a-server"></a>Gestire database PostgreSQL in un server

Sono diverse le applicazioni che è possibile usare per connettere il database di Azure per il server PostgreSQL. Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [PSQL](https://www.postgresql.org/docs/current/static/app-psql.html). A questo punto, è possibile usare lo strumento da riga di comando psql per connettersi al database di Azure per il server PostgreSQL.

1. Eseguire il comando **PSQL** seguente:

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **Postgres** nel server PostgreSQL **mydemoserver.postgres.database.Azure.com** tramite le credenziali di accesso. Quando viene richiesto, immettere il `<server_admin_password>` scelto.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Dopo la connessione, lo strumento PSQL Visualizza un prompt **Postgres** in cui è possibile immettere i comandi SQL. Se la versione di psql in uso è diversa dalla versione del database di Azure per il server PostgreSQL, verrà visualizzato un avviso nell'output della connessione iniziale.

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
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Specificare le opzioni SSL e riprovare ".
   >
   > Verificare che l'indirizzo IP del client sia consentito nelle regole del firewall.

2. Creare un database vuoto denominato **postgresdb** digitando il comando seguente al prompt:

    ```bash
    CREATE DATABASE postgresdb;
    ```

3. Al prompt, eseguire il comando seguente per cambiare le connessioni al database appena creato **postgresdb**:

    ```bash
    \c postgresdb
    ```

4. Digitare  `\q` e premere INVIO per uscire da PSQL.

In questa sezione è stata effettuata la connessione al database di Azure per il server PostgreSQL tramite psql e è stato creato un database utente vuoto.

## <a name="reset-the-admin-password"></a>Reimpostare la password amministratore

È possibile modificare la password del ruolo di amministratore con il comando seguente:

```azurecli-interactive
az postgres flexible-server update --resource-group myresourcegroup --name mydemoserver --admin-password <new-password>
```

> [!IMPORTANT]
> Scegliere una password con un minimo di 8 caratteri e un massimo di 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: 
> - Lettere maiuscole
> - Lettere minuscole
> - Numeri
> - Caratteri non alfanumerici

## <a name="delete-a-server"></a>Eliminazione di un server

Per eliminare il server flessibile database di Azure per PostgreSQL, eseguire il comando [AZ Postgres flexible-server delete](/cli/azure/postgres/flexible-server#az-PostgreSQL-flexible-server-delete) .

```azurecli-interactive
az postgres flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui concetti di backup e ripristino](concepts-backup-restore.md)
- [Ottimizzazione e monitoraggio del server](concepts-monitoring.md)