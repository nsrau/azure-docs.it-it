---
title: Usare Python per eseguire query su un database
description: Questo argomento illustra come usare Python per creare un programma che si connette a un database nel database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, tracking-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 34d98905c9f068944ca9c149afa8fafa28a4cb68
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515088"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Avvio rapido: Usare Python per eseguire query su un database nel database SQL di Azure o nell'istanza gestita di SQL di Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questa guida di avvio rapido illustra come usare Python per connettersi a un database SQL di Azure o a un'istanza gestita di SQL di Azure e usare le istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | Azione | Database SQL | Istanza gestita di SQL | SQL Server in una macchina virtuale Azure |
  |:--- |:--- |:---|:---|
  | Create| [Portale](single-database-create-quickstart.md) | [Portale](../managed-instance/instance-create-quickstart.md) | [Portale](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurare | [Regola del firewall IP a livello di server](firewall-create-server-level-portal-quickstart.md)| [Connettività da una VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connettività dall'ambiente locale](../managed-instance/point-to-site-p2s-configure.md) | [Connettersi a un'istanza di SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Caricare dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Ripristinare Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Ripristinare o importare i dati di Adventure Works dal file [BACPAC](database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Ripristinare o importare i dati di Adventure Works dal file [BACPAC](database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 e software correlato

  # <a name="macos"></a>[macOS](#tab/macos)

  Per installare Homebrew e Python, il driver ODBC, SQLCMD e il driver Python per SQL Server, seguire i passaggi **1.2**, **1.3** e **2.1** della procedura [Creare app Python con SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Per altre informazioni, vedere [Driver Microsoft ODBC in macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Per installare Python e altri pacchetti necessari, usare `sudo apt-get install python python-pip gcc g++ build-essential`.

  Per installare il driver ODBC, SQLCMD e il driver Python per SQL Server, vedere [Configurare un ambiente per lo sviluppo di pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Per altre informazioni, vedere [Driver Microsoft ODBC in Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Per installare Python, il driver ODBC, SQLCMD e il driver Python per SQL Server, vedere [Configurare un ambiente per lo sviluppo di pyodbc Python](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Per altre informazioni, vedere [Driver Microsoft ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Gli script di questo articolo sono scritti in modo da usare il database **Adventure Works**.

> [!NOTE]
> È anche possibile scegliere di usare un'istanza gestita di SQL di Azure.
>
> Per le operazioni di creazione e configurazione, usare il [portale di Azure](../managed-instance/instance-create-quickstart.md), [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) o l'[interfaccia della riga di comando](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), quindi configurare la connettività dall'ambiente [locale](../managed-instance/point-to-site-p2s-configure.md) o dalla [macchina virtuale](../managed-instance/connect-vm-instance-configure.md).
>
> Per caricare i dati, vedere come eseguire il [ripristino con BACPAC](database-import.md) con il file di [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) oppure come eseguire il [ripristino del database Wide World Importers](../managed-instance/restore-sample-database-quickstart.md).

Per esplorare ulteriormente Python e il database nel database SQL di Azure, vedere [Librerie del database SQL di Azure per Python](/python/api/overview/azure/sql), il [repository di modelli pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e un [esempio di modelli pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Recuperare le informazioni di connessione del server

Recuperare le informazioni di connessione necessarie per connettersi al database nel database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per il database nel database SQL di Azure o il nome del server completo (o l'indirizzo IP) accanto a **Host** per un'istanza gestita di SQL di Azure o di SQL Server in una macchina virtuale di Azure. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

> [!NOTE]
> Per le informazioni di connessione per SQL Server in una macchina virtuale di Azure, vedere [Connettersi a un'istanza di SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Creare il codice per eseguire query sul database 

1. Nell'editor di testo, creare un nuovo file chiamato *sqltest.py*.  
   
1. Aggiungere il codice seguente. Sostituire \<server>, \<database>, \<username>, e \<password> con i propri valori.
   
   >[!IMPORTANT]
   >Il codice in questo esempio usa i dati di esempio di AdventureWorksLT, che è possibile scegliere come origine durante la creazione del database. Se il database contiene dati diversi, utilizzare le tabelle del database personale nella query SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
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

- [Progettare il primo database nel database SQL di Azure](design-first-database-tutorial.md)
- [Driver Microsoft Python per SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Centro per sviluppatori Python](https://azure.microsoft.com/develop/python/?v=17.23h)

