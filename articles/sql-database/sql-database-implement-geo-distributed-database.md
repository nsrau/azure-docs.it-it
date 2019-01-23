---
title: Implementare una soluzione di database SQL di Azure con distribuzione geografica | Microsoft Docs
description: Informazioni su come configurare il database SQL di Azure e l'applicazione per il failover in un database replicato e su come testare un failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 01/03/2018
ms.openlocfilehash: 679a02c760d8b37d94a734bc9b023ed8fe59acad
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198185"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Esercitazione: Implementare un database con distribuzione geografica

Questa esercitazione mostra come configurare un database SQL di Azure e l'applicazione per il failover in un'area remota e come testare un piano di failover. Si apprenderà come:

> [!div class="checklist"]
> - Creare un [gruppo di failover](sql-database-auto-failover-group.md)
> - Usare un'applicazione Java per eseguire query su un database SQL di Azure
> - Testare un failover

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, verificare di avere installato i componenti seguenti:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Un database SQL di Azure. Per crearne uno, usare:
  - [Portale](sql-database-get-started-portal.md)
  - [Interfaccia della riga di comando](sql-database-cli-samples.md)
  - [PowerShell](sql-database-powershell-samples.md)

  > [!NOTE]
  > In questa esercitazione viene usato il database di esempio *AdventureWorksLT*.

- Java e Maven. Vedere [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Creare un'app con SQL Server), evidenziare **Java**, selezionare l'ambiente in uso e quindi seguire i passaggi.

> [!IMPORTANT]
> Assicurarsi di configurare le regole del firewall in modo da usare l'indirizzo IP pubblico del computer in cui si eseguono i passaggi dell'esercitazione. Le regole del firewall a livello di database verranno replicate automaticamente nel server secondario.
>
> Per informazioni, vedere [Creare una regola del firewall a livello di database](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) o per determinare l'indirizzo IP usato per la regola del firewall a livello di server per il proprio computer, vedere [Creare una regola del firewall a livello di server](sql-database-get-started-portal-firewall.md).  

## <a name="create-a-failover-group"></a>Creare un gruppo di failover

Usando Azure PowerShell, creare [gruppi di failover](sql-database-auto-failover-group.md) tra un server SQL di Azure esistente e un nuovo server SQL di Azure in un'altra area. Aggiungere quindi il database di esempio al gruppo di failover.

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Per creare un gruppo di failover, eseguire lo script seguente:

   ```powershell
    # Set variables for your server and database
    $adminlogin = "<your admin>"
    $password = "<your password>"
    $myresourcegroupname = "<your resource group name>"
    $mylocation = "<your resource group location>"
    $myservername = "<your existing server name>"
    $mydatabasename = "<your database name>"
    $mydrlocation = "<your disaster recovery location>"
    $mydrservername = "<your disaster recovery server name>"
    $myfailovergroupname = "<your globally unique failover group name>"

    # Create a backup server in the failover region
    New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
       -ServerName $mydrservername `
       -Location $mydrlocation `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
          -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

    # Create a failover group between the servers
    New-AzureRMSqlDatabaseFailoverGroup `
       –ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -PartnerServerName $mydrservername  `
       –FailoverGroupName $myfailovergroupname `
       –FailoverPolicy Automatic `
       -GracePeriodWithDataLossHours 2

    # Add the database to the failover group
    Get-AzureRmSqlDatabase `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -DatabaseName $mydatabasename | `
     Add-AzureRmSqlDatabaseToFailoverGroup `
       -ResourceGroupName $myresourcegroupname `
       -ServerName $myservername `
       -FailoverGroupName $myfailovergroupname
   ```

Le impostazioni di replica geografica possono essere modificate anche nel portale di Azure, selezionando il database e quindi scegliendo **Impostazioni** > **Replica geografica**.

![Impostazioni di replica geografica](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>Eseguire il progetto di esempio

1. Nella console creare un progetto Maven con il comando seguente:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Digitare **Y** e premere **INVIO**.

1. Passare alla directory del nuovo progetto.

   ```bash
   cd SqlDbSample
   ```

1. Con l'editor preferito, aprire il file *pom.xml* nella cartella del progetto.

1. Includere la dipendenza Microsoft JDBC Driver per SQL Server aggiungendo la sezione `dependency` seguente. La dipendenza deve essere incollata all'interno della sezione `dependencies` più estesa.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Specificare la versione di Java aggiungendo la sezione `properties` dopo la sezione `dependencies`:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Supportare i file manifesto aggiungendo la sezione `build` dopo la sezione `properties`:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Salvare e chiudere il file *pom.xml*.

1. Aprire il file *App.java* in ..\SqlDbSample\src\main\java\com\sqldbsamples e sostituirne il contenuto con il codice seguente:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Salvare e chiudere il file *App.java*.

1. Nella console dei comandi eseguire il comando seguente:

   ```bash
   mvn package
   ```

1. Avviare l'applicazione che verrà eseguita per circa un'ora finché non si arresterà manualmente, tenendo conto del tempo necessario per eseguire il test di failover.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Testare un failover

Eseguire gli script seguenti per simulare un failover e osservare i risultati dell'applicazione. Si noti come alcune operazioni di inserimento e selezione avranno esito negativo durante la migrazione del database.

È anche possibile verificare il ruolo del server di ripristino di emergenza durante il test con il comando seguente:

   ```powershell
   (Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername).ReplicationRole
   ```

Per testare un failover:

1. Avviare un failover manuale del gruppo di failover:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -FailoverGroupName $myfailovergroupname
   ```

1. Ripristinare il gruppo di failover nel server primario:

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   ```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati configurati un database SQL di Azure e un'applicazione per il failover in un'area remota ed è stato testato un piano di failover. Si è appreso come:

> [!div class="checklist"]
> - Creare un gruppo di failover con replica geografica
> - Usare un'applicazione Java per eseguire query su un database SQL di Azure
> - Testare un failover

Passare all'esercitazione successiva per informazioni su come eseguire la migrazione con Servizio Migrazione del database.

> [!div class="nextstepaction"]
> [Eseguire la migrazione di SQL Server a Istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure](../dms/tutorial-sql-server-to-managed-instance.md)
