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
ms.date: 12/10/2018
ms.openlocfilehash: b9c33da4f002504a55802e4253d648ff87847d92
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53271828"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare Python per eseguire query su un database SQL di Azure

 Questa guida introduttiva illustra come usare [Python](https://python.org) per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati. Per altri dettagli sull'SDK, vedere la documentazione di [riferimento](https://docs.microsoft.com/python/api/overview/azure/sql), un [esempio pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e il [repository GitHub pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa guida introduttiva, accertarsi di soddisfare i requisiti seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Avere una [regola del firewall a livello di server](sql-database-get-started-portal-firewall.md) per l'indirizzo IP pubblico del computer usato per questa guida introduttiva.
  
- Software correlato e Python per il sistema operativo in uso:
  
  - **MacOS**: installare Homebrew e Python, installare il driver ODBC e SQLCMD e quindi installare il driver Python per SQL Server. Vedere i passaggi 1.2, 1.3 e 2.1 in [Creare app Python con SQL Server in MacOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/). Per altre informazioni, vedere [Installare il Driver ODBC Microsoft su Linux e MacOS](https://docs.microsoft.com/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).
    
  - **Ubuntu**: Installare Python e altri pacchetti necessari con `sudo apt-get install python python-pip gcc g++ build-essential`. Scaricare e installare il driver ODBC, SQLCMD e il driver Python per SQL Server. Per istruzioni, vedere [Configurare un ambiente di sviluppo per lo sviluppo Python con pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).
    
  - **Windows**: Installare Python, il driver ODBC, SQLCMD e il driver Python per SQL Server. Per istruzioni, vedere [Configurare un ambiente di sviluppo per lo sviluppo Python con pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]
    
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

