---
title: Usare Visual Studio con .NET e C# per eseguire query nel database SQL di Azure | Microsoft Docs
description: Usare Visual Studio per creare un'app C# che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 3ed11d2b1628cecc0696e4c37135cfc7d2190de5
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597917"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Guida introduttiva: Usare .NET (C#) e C# in Visual Studio per connettersi a un database SQL di Azure ed eseguire query

Questa guida introduttiva illustra come usare il [framework .NET](https://www.microsoft.com/net/) e il codice C# in Visual Studio per eseguire query nel database SQL di Azure usando istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- un database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  || Database singolo | Istanza gestita |
  |:--- |:--- |:---|
  | Create| [Portale](sql-database-single-database-get-started.md) | [Portale](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurare | [Regola del firewall IP a livello di server](sql-database-server-level-firewall-rule.md)| [Connettività da una VM](sql-database-managed-instance-configure-vm.md)|
  |||[Connettività da locale](sql-database-managed-instance-configure-p2s.md)
  |Caricare i dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Ripristinare o importare Adventure Works dal file [BACPAC](sql-database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Gli script in questo articolo sono scritti in modo da usare il database Adventure Works. Con un'istanza gestita, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo in modo da usare il database Wide World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional o Enterprise Edition.

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**. 

## <a name="create-code-to-query-the-sql-database"></a>Creare codice per eseguire query sul database SQL

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**. 
   
1. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#** , quindi **App console (.NET Framework)** .
   
1. Immettere *sqltest* come nome di progetto e quindi selezionare **OK**. Viene creato il nuovo progetto. 
   
1. Selezionare **Progetto** > **Gestisci pacchetti NuGet**. 
   
1. In **Gestisci pacchetti NuGet**, selezionare la scheda **Sfoglia**, quindi cercare e selezionare **System.Data.SqlClient**.
   
1. Nella pagina **System.Data.SqlClient** selezionare **Installa**. 
   - Se richiesto, selezionare **OK** per continuare l'installazione. 
   - Se viene visualizzata una finestra **Accettazione della licenza** selezionare **Accetto**.
   
1. Al termine dell'installazione, è possibile chiudere **Gestisci pacchetti NuGet**. 
   
1. Nell'editor di codice, sostituire i contenuti **Program.cs** con il codice seguente. Sostituire con i valori per `<server>`, `<username>`, `<password>` e `<database>`.
   
   >[!IMPORTANT]
   >Il codice in questo esempio usa i dati di esempio di AdventureWorksLT, che è possibile scegliere come origine durante la creazione del database. Se il database contiene dati diversi, utilizzare le tabelle del database personale nella query SELECT. 
   
   ```csharp
   using System;
   using System.Data.SqlClient;
   using System.Text;
   
   namespace sqltest
   {
       class Program
       {
           static void Main(string[] args)
           {
               try 
               { 
                   SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();
                   builder.DataSource = "<server>.database.windows.net"; 
                   builder.UserID = "<username>";            
                   builder.Password = "<password>";     
                   builder.InitialCatalog = "<database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       connection.Open();       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           using (SqlDataReader reader = command.ExecuteReader())
                           {
                               while (reader.Read())
                               {
                                   Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                               }
                           }
                       }                    
                   }
               }
               catch (SqlException e)
               {
                   Console.WriteLine(e.ToString());
               }
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Eseguire il codice

1. Per eseguire l'app, selezionare **Debug** > **Avvia debug**, o selezionare **Avvia** sulla barra degli strumenti oppure premere **F5**.
1. Verificare che vengano restituite le prime 20 righe di categoria/prodotto dal database, quindi chiudere la finestra di dell'app.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [connettersi ed eseguire query su un database SQL di Azure usando .NET Core](sql-database-connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Informazioni su come [iniziare a usare .NET Core in Windows/Linux/macOS con la riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [progettare il primo database SQL di Azure con SSMS](sql-database-design-first-database.md) o su come [progettare il primo database SQL di Azure con .NET](sql-database-design-first-database-csharp.md).
- Per altre informazioni su .NET, vedere la [documentazione di .NET](https://docs.microsoft.com/dotnet/).
- Esempio di logica di ripetizione dei tentativi: [Connettersi in modo resiliente a SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

