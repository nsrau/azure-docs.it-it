---
title: Usare Visual Studio e .NET per eseguire query nel database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare Visual Studio per creare un programma che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: devcenter
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/05/2017
ms.author: carlrab
ms.openlocfilehash: d74c889b60e4d2ad24d70db70f4a0bc8284013ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-net-c-with-visual-studio-to-connect-and-query-an-azure-sql-database"></a>Usare .NET (C#) con Visual Studio Code per connettersi a un database SQL ed eseguire query

Questa esercitazione introduttiva illustra come usare [.NET Framework](https://www.microsoft.com/net/) per creare un programma C# con Visual Studio per connettersi a un database SQL di Azure e usare istruzioni Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione introduttiva, accertarsi di avere:

- un database SQL di Azure. Questa guida introduttiva usa le risorse create in una delle guide introduttive seguenti: 

   - [Creare un database: portale](sql-database-get-started-portal.md)
   - [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
   - [Creare un database: PowerShell](sql-database-get-started-powershell.md)

- Una [regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) per l'indirizzo IP pubblico del computer usato per questa esercitazione introduttiva.
- Un'installazione di [Visual Studio Community 2017, Visual Studio Professional 2017 o Visual Studio Enterprise 2017](https://www.visualstudio.com/downloads/).

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si dimenticano le informazioni di accesso per il server di database SQL di Azure, passare alla pagina del server di database SQL per visualizzare il nome dell'amministratore del server. È possibile reimpostare la password, se necessario.

5. Fare clic su **Mostra stringhe di connessione del database**.

6. Esaminare la stringa di connessione completa **ADO.NET**.

    ![Stringa di connessione ADO.NET](./media/sql-database-connect-query-dotnet/adonet-connection-string.png)

> [!IMPORTANT]
> È necessario avere una regola del firewall impostata per l'indirizzo IP pubblico del computer su cui si esegue questa esercitazione. Se si usa un computer o un indirizzo IP pubblico diverso, creare una [regola del firewall a livello di server con il portale di Azure](sql-database-get-started-portal.md#create-a-server-level-firewall-rule). 
>
  
## <a name="create-a-new-visual-studio-project"></a>Creare un nuovo progetto di Visual Studio

1. In Visual Studio scegliere **File**, **Nuovo**, **Progetto**. 
2. Nella finestra di dialogo **Nuovo progetto** espandere **Visual C#**.
3. Selezionare **Applicazione console** e immettere *sqltest* come nome di progetto.
4. Fare clic su **OK** per creare e aprire il nuovo progetto in Visual Studio
4. In Esplora soluzioni fare clic con il pulsante destro del mouse su **sqltest** e scegliere **Gestisci pacchetti NuGet**. 
5. In **Sfoglia** cercare ```System.Data.SqlClient``` e, dopo averlo trovato, selezionarlo.
6. Nella pagina **System.Data.SqlClient** fare clic su **Installa**.
7. Al termine dell'installazione, rivedere le modifiche e quindi fare clic su **OK** per chiudere la finestra **Anteprima**. 
8. Se viene visualizzata una finestra **Accettazione della licenza** fare clic su **Accetto**.

## <a name="insert-code-to-query-sql-database"></a>Inserire il codice per eseguire query sul database SQL
1. Passare a (o aprire, se necessario) **Program.cs**

2. Sostituire il contenuto di **Program.cs** con il codice seguente e aggiungere i valori appropriati per il server, il database, l'utente e la password.

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
                builder.DataSource = "your_server.database.windows.net"; 
                builder.UserID = "your_user";            
                builder.Password = "your_password";     
                builder.InitialCatalog = "your_database";

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

1. Premere **F5** per eseguire l'applicazione.
2. Verificare che vengano restituite le prime 20 righe e quindi chiudere la finestra dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [connettersi ed eseguire query su un database SQL di Azure usando .NET Core](sql-database-connect-query-dotnet-core.md) in Windows/Linux/macOS.  
- Informazioni su come [iniziare a usare .NET Core in Windows/Linux/macOS con la riga di comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Informazioni su come [progettare il primo database SQL di Azure con SSMS](sql-database-design-first-database.md) o su come [progettare il primo database SQL di Azure con .NET](sql-database-design-first-database-csharp.md).
- Per altre informazioni su .NET, vedere la [documentazione di .NET](https://docs.microsoft.com/dotnet/).
