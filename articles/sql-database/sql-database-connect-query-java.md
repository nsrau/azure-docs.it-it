---
title: Usare Java per eseguire query su un database
description: Illustra come usare Java per creare un programma che si connette a un database SQL di Azure ed eseguire query sul database con istruzioni T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768652"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Avvio rapido: Usare Java per eseguire query su un database SQL di Azure

Questo argomento di avvio rapido illustra come usare Java per connettersi a un database SQL di Azure e usare le istruzioni T-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisites

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuito](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Un [database SQL di Azure](sql-database-single-database-get-started.md)
- Software correlato a [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Installare Homebrew, Java e quindi Maven seguendo i passaggi **1.2** e **1.3** della procedura [Creare app Java con SQL Server in macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Installare Java, Java Development Kit e quindi Maven seguendo i passaggi **1.2**, **1.3** e **1.4** della procedura [Creare app Java con SQL Server in Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Installare Java, quindi Maven seguendo i passaggi **1.2** e **1.3** della procedura [Creare app Java con SQL Server in Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> Gli script di questo articolo sono scritti in modo da usare il database **Adventure Works**.

> [!NOTE]
> È anche possibile scegliere di usare un'istanza gestita di Azure SQL.
>
> Per creare e configurare il progetto, usare il [portale di Azure](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) o l'[interfaccia della riga di comando](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44), quindi configurare la connettività [sul posto](sql-database-managed-instance-configure-p2s.md) o tramite [VM](sql-database-managed-instance-configure-vm.md).
>
> Per caricare i dati, vedere come eseguire il [ripristino con BACPAC](sql-database-import.md) con il file di [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) oppure come eseguire il [ripristino del database Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Ottenere informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server o il nome host, il nome del database e le informazioni di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Database SQL** o aprire la pagina **Istanze gestite di SQL**.

3. Nella pagina **Panoramica** verificare il nome completo del server accanto a **Nome server** per un database singolo o accanto a **Host** per un'istanza gestita. Per copiare il nome del server o il nome host, passare il puntatore su di esso e selezionare l'icona **Copia**. 

## <a name="create-the-project"></a>Creare il progetto

1. Dal prompt dei comandi creare un nuovo progetto Maven denominato *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Passare alla cartella *sqltest* e aprire *pom.xml* con l'editor di testo preferito. Aggiungere **Microsoft JDBC Driver per SQL Server** alle dipendenze del progetto usando il codice seguente.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. In *pom.xml* aggiungere anche le proprietà seguenti al progetto. Se non è presente una sezione properties, è possibile aggiungerla dopo le dipendenze.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Salvare e chiudere *pom.xml*.

## <a name="add-code-to-query-database"></a>Aggiungere il codice per eseguire query sul database

1. Nel progetto Maven dovrebbe essere già presente un file denominato *App.java* in:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Aprire il file e sostituirne il contenuto con il codice seguente. Aggiungere quindi i valori appropriati per il server, il database, l'utente e la password.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > L'esempio di codice usa il database di esempio **AdventureWorksLT** per Azure SQL.

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi eseguire l'app.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Verificare che vengano restituite le prime 20 righe e chiudere la finestra dell'app.

## <a name="next-steps"></a>Passaggi successivi

- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)  

- [Driver Microsoft JDBC per SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Segnalare problemi e porre domande](https://github.com/microsoft/mssql-jdbc/issues)  
