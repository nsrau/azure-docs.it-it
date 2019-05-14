---
title: Usare Python per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare Python per creare un programma che si connette a un database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/25/2019
ms.openlocfilehash: 788e21dc5d866bdd6641349ceeeeacbfab98c25c
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58444973"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Avvio rapido: Usare Python per eseguire query su un database SQL di Azure

 Questa guida introduttiva illustra come usare [Python](https://python.org) per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati. Per altri dettagli sull'SDK, vedere la documentazione di [riferimento](https://docs.microsoft.com/python/api/overview/azure/sql), un [esempio pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e il [repository GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

- un database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  || Database singolo | Istanza gestita |
  |:--- |:--- |:---|
  | Create| [Portale](sql-database-single-database-get-started.md) | [Portale](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurare | [Regola del firewall per gli indirizzi IP a livello di server](sql-database-server-level-firewall-rule.md)| [Connettività da una VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connettività da locale](sql-database-managed-instance-configure-p2s.md)
  |Caricare i dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Ripristinare o importare Adventure Works dal file [BACPAC](sql-database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Gli script in questo articolo sono scritti in modo da usare il database Adventure Works. Con un'istanza gestita, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo in modo da usare il database Wide World Importers.
  
- Software correlato e Python per il sistema operativo in uso:
  
  - **MacOS**: installare Homebrew e Python, installare il driver ODBC e SQLCMD e quindi installare il driver Python per SQL Server. Vedere i passaggi 1.2, 1.3 e 2.1 in [Creare app Python con SQL Server in MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Per altre informazioni, vedere [Installare il Driver ODBC Microsoft su Linux e MacOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  - **Ubuntu**: Installare Python e altri pacchetti necessari con `sudo apt-get install python python-pip gcc g++ build-essential`. Scaricare e installare il driver ODBC, SQLCMD e il driver Python per SQL Server. Per istruzioni, vedere [Configurare un ambiente di sviluppo per lo sviluppo Python con pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  - **Windows**: Installare Python, il driver ODBC, SQLCMD e il driver Python per SQL Server. Per istruzioni, vedere [Configurare un ambiente di sviluppo per lo sviluppo Python con pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

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

