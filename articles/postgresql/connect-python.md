---
title: 'Avvio rapido: Connettersi con Python - Database di Azure per PostgreSQL - Server singolo'
description: Questa guida introduttiva contiene un esempio di codice Python che può essere usato per connettersi ai dati ed eseguire query da Database di Azure per PostgreSQL - Server singolo.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 10/28/2020
ms.openlocfilehash: db94a82112f2670facd4d89178f11653c5316c36
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998941"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Avvio rapido: Usare Python per connettersi ed eseguire query sui dati in Database di Azure per PostgreSQL - Server singolo

Questa guida di avvio rapido illustra come connettersi al database nel server singolo di Database di Azure per PostgreSQL e immettere istruzioni SQL per eseguire query con Python in macOS, Ubuntu Linux o Windows.

> [!TIP]
> Per indicazioni su come creare un'applicazione Django con PostgreSQL, vedere l'esercitazione [Distribuire un'app Web Django con PostgreSQL](../app-service/tutorial-python-postgresql-app.md).


## <a name="prerequisites"></a>Prerequisiti
Per questa guida di avvio rapido, è necessario:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
- Creare un server singolo di Database di Azure per PostgreSQL usando il [portale di Azure](./quickstart-create-server-database-portal.md) <br/> o in alternativa l'[interfaccia della riga di comando di Azure](./quickstart-create-server-database-azure-cli.md).
- A seconda che si usi l'accesso pubblico o privato, completare **UNA** delle azioni seguenti per abilitare la connettività.

  |Action| Metodo di connettività|Guida pratica|
  |:--------- |:--------- |:--------- |
  | **Configurare le regole del firewall** | Pubblico | [Portale](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **Configurare l'endpoint di servizio** | Pubblico | [Portale](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **Configurare il collegamento privato** | Privato | [Portale](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Python](https://www.python.org/downloads/) 2.7.9 o 3.4 o versioni successive.

- Versione più recente del programma di installazione del pacchetto [pip](https://pip.pypa.io/en/stable/installing/).
- Installare [psycopg2](https://pypi.python.org/pypi/psycopg2/) usando `pip install psycopg2` in una finestra del terminale o del prompt dei comandi. Per altre informazioni, vedere la [procedura per installare `psycopg2`](http://initd.org/psycopg/docs/install.html).

## <a name="get-database-connection-information"></a>Recuperare le informazioni di connessione al database SQL
Per la connessione a Database di Azure per PostgreSQL, sono necessari il nome completo del server e le credenziali di accesso. È possibile ottenere queste informazioni nel portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com/), cercare il nome del server di Database di Azure per PostgreSQL e selezionarlo.
1. Nella pagina **Panoramica** del server, copiare il valore dei campi **Nome server** (completo) e **Nome utente amministratore**. Il valore completo di **Nome server** è sempre nel formato *\<my-server-name>.postgres.database.azure.com* e il valore di **Nome utente amministratore** è sempre nel formato *\<my-admin-username>@\<my-server-name>* .

   È necessaria anche la password dell'amministratore. Se la password è stata dimenticata, è possibile reimpostarla in questa pagina.

   :::image type="content" source="./media/connect-python/1-connection-string.png" alt-text="Nome del server Database di Azure per PostgreSQL":::

> [!IMPORTANT]
>  Sostituire i valori seguenti:
>   - `<server-name>` e `<admin-username>` con i valori copiati dal portale di Azure.
>   - `<admin-password>` con la password del server.
>   - `<database-name>` con il nome del database del server singolo di Database di Azure per PostreSQL. Un database predefinito denominato *postgres* è stato creato automaticamente contemporaneamente al server. È possibile rinominare questo database o [crearne uno nuovo](https://www.postgresql.org/docs/9.0/sql-createdatabase.html) usando i comandi di SQL.

## <a name="step-1-connect-and-insert-data"></a>Passaggio 1: Connettersi e inserire i dati
L'esempio di codice seguente si connette al database di Database di Azure per PostgreSQL usando
-  la funzione [psycopg2. Connect](http://initd.org/psycopg/docs/connection.html), quindi carica i dati con un'istruzione **INSERT** di SQL.
- La funzione [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) esegue la query SQL sul database.

```Python
import psycopg2

# Update connection string information
host = "<server-name>"
dbname = "<database-name>"
user = "<admin-username>"
password = "<admin-password>"
sslmode = "require"

# Construct connection string
conn_string = "host={0} user={1} dbname={2} password={3} sslmode={4}".format(host, user, dbname, password, sslmode)
conn = psycopg2.connect(conn_string)
print("Connection established")

cursor = conn.cursor()

# Drop previous table of same name if one exists
cursor.execute("DROP TABLE IF EXISTS inventory;")
print("Finished dropping table (if existed)")

# Create a table
cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
print("Finished creating table")

# Insert some data into the table
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
print("Inserted 3 rows of data")

# Clean up
conn.commit()
cursor.close()
conn.close()
```

Quando il codice viene eseguito in modo corretto, genera l'output seguente:

:::image type="content" source="media/connect-python/2-example-python-output.png" alt-text="Output della riga di comando":::


[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback)

## <a name="step-2-read-data"></a>Passaggio 2: Leggere i dati
L'esempio di codice seguente si connette al database di Database di Azure per PostgreSQL e usa
- la funzione [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **SELECT** di SQL per leggere i dati.
- La funzione accetta una query e restituisce un set di risultati su cui eseguire l'iterazione tramite [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall)

```Python

# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()

# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))


```
[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback)

## <a name="step-3-update-data"></a>Passaggio 3: Aggiornare i dati
L'esempio di codice seguente usa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **UPDATE** di SQL per aggiornare i dati.

```Python

# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")

```
[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback)

## <a name="step-5-delete-data"></a>Passaggio 5: Eliminare i dati
L'esempio di codice seguente esegue [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **DELETE** di SQL per eliminare una voce di inventario inserita in precedenza.

```Python

# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")

```

[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire tutte le risorse usate in questo argomento di avvio rapido, eliminare il gruppo di risorse con il comando seguente:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Gestire il server di Database di Azure per MySQL usando il portale](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gestire il server di Database di Azure per MySQL usando l'interfaccia della riga di comando](./how-to-manage-server-cli.md)<br/>

[Ci sono problemi a trovare le informazioni giuste? Segnalarli](https://aka.ms/postgres-doc-feedback).
