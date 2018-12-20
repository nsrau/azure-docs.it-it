---
title: Usare Visual Studio con .NET e C# per eseguire query nel database SQL di Azure | Microsoft Docs
description: Usare Visual Studio per creare un'app C# che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: dotnet
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/11/2018
ms.openlocfilehash: 04a0abd0fba7ec53aebeb481ac80d36653d118b6
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384939"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-an-azure-sql-database"></a>Guida introduttiva: Usare .NET (C#) e C# in Visual Studio per connettersi a un database SQL ed eseguire query

Questa guida introduttiva illustra come usare il [framework .NET](https://www.microsoft.com/net/) e il codice C# in Visual Studio per eseguire query nel database SQL di Azure usando istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]
  
- Una [regola del firewall a livello di server](sql-database-get-started-portal-firewall.md) per consentire l'indirizzo IP pubblico del computer usato.
  
- [Visual Studio 2017](https://www.visualstudio.com/downloads/) Community, Professional o Enterprise edition.

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-code-to-query-the-sql-database"></a>Creare codice per eseguire query sul database SQL

1. In Visual Studio selezionare **File** > **Nuovo** > **Progetto**. 
   
1. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#**, quindi **App console (.NET Framework)**.
   
1. Immettere *sqltest* come nome di progetto e quindi selezionare **OK**. Viene creato il nuovo progetto. 
   
1. Selezionare **Progetto** > **Gestisci pacchetti NuGet**. 
   
1. In **Gestisci pacchetti NuGet**, selezionare la scheda **Sfoglia**, quindi cercare e selezionare **System.Data.SqlClient**.
   
1. Nella pagina **System.Data.SqlClient** selezionare **Installa**. 
   - Se richiesto, selezionare **OK** per continuare l'installazione. 
   - Se viene visualizzata una finestra **Accettazione della licenza** selezionare **Accetto**.
   
1. Al termine dell'installazione, è possibile chiudere **Gestisci pacchetti NuGet**. 
   
1. Nell'editor di codice, sostituire i contenuti **Program.cs** con il codice seguente. Sostituire con i valori per `<server>`, `<username>`, `<password>`, e `<database>`.
   
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

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

