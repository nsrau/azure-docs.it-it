---
title: Usare Java per eseguire query sul database SQL di Azure | Microsoft Docs
description: Questo argomento illustra come usare Java per creare un programma che si connette a un database SQL di Azure ed esegue query usando istruzioni Transact-SQL.
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: andrela
ms.openlocfilehash: 943e355ffcc6c0670585936ec2e559b7fb592ff5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Usare Java per eseguire query su un database SQL di Azure

Questa guida introduttiva illustra come usare [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query sui dati.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione introduttiva, accertarsi di avere i prerequisiti seguenti:

- un database SQL di Azure. Questa guida introduttiva usa le risorse create in una delle guide introduttive seguenti: 

   - [Creare un database: portale](sql-database-get-started-portal.md)
   - [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
   - [Creare un database: PowerShell](sql-database-get-started-powershell.md)

- Una [regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) per l'indirizzo IP pubblico del computer usato per questa esercitazione introduttiva.

- Avere installato Java e il software correlato per il sistema operativo.

    - **MacOS**: installare Homebrew e Java, quindi installare Maven. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **Ubuntu**: installare Java Development Kit e quindi Maven. Vedere i [passaggi 1.2, 1.3 e 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **Windows**: installare Java Development Kit e Maven. Vedere i [passaggi 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come illustrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si dimenticano le informazioni di accesso per il server, passare alla pagina del server di database SQL per visualizzare il nome dell'amministratore del server.  Se necessario, reimpostare la password.     

## <a name="create-maven-project-and-dependencies"></a>**Creare il progetto Maven e le dipendenze**
1. Dal terminale creare un nuovo progetto Maven denominato **sqltest**. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Quando richiesto, immettere **Y**.
3. Passare alla directory **sqltest** e aprire ***pom.xml*** con l'editor di testo preferito.  Aggiungere **Microsoft JDBC Driver per SQL Server** alle dipendenze del progetto usando il codice seguente:

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.2.1.jre8</version>
   </dependency>
   ```

4. In ***pom.xml*** aggiungere anche le proprietà seguenti al progetto.  Se non è presente una sezione properties, è possibile aggiungerla dopo le dipendenze.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Salvare e chiudere ***pom.xml***.

## <a name="insert-code-to-query-sql-database"></a>Inserire il codice per eseguire query sul database SQL

1. Nel progetto Maven in ..\sqltest\src\main\java\com\sqlsamples\App.java dovrebbe essere già presente un file denominato ***App.java***.

2. Aprire il file, sostituirne il contenuto con il codice seguente e aggiungere i valori appropriati per il server, il database, l'utente e la password.

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
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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

## <a name="run-the-code"></a>Eseguire il codice

1. Al prompt dei comandi eseguire questi comandi:

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Verificare che vengano restituite le prime 20 righe e quindi chiudere la finestra dell'applicazione.


## <a name="next-steps"></a>Passaggi successivi
- [Progettare il primo database SQL di Azure](sql-database-design-first-database.md)
- [Driver Microsoft JDBC per SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Segnalare problemi e porre domande](https://github.com/microsoft/mssql-jdbc/issues)

