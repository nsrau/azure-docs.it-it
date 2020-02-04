---
title: Usare Python per eseguire query su un database
description: Questo argomento illustra come usare Python per creare un programma che si connette a un database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768565"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Avvio rapido: Usare Python per eseguire query su un database SQL di Azure

Questo argomento di avvio rapido illustra come usare Python per connettersi a un database SQL di Azure e usare le istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisites

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un [database SQL di Azure](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 e software correlato

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Per installare Homebrew e Python, il driver ODBC, SQLCMD e il driver Python per SQL Server, seguire i passaggi **1.2**, **1.3** e **2.1** della procedura [Creare app Python con SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Per altre informazioni, vedere [Driver Microsoft ODBC in macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Per installare Python e altri pacchetti necessari, usare `sudo apt-get install python python-pip gcc g++ build-essential`.

  Per installare il driver ODBC, SQLCMD e il driver Python per SQL Server, vedere [Configurare un ambiente per lo sviluppo di pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Per altre informazioni, vedere [Driver Microsoft ODBC in Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Per installare Python, il driver ODBC, SQLCMD e il driver Python per SQL Server, vedere [Configurare un ambiente per lo sviluppo di pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Per altre informazioni, vedere [Driver Microsoft ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Gli script di questo articolo sono scritti in modo da usare il database **Adventure Works**.

> [!NOTE]
> È anche possibile scegliere di usare un'istanza gestita di Azure SQL.
>
> Per creare e configurare il progetto, usare il [portale di Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) o l'[interfaccia della riga di comando](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), quindi configurare la connettività [sul posto](sql-database-managed-instance-configure-p2s.md) o tramite [VM](sql-database-managed-instance-configure-vm.md).
>
> Per caricare i dati, vedere come eseguire il [ripristino con BACPAC](sql-database-import.md) con il file di [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) oppure come eseguire il [ripristino del database Wide World Importers](sql-database-managed-instance-get-started-restore.md).

Per esplorare ulteriormente Python e il database SQL di Azure, vedere [Librerie del database SQL di Azure per Python](/python/api/overview/azure/sql), il [repository di pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e un [esempio di pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

## <a name="create-code-to-query-your-sql-database"></a>Creare codice per eseguire query sul database SQL 

1. Nell'editor di testo, creare un nuovo file chiamato *sqltest.py*.  
   
1. Aggiungere il codice seguente. Sostituire con i propri valori per \<server>, \<database>, \<nome utente>, e \<password>.
   
   >[!IMPORTANT]
   >Il codice in questo esempio usa i dati di esempio di AdventureWorksLT, che è possibile scegliere come origine durante la creazione del database. Se il database contiene dati diversi, utilizzare le tabelle del database personale nella query SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi, eseguire il seguente comando:

   ```cmd
   python sqltest.py
   ```

1. Verificare che vengano restituite le prime 20 righe di categoria/prodotto, quindi chiudere la finestra di comando.

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Driver Microsoft Python per SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/?v=17.23h)

