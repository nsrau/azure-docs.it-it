---
title: Usare .NET Core per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare .NET Core per creare un programma che si connette a un database SQL di Azure ed eseguire query usando istruzioni Transact-SQL.
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
ms.date: 12/21/2018
ms.openlocfilehash: 0b6e5116a90c66852ac39f67f9f32c94470e5332
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2019
ms.locfileid: "55565327"
---
# <a name="quickstart-use-net-core-c-to-query-an-azure-sql-database"></a>Guida introduttiva: Usare .NET Core (C#) per eseguire query su un database SQL di Azure

In questa guida introduttiva si userà [.NET Core](https://www.microsoft.com/net/) e il codice C# per la connessione a un database SQL di Azure. Si eseguirà quindi un'istruzione Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire questa esercitazione, è necessario avere:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Aver installato [.NET Core per il sistema operativo](https://www.microsoft.com/net/core). 

> [!NOTE]
> Questa guida introduttiva usa il database *mySampleDatabase*. Se si desidera utilizzare un database diverso, è necessario cambiare i riferimenti al database e modificare la query `SELECT` nel codice C#.


## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

#### <a name="get-adonet-connection-information-optional"></a>Ottenere informazioni di connessione ADO.NET (facoltativo)

1. Passare alla pagina **mySampleDatabase** e, in **Impostazioni**, selezionare **Stringhe di connessione**.

2. Esaminare la stringa di connessione completa **ADO.NET**.

    ![Stringa di connessione ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string2.png)

3. Copiare la stringa di connessione **ADO.NET** se si prevede di usarla.
  
## <a name="create-a-new-net-core-project"></a>Creare un nuovo progetto .NET Core

1. Aprire un prompt dei comandi e creare una cartella denominata **sqltest**. Passare a questa cartella ed eseguire questo comando.

    ```cmd
    dotnet new console
    ```
    Vengono creati nuovi file di progetto dell'app, inclusi un iniziale file di codice C# (**Program.cs**), un file di configurazione XML (**sqltest. csproj**) e i file binari necessari.

2. In un editor di testo, aprire **sqltest. csproj** e incollare il codice XML seguente tra i tag `<Project>`. In questo modo viene aggiunto `System.Data.SqlClient` come dipendenza.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-sql-database"></a>Inserire il codice per eseguire query sul database SQL

1. Aprire **Program.cs** in un editor di testo.

2. Sostituire il contenuto con il codice seguente e aggiungere i valori appropriati per il server, il database, il nome utente e la password.

> [!NOTE]
> Per usare una stringa di connessione ADO.NET, sostituire le 4 righe di codice impostando server, database, nome utente e password con la riga seguente. Immettere il proprio nome utente e la password.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

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

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
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
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt eseguire i seguenti comandi.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Verificare che vengano restituite le prime 20 righe.

   ```text
   Query data example:
   =========================================

   Road Frames HL Road Frame - Black, 58
   Road Frames HL Road Frame - Red, 58
   Helmets Sport-100 Helmet, Red
   Helmets Sport-100 Helmet, Black
   Socks Mountain Bike Socks, M
   Socks Mountain Bike Socks, L
   Helmets Sport-100 Helmet, Blue
   Caps AWC Logo Cap
   Jerseys Long-Sleeve Logo Jersey, S
   Jerseys Long-Sleeve Logo Jersey, M
   Jerseys Long-Sleeve Logo Jersey, L
   Jerseys Long-Sleeve Logo Jersey, XL
   Road Frames HL Road Frame - Red, 62
   Road Frames HL Road Frame - Red, 44
   Road Frames HL Road Frame - Red, 48
   Road Frames HL Road Frame - Red, 52
   Road Frames HL Road Frame - Red, 56
   Road Frames LL Road Frame - Black, 58
   Road Frames LL Road Frame - Black, 60
   Road Frames LL Road Frame - Black, 62

   Done. Press enter.
   ```
3. Scegliere **INVIO** per chiudere la finestra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione all'uso di .NET Core su Windows/Linux/macOS dalla riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [connettersi ed eseguire query su un database SQL di Azure usando .NET Framework e Visual Studio](sql-database-connect-query-dotnet-visual-studio.md).  
- Informazioni su come [Progettare il primo database SQL di Azure con SSMS](sql-database-design-first-database.md) o su come [Progettare un database SQL di Azure e connettersi con C# e ADO.NET](sql-database-design-first-database-csharp.md).
- Per altre informazioni su .NET, vedere la [documentazione di .NET](https://docs.microsoft.com/dotnet/).
