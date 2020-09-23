---
title: 'Avvio rapido: Connettersi con Python - Database di Azure per MySQL'
description: Questa guida introduttiva fornisce diversi esempi di codice Python che è possibile usare per connettersi ai dati ed eseguire query da Database di Azure per MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.devlang: python
ms.topic: quickstart
ms.date: 5/26/2020
ms.openlocfilehash: aad160f8a4ec96a1e0e627684c598062079b57e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90896304"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Avvio rapido: Usare Python per connettersi ai dati ed eseguire query in Database di Azure per MySQL

In questo argomento di avvio rapido ci si connette a un'istanza di Database di Azure per MySQL usando Python. È quindi possibile usare istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database dalle piattaforme Mac, Ubuntu Linux e Windows. 

Questo argomento presuppone che si abbia familiarità con lo sviluppo con Python, ma non con Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un'istanza di Database di Azure per il server MySQL. [Creare un database di Azure per il server MySQL tramite il portale di Azure](quickstart-create-mysql-server-database-using-azure-portal.md) o [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](quickstart-create-mysql-server-database-using-azure-cli.md).

> [!IMPORTANT] 
> Verificare che l'indirizzo IP da cui ci si sta connettendo sia stato aggiunto alle regole del firewall del server usando il [portale di Azure](./howto-manage-firewall-using-portal.md) o l’[interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)

## <a name="install-python-and-the-mysql-connector"></a>Installare Python e il connettore MySQL

Installare Python e il connettore MySQL per Python nel computer seguendo questa procedura: 

> [!NOTE]
> Questo argomento di avvio rapido usa un approccio basato su una query SQL non elaborata per connettersi a MySQL. Se si usa un framework Web, usare il connettore consigliato, ad esempio [mysqlclient](https://pypi.org/project/mysqlclient/) per Django.

1. Scaricare e installare [Python 3.7 o versione successiva](https://www.python.org/downloads/) per il sistema operativo in uso. Assicurarsi di aggiungere Python a `PATH`, perché è un requisito del connettore MySQL.
   
1. Aprire un prompt dei comandi o una shell `bash` e controllare la versione di Python eseguendo `python -V` con l'opzione V in maiuscolo.
   
1. Il programma di installazione del pacchetto `pip` è incluso nelle ultime versioni di Python. Aggiornare `pip` alla versione più recente eseguendo`pip install -U pip`. 
   
   Se `pip` non è installato, è possibile scaricarlo e installarlo con `get-pip.py`. Per altre informazioni, vedere [Installazione](https://pip.pypa.io/en/stable/installing/). 
   
1. Usare `pip` per installare il connettore MySQL per Python e le relative dipendenze:
   
   ```bash
   pip install mysql-connector-python
   ```
   
   È anche possibile installare il connettore Python per MySQL da [mysql.com](https://dev.mysql.com/downloads/connector/python/). Per altre informazioni sul connettore MySQL per Python, vedere [Guida dello sviluppatore per il connettore MySQL/Python](https://dev.mysql.com/doc/connector-python/en/). 

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL dal portale di Azure. È necessario avere il nome del server, il nome del database e le credenziali di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).
   
1. Sulla barra di ricerca del portale cercare e selezionare il server di database di Azure per MySQL creato, ad esempio **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nome del server del database di Azure per MySQL":::
   
1. Nella pagina **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questa pagina è anche possibile reimpostarla.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Nome del server del database di Azure per MySQL":::

## <a name="run-the-python-examples"></a>Eseguire gli esempi di Python

Per ogni esempio di codice in questo articolo, eseguire queste operazioni.

1. Creare un nuovo file in un editor di testo.
1. Aggiungere l'esempio di codice al file. Nel codice sostituire i segnaposto `<mydemoserver>`, `<myadmin>`, `<mypassword>` e `<mydatabase>` con i valori relativi al server e al database MySQL.
1. Salvare il file in una cartella del progetto con estensione *py*, ad esempio *C:\pythonmysql\createtable.py* o */home/username/pythonmysql/createtable.py*.
1. Per eseguire il codice, aprire un prompt dei comandi o una shell `cd pythonmysql` e passare alla cartella del progetto, ad esempio `bash`. Digitare il comando `python` seguito dal nome file, ad esempio `python createtable.py`, quindi premere INVIO. 
   
   > [!NOTE]
   > In Windows, se *python.exe* non viene trovato, potrebbe essere necessario aggiungere il percorso di Python nella variabile di ambiente PATH oppure specificare il percorso completo di *python.exe*, ad esempio `C:\python27\python.exe createtable.py`.

## <a name="create-a-table-and-insert-data"></a>Creare una tabella e inserire i dati

Usare il codice seguente per connettersi al server e al database, creare una tabella e caricare i dati usando un'istruzione SQL **INSERT**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al database di Azure per MySQL usando gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Drop previous table of same name if one exists
  cursor.execute("DROP TABLE IF EXISTS inventory;")
  print("Finished dropping table (if existed).")

  # Create table
  cursor.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);")
  print("Finished creating table.")

  # Insert some data into table
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("banana", 150))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("orange", 154))
  print("Inserted",cursor.rowcount,"row(s) of data.")
  cursor.execute("INSERT INTO inventory (name, quantity) VALUES (%s, %s);", ("apple", 100))
  print("Inserted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="read-data"></a>Leggere i dati

Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al database di Azure per MySQL usando gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

Il codice legge le righe di dati usando il metodo [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), mantiene il set di risultati in una riga della raccolta e usa un iteratore `for` per scorrere le righe in un ciclo.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="update-data"></a>Aggiornare i dati

Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al database di Azure per MySQL usando gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="delete-data"></a>Eliminare i dati

Usare il codice seguente per connettersi e rimuovere i dati usando un'istruzione SQL **DELETE**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al database di Azure per MySQL usando gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>'
}

# Construct connection string
try:
   conn = mysql.connector.connect(**config)
   print("Connection established.")
except mysql.connector.Error as err:
  if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
    print("Something is wrong with the user name or password.")
  elif err.errno == errorcode.ER_BAD_DB_ERROR:
    print("Database does not exist.")
  else:
    print(err)
else:
  cursor = conn.cursor()

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")

  # Cleanup
  conn.commit()
  cursor.close()
  conn.close()
  print("Done.")
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./concepts-migrate-import-export.md)
