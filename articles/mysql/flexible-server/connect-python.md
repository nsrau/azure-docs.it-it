---
title: 'Avvio rapido: Connettersi usando Python - Database di Azure per MySQL - Server flessibile'
description: Questo argomento di avvio rapido fornisce diversi esempi di codice Python che è possibile usare per connettersi ai dati ed eseguire query dal server flessibile di Database di Azure per MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: ff14bb1daeef6fc54ee5d11632ad98a29db2a172
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944195"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server"></a>Avvio rapido: Usare Python per connettersi ai dati ed eseguire query nel server flessibile di Database di Azure per MySQL

> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

In questo argomento di avvio rapido ci si connette a un server flessibile di Database di Azure per MySQL tramite Python. È quindi possibile usare istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database dalle piattaforme Mac, Ubuntu Linux e Windows. 

Questo articolo presuppone che si abbia familiarità con lo sviluppo con Python, ma non con il server flessibile di Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Un server flessibile di Database di Azure per MySQL. Per creare un server flessibile, vedere [Creare un server flessibile di Database di Azure per MySQL tramite il portale di Azure](./quickstart-create-server-portal.md) o [Creare un server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-server-cli.md).

## <a name="preparing-your-client-workstation"></a>Preparazione della workstation client
- Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile. Vedere [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](./how-to-manage-virtual-network-cli.md).
- Se il server flessibile è stato creato con l'opzione *Accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere il proprio indirizzo IP locale all'elenco di regole del firewall nel server. Vedere [Creare e gestire le regole del firewall del server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](./how-to-manage-firewall-cli.md).

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

Ottenere le informazioni di connessione necessarie per connettersi al server flessibile di Database di Azure per MySQL dal portale di Azure. È necessario avere il nome del server, il nome del database e le credenziali di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).
   
1. Sulla barra di ricerca del portale cercare e selezionare il server flessibile di Database di Azure per MySQL, ad esempio **mydemoserver**.
   
   <!---:::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->
   
1. Nella pagina **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questa pagina è anche possibile reimpostarla.
   
   <!---:::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::-->

## <a name="code-samples"></a>Esempi di codice

### <a name="run-below-mentioned-python-code-samples"></a>Eseguire gli esempi di codice Python seguenti
Per ogni esempio di codice in questo articolo, eseguire queste operazioni.

1. Creare un nuovo file in un editor di testo.
1. Aggiungere l'esempio di codice al file. Nel codice sostituire i segnaposto `<mydemoserver>`, `<myadmin>`, `<mypassword>` e `<mydatabase>` con i valori relativi al server e al database MySQL.
1. Salvare il file in una cartella del progetto con estensione *py*, ad esempio *C:\pythonmysql\createtable.py* o */home/username/pythonmysql/createtable.py*.
1. Per eseguire il codice, aprire un prompt dei comandi o una shell `cd pythonmysql` e passare alla cartella del progetto, ad esempio `bash`. Digitare il comando `python` seguito dal nome file, ad esempio `python createtable.py`, quindi premere INVIO. 
   
   > [!NOTE]
   > In Windows, se *python.exe* non viene trovato, potrebbe essere necessario aggiungere il percorso di Python nella variabile di ambiente PATH oppure specificare il percorso completo di *python.exe*, ad esempio `C:\python27\python.exe createtable.py`.

### <a name="create-a-table-and-insert-data"></a>Creare una tabella e inserire i dati

Usare il codice seguente per connettersi al server e al database, creare una tabella e caricare i dati usando un'istruzione SQL **INSERT**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al server flessibile con gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="read-data"></a>Leggere i dati

Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al server flessibile con gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

Il codice legge le righe di dati usando il metodo [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), mantiene il set di risultati in una riga della raccolta e usa un iteratore `for` per scorrere le righe in un ciclo.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="update-data"></a>Aggiornare i dati

Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al server flessibile con gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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

### <a name="delete-data"></a>Eliminare i dati

Usare il codice seguente per connettersi e rimuovere i dati usando un'istruzione SQL **DELETE**. 

Il codice importa la libreria mysql.connector e usa la funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi al server flessibile con gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. Il codice usa un cursore per la connessione, mentre il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>',
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
- [Connettività crittografata con Transport Layer Security (TLS 1.2) nel server flessibile di Database di Azure per MySQL](./how-to-connect-tls-ssl.md).
- Vedere le informazioni sulle [funzionalità di rete nel server flessibile di Database di Azure per MySQL](./concepts-networking.md).
- [Creare e gestire le regole del firewall del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-firewall-portal.md).
- [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-virtual-network-portal.md).
