---
title: Connettersi con Python - Database di Azure per PostgreSQL - Server singolo
description: Questa guida introduttiva contiene un esempio di codice Python che può essere usato per connettersi ai dati ed eseguire query da Database di Azure per PostgreSQL - Server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devcenter
ms.devlang: python
ms.topic: quickstart
ms.date: 11/07/2019
ms.openlocfilehash: f01ac67b25cbcc098973a3beb0a2b7b311827a37
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766396"
---
# <a name="use-python-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>Usare Python per connettersi ed eseguire query sui dati in Database di Azure per PostgreSQL - Server singolo
Questa Guida introduttiva illustra come usare Database di Azure per PostgreSQL con Python in macOS, Ubuntu Linux o Windows. La Guida introduttiva illustra come connettersi al database e usare istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati. L'articolo presuppone che l'utente abbia familiarità con Python, ma non con Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti
- Database di Azure per PostgreSQL - Server singolo, creato con i passaggi descritti in [Guida introduttiva: Creare un server Database di Azure per PostgreSQL nel portale di Azure](quickstart-create-server-database-portal.md) o [Guida introduttiva: Creare un Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](quickstart-create-server-database-azure-cli.md). 
  
- [Python](https://www.python.org/downloads/) 2.7.9 o 3.4 o versioni successive.
  
- Aggiornamento più recente al programma di installazione dei pacchetti [pip](https://pip.pypa.io/en/stable/installing/), installato tramite `pip install -U pip`. 

## <a name="install-the-python-libraries-for-postgresql"></a>Installare le librerie di Python per PostgreSQL
Il modulo [psycopg2](https://pypi.python.org/pypi/psycopg2/) consente la connessione e l'esecuzione di query su un database PostgreSQL ed è disponibile come pacchetto [wheel](https://pythonwheels.com/) di Linux, macOS o Windows. Installare la versione binaria del modulo, incluse tutte le dipendenze. Per altre informazioni sull'installazione e i requisiti di `psycopg2`, vedere la pagina relativa all'[installazione](http://initd.org/psycopg/docs/install.html). 

Per installare `psycopg2`, aprire un terminale o un prompt dei comandi ed eseguire il comando `pip install psycopg2`.

## <a name="get-database-connection-information"></a>Recuperare le informazioni di connessione al database SQL
Per la connessione a Database di Azure per PostgreSQL, sono necessari il nome completo del server e le credenziali di accesso. È possibile ottenere queste informazioni nel portale di Azure.

1. Nel [portale di Azure](https://portal.azure.com/), cercare il nome del server di Database di Azure per PostgreSQL e selezionarlo. 
1. Nella pagina **Panoramica** del server, copiare il valore dei campi **Nome server** (completo) e **Nome utente amministratore**. Il valore del campo **Nome server** (completo) è sempre nella forma *\<mio-nome-server>.postgres.database.azure.com*, mentre il valore del campo **Nome utente amministratore** è sempre nella forma *\<mio-nomeutente-amministratore>@\<mio-nome-server>* . 
   
   È necessaria anche la password dell'amministratore. Se la password è stata dimenticata, è possibile reimpostarla in questa pagina. 
   
   ![Nome del server Database di Azure per PostgreSQL](./media/connect-python/1-connection-string.png)

## <a name="how-to-run-the-python-examples"></a>Come eseguire gli esempi Python

Per ogni esempio di codice in questo articolo, eseguire queste operazioni.

1. Creare un nuovo file in un editor di testo. 
   
1. Aggiungere l'esempio di codice al file. Nel codice sostituire:
   - `<server-name>` e `<admin-username>` con i valori copiati dal portale di Azure.
   - `<admin-password>` con la password del server.
   - `<database-name>` con il nome dell'istanza di Database di Azure per PostgreSQL. Un database predefinito denominato *postgres* è stato creato automaticamente al momento della creazione del server. È possibile rinominare il database o creare un nuovo database usando i comandi SQL. 
   
1. Salvare il file nella cartella del progetto con un'estensione *. py*, ad esempio *postgres-insert.py*. Per Windows, verificare che la codifica UTF-8 sia selezionata quando si salva il file. 
   
1. Per eseguire il file, passare alla cartella del progetto in un'interfaccia della riga di comando e digitare `python` seguito dal nome file, ad esempio `python postgres-insert.py`.

## <a name="create-a-table-and-insert-data"></a>Creare una tabella e inserire i dati
L'esempio di codice seguente consente di connettersi al Database di Azure per PostgreSQL usando la funzione [psycopg2.connect](http://initd.org/psycopg/docs/connection.html) e di caricare i dati con un'istruzione **INSERT** di SQL. La funzione [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) esegue la query SL sul database. 

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
L'esempio di codice seguente si connette al Database di Azure per PostgreSQL e usa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **SELECT** di SQL per leggere i dati. La funzione accetta una query e restituisce un set di risultati su cui eseguire l'iterazione tramite [cursor.fetchall()](http://initd.org/psycopg/docs/cursor.html#cursor.fetchall). 

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
L'esempio di codice seguente si connette al Database di Azure per PostgreSQL e usa [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **UPDATE** di SQL per aggiornare i dati. 

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
L'esempio di codice seguente si connette al Database di Azure per PostgreSQL e USA [cursor.execute](http://initd.org/psycopg/docs/cursor.html#execute) con l'istruzione **DELETE** di SQL per eliminare un elemento di inventario inserito in precedenza. 

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
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
