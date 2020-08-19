---
title: Usare Visual Studio con .NET e C# per eseguire query
description: Usare Visual Studio per creare un'app C# che si connette a un database nel database SQL di Azure o nell'istanza gestita di SQL di Azure ed esegue query.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 8fe541432366d3c2ac1dc1470fea66d328f79780
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213041"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Avvio rapido: Usare .NET (C#) e C# in Visual Studio per connettersi a un database nel database SQL di Azure o nell'istanza gestita di SQL di Azure ed eseguire query
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Questa guida di avvio rapido illustra come usare il codice [.NET Framework](https://www.microsoft.com/net/) e C# in Visual Studio per eseguire query su un database nel database SQL di Azure con istruzioni Transact-SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione introduttiva, sono necessari gli elementi seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Database nel database SQL di Azure. Per creare e quindi configurare un database in Database SQL di Azure è possibile usare una di queste guide introduttive:

  | Azione | Database SQL | Istanza gestita di SQL | SQL Server in una macchina virtuale Azure |
  |:--- |:--- |:---|:---|
  | Create| [Portale](single-database-create-quickstart.md) | [Portale](../managed-instance/instance-create-quickstart.md) | [Portale](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurare | [Regola del firewall IP a livello di server](firewall-create-server-level-portal-quickstart.md)| [Connettività da una VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Connettività dall'ambiente locale](../managed-instance/point-to-site-p2s-configure.md) | [Connettersi a SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Caricare dati|Adventure Works caricato in base alla guida introduttiva|[Ripristinare Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Ripristinare Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Ripristinare o importare i dati di Adventure Works dal file [BACPAC](database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Ripristinare o importare i dati di Adventure Works dal file [BACPAC](database-import.md) ottenuto da [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Gli script in questo articolo sono scritti in modo da usare il database Adventure Works. Con un'istanza gestita di SQL, è necessario importare il database Adventure Works in un database dell'istanza oppure modificare gli script di questo articolo per usare il database Wide World Importers.

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional o Enterprise Edition.

## <a name="get-server-connection-information"></a>Recuperare le informazioni di connessione del server

Ottenere le informazioni di connessione necessarie per connettersi al database. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Passare alla pagina **Database SQL** o **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per il database nel database SQL di Azure o il nome del server completo (o l'indirizzo IP) accanto a **Host** per un'istanza gestita di SQL di Azure o di SQL Server in una macchina virtuale di Azure. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**.

> [!NOTE]
> Per le informazioni di connessione per SQL Server in una macchina virtuale di Azure, vedere [Connettersi a un'istanza di SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Creare il codice per eseguire query sul database nel database SQL di Azure

1. In Visual Studio creare un nuovo progetto. 
   
1. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#** , **App console (.NET Framework)** .
   
1. Immettere *sqltest* come nome di progetto e quindi selezionare **OK**. Viene creato il nuovo progetto. 
   
1. Selezionare **Progetto** > **Gestisci pacchetti NuGet**. 
   
1. In **Gestisci pacchetti NuGet**, selezionare la scheda **Sfoglia**, quindi cercare e selezionare **Microsoft.Data.SqlClient**.
   
1. Nella pagina **Microsoft.Data.SqlClient** selezionare **Installa**. 
   - Se richiesto, selezionare **OK** per continuare l'installazione. 
   - Se viene visualizzata una finestra **Accettazione della licenza** selezionare **Accetto**.
   
1. Al termine dell'installazione, è possibile chiudere **Gestisci pacchetti NuGet**. 
   
1. Nell'editor di codice, sostituire i contenuti **Program.cs** con il codice seguente. Sostituire con i valori per `<server>`, `<username>`, `<password>` e `<database>`.
   
   >[!IMPORTANT]
   >Il codice in questo esempio usa i dati di esempio di AdventureWorksLT, che è possibile scegliere come origine durante la creazione del database. Se il database contiene dati diversi, utilizzare le tabelle del database personale nella query SELECT. 
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                       
                       StringBuilder sb = new StringBuilder();
                       sb.Append("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName ");
                       sb.Append("FROM [SalesLT].[ProductCategory] pc ");
                       sb.Append("JOIN [SalesLT].[Product] p ");
                       sb.Append("ON pc.productcategoryid = p.productcategoryid;");
                       String sql = sb.ToString();
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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

- Informazioni su come [connettersi ed eseguire query su un database nel database SQL di Azure usando .NET Core](connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Informazioni su come [iniziare a usare .NET Core in Windows/Linux/macOS con la riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [progettare il primo database nel database SQL di Azure con SSMS](design-first-database-tutorial.md) o su come [progettare il primo database nel database SQL di Azure con .NET](design-first-database-csharp-tutorial.md).
- Per altre informazioni su .NET, vedere la [documentazione di .NET](https://docs.microsoft.com/dotnet/).
- Esempio di logica di ripetizione dei tentativi: [Connettersi in modo resiliente ad Azure SQL con ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

