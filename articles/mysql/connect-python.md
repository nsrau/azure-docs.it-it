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
ms.date: 10/28/2020
ms.openlocfilehash: 12452367de0e8f936d30387df709d5d2779bfcb1
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427633"
---
# <a name="quickstart-use-python-to-connect-and-query-data-in-azure-database-for-mysql"></a>Avvio rapido: Usare Python per connettersi ai dati ed eseguire query in Database di Azure per MySQL

In questo argomento di avvio rapido ci si connette a un'istanza di Database di Azure per MySQL usando Python. È quindi possibile usare istruzioni SQL per eseguire query, inserire, aggiornare ed eliminare dati nel database dalle piattaforme Mac, Ubuntu Linux e Windows. 

## <a name="prerequisites"></a>Prerequisiti
Per questa guida di avvio rapido, è necessario:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
- Creare un server singolo del database di Azure per MySQL usando il [portale di Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> o in alternativa l'[interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md).
- A seconda che si usi l'accesso pubblico o privato, completare **UNA** delle azioni seguenti per abilitare la connettività.

   |Action| Metodo di connettività|Guida pratica|
   |:--------- |:--------- |:--------- |
   | **Configurare le regole del firewall** | Pubblico | [Portale](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
   | **Configurare l'endpoint di servizio** | Pubblico | [Portale](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
   | **Configurare il collegamento privato** | Privato | [Portale](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Creare un database e un utente non amministratore](./howto-create-users.md)

## <a name="install-python-and-the-mysql-connector"></a>Installare Python e il connettore MySQL

Installare Python e il connettore MySQL per Python nel computer seguendo questa procedura: 

> [!NOTE]
> Questo argomento di avvio rapido usa la [Guida dello sviluppatore per il connettore MySQL/Python](https://dev.mysql.com/doc/connector-python/en/).

1. Scaricare e installare [Python 3.7 o versione successiva](https://www.python.org/downloads/) per il sistema operativo in uso. Assicurarsi di aggiungere Python a `PATH`, perché è un requisito del connettore MySQL.
   
2. Aprire un prompt dei comandi o una shell `bash` e controllare la versione di Python eseguendo `python -V` con l'opzione V in maiuscolo.
   
3. Il programma di installazione del pacchetto `pip` è incluso nelle ultime versioni di Python. Aggiornare `pip` alla versione più recente eseguendo`pip install -U pip`. 
   
   Se `pip` non è installato, è possibile scaricarlo e installarlo con `get-pip.py`. Per altre informazioni, vedere [Installazione](https://pip.pypa.io/en/stable/installing/). 
   
4. Usare `pip` per installare il connettore MySQL per Python e le relative dipendenze:
   
   ```bash
   pip install mysql-connector-python
   ```
   
[Problemi? Segnalarli](https://aka.ms/mysql-doc-feedback)

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL dal portale di Azure. È necessario avere il nome del server, il nome del database e le credenziali di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).
   
1. Sulla barra di ricerca del portale cercare e selezionare il server di database di Azure per MySQL creato, ad esempio **mydemoserver**.
   
   :::image type="content" source="./media/connect-python/1_server-overview-name-login.png" alt-text="Nome del server del database di Azure per MySQL":::
   
1. Nella pagina **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questa pagina è anche possibile reimpostarla.
   
   :::image type="content" source="./media/connect-python/azure-database-for-mysql-server-overview-name-login.png" alt-text="Nome del server del database di Azure per MySQL 2":::

## <a name="step-1-create-a-table-and-insert-data"></a>Passaggio 1: Creare una tabella e inserire i dati

Usare il codice seguente per connettersi al server e al database, creare una tabella e caricare i dati usando un'istruzione SQL **INSERT**. Il codice importa la libreria mysql.connector e usa il metodo:
- funzione [connect()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysql-connector-connect.html) per connettersi a Database di Azure per MySQL usando gli [argomenti](https://dev.mysql.com/doc/connector-python/en/connector-python-connectargs.html) presenti nella raccolta di configurazione. 
- il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) esegue la query SQL sul database MySQL. 
- [cursor.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-close.html) al termine dell'utilizzo di un cursore.
- [conn.close()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlconnection-close.html) per chiudere la connessione.

> [!IMPORTANT]
> - SSL è abilitato per impostazione predefinita. Potrebbe essere necessario scaricare il [certificato SSL DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) per connettersi dall'ambiente locale.
> - Sostituire i segnaposto `<mydemoserver>`, `<myadmin>`, `<mypassword>` e `<mydatabase>` con i valori relativi al server e al database MySQL.

```python
import mysql.connector
from mysql.connector import errorcode

# Obtain connection string information from the portal
config = {
  'host':'<mydemoserver>.mysql.database.azure.com',
  'user':'<myadmin>@<mydemoserver>',
  'password':'<mypassword>',
  'database':'<mydatabase>',
  'client_flags': [ClientFlag.SSL],
  'ssl_cert': '/var/wwww/html/DigiCertGlobalRootG2.crt.pem'
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

[Problemi? Segnalarli](https://aka.ms/mysql-doc-feedback)

## <a name="step-2-read-data"></a>Passaggio 2: Leggere i dati

Usare il codice seguente per connettersi e leggere i dati usando un'istruzione SQL **SELECT**. Il codice importa la libreria mysql.connector e usa il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) per eseguire la query SQL sul database MySQL. 

Il codice legge le righe di dati usando il metodo [fetchall()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-fetchall.html), mantiene il set di risultati in una riga della raccolta e usa un iteratore `for` per scorrere le righe in un ciclo.

```python
  # Read data
  cursor.execute("SELECT * FROM inventory;")
  rows = cursor.fetchall()
  print("Read",cursor.rowcount,"row(s) of data.")

  # Print all rows
  for row in rows:
    print("Data row = (%s, %s, %s)" %(str(row[0]), str(row[1]), str(row[2])))

```

## <a name="step-3-update-data"></a>Passaggio 3: Aggiornare i dati

Usare il codice seguente per connettersi e aggiornare i dati usando un'istruzione SQL **UPDATE**. Il codice importa la libreria mysql.connector e usa il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) per eseguire la query SQL sul database MySQL. 

```python
  # Update a data row in the table
  cursor.execute("UPDATE inventory SET quantity = %s WHERE name = %s;", (200, "banana"))
  print("Updated",cursor.rowcount,"row(s) of data.")
```

## <a name="step-4-delete-data"></a>Passaggio 4: Eliminare i dati

Usare il codice seguente per connettersi e rimuovere i dati usando un'istruzione SQL **DELETE**. Il codice importa la libreria mysql.connector e usa il metodo [cursor.execute()](https://dev.mysql.com/doc/connector-python/en/connector-python-api-mysqlcursor-execute.html) per eseguire la query SQL sul database MySQL. 

```python

  # Delete a data row in the table
  cursor.execute("DELETE FROM inventory WHERE name=%(param1)s;", {'param1':"orange"})
  print("Deleted",cursor.rowcount,"row(s) of data.")
```

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
> [Gestire il server di Database di Azure per MySQL usando l'interfaccia della riga di comando](./how-to-manage-single-server-cli.md)

[Ci sono problemi a trovare le informazioni giuste? Segnalarli](https://aka.ms/mysql-doc-feedback).
