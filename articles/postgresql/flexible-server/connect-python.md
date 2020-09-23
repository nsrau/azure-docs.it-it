---
title: 'Avvio rapido: Connettersi con Python - Database di Azure per PostgreSQL - Server flessibile'
description: Questo argomento di avvio rapido include diversi esempi di codice Python che è possibile usare per connettersi ai dati ed eseguire query dal server flessibile di Database di Azure per PostgreSQL.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 89dc36a9b1b1fee9ad10d55945c7fc17bf72f476
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945029"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-postgresql---flexible-server"></a>Avvio rapido: Usare Python per connettersi ai dati ed eseguire query nel server flessibile di Database di Azure per PostgreSQL

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

In questo argomento di avvio rapido ci si connette a un server flessibile di Database di Azure per PostgreSQL tramite Python. È quindi possibile usare istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database dalle piattaforme Mac, Ubuntu Linux e Windows. 

Questo articolo presuppone che si abbia familiarità con lo sviluppo con Python, ma non con il server flessibile di Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un server flessibile di Database di Azure per PostgreSQL. Per creare un server flessibile, vedere [Creare un server flessibile di Database di Azure per PostgreSQL tramite il portale di Azure](./quickstart-create-server-portal.md).
* [Python](https://www.python.org/downloads/) 2.7.9 o 3.4 o versioni successive.
* Versione più recente del programma di installazione del pacchetto [pip](https://pip.pypa.io/en/stable/installing/).

## <a name="preparing-your-client-workstation"></a>Preparazione della workstation client
- Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile. Vedere [Creare e gestire la rete virtuale del server flessibile di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](./how-to-manage-virtual-network-cli.md).
- Se il server flessibile è stato creato con l'opzione *Accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere il proprio indirizzo IP locale all'elenco di regole del firewall nel server. Vedere [Creare e gestire le regole del firewall del server flessibile di Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure](./how-to-manage-firewall-cli.md).

## <a name="install-the-python-libraries-for-postgresql"></a>Installare le librerie di Python per PostgreSQL
Il modulo [psycopg2](https://pypi.python.org/pypi/psycopg2/) consente la connessione e l'esecuzione di query su un database PostgreSQL ed è disponibile come pacchetto [wheel](https://pythonwheels.com/) di Linux, macOS o Windows. Installare la versione binaria del modulo, incluse tutte le dipendenze. Per altre informazioni sull'installazione e i requisiti di `psycopg2`, vedere la pagina relativa all'[installazione](http://initd.org/psycopg/docs/install.html). 

Per installare `psycopg2`, aprire un terminale o un prompt dei comandi ed eseguire il comando `pip install psycopg2`.

## <a name="get-database-connection-information"></a>Recuperare le informazioni di connessione al database SQL
Per connettersi a un server flessibile di Database di Azure per PostgreSQL, è necessario avere il nome completo del server e le credenziali di accesso. È possibile ottenere queste informazioni nel portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com/) cercare e selezionare il nome del server flessibile. 
2. Nella pagina **Panoramica** del server, copiare il valore dei campi **Nome server** (completo) e **Nome utente amministratore**. Il valore completo di **Nome server** è sempre nel formato *\<my-server-name>.postgres.database.azure.com*.

   È necessaria anche la password dell'amministratore. Se la password è stata dimenticata, è possibile reimpostarla nella pagina di panoramica. 

   <!--![Azure Database for PostgreSQL server name](./media/connect-python/1-connection-string.png)-->

## <a name="how-to-run-the-python-examples"></a>Come eseguire gli esempi Python

Per ogni esempio di codice in questo articolo, eseguire queste operazioni.

1. Creare un nuovo file in un editor di testo. 

1. Aggiungere l'esempio di codice al file. Nel codice sostituire:
   - `<server-name>` e `<admin-username>` con i valori copiati dal portale di Azure.
   - `<admin-password>` con la password del server.
   - `<database-name>` con il nome del database del server flessibile di Database di Azure per PostgreSQL. Un database predefinito denominato *postgres* è stato creato automaticamente al momento della creazione del server. È possibile rinominare il database o creare un nuovo database usando i comandi SQL. 

1. Salvare il file nella cartella del progetto con un'estensione *. py*, ad esempio *postgres-insert.py*. Per Windows, verificare che la codifica UTF-8 sia selezionata quando si salva il file. 

1. Per eseguire il file, passare alla cartella del progetto in un'interfaccia della riga di comando e digitare `python` seguito dal nome file, ad esempio `python postgres-insert.py`.

## <a name="create-a-table-and-insert-data"></a>Creare una tabella e inserire i dati
L'esempio di codice seguente stabilisce una connessione al database del server flessibile di Database di Azure per PostgreSQL usando la funzione [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e carica i dati con un'istruzione **INSERT** di SQL. La funzione [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) esegue la query SL sul database. 

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

![Output della riga di comando](media/connect-python/2-example-python-output.png)

## <a name="read-data"></a>Leggere i dati
L'esempio di codice seguente stabilisce una connessione al database del server flessibile di Database di Azure per PostgreSQL e usa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **SELECT** di SQL per leggere i dati. La funzione accetta una query e restituisce un set di risultati su cui eseguire l'iterazione tramite [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
# Fetch all rows from table
cursor.execute("SELECT * FROM inventory;")
rows = cursor.fetchall()
# Print all rows
for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="update-data"></a>Aggiornare i dati
L'esempio di codice seguente stabilisce una connessione al database del server flessibile di Database di Azure per PostgreSQL e usa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **UPDATE** di SQL per aggiornare i dati. 

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
# Update a data row in the table
cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
print("Updated 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="delete-data"></a>Eliminare i dati
L'esempio di codice seguente stabilisce una connessione al database del server flessibile di Database di Azure per PostgreSQL e usa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **DELETE** di SQL per eliminare una voce di inventario inserita in precedenza. 

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
# Delete data row from table
cursor.execute("DELETE FROM inventory WHERE name = %s;", ("orange",))
print("Deleted 1 row of data")
# Cleanup
conn.commit()
cursor.close()
conn.close()
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando dump e ripristino](../howto-migrate-using-dump-and-restore.md)