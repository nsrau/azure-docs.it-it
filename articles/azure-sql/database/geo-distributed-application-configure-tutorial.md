---
title: Implementare una soluzione con distribuzione geografica
description: Informazioni su come configurare il database nel database SQL di Azure e nell'applicazione client per il failover in un database replicato e il failover di test.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 90bfe3f6e8d5ed19380ab2b48aa38e598c3f109a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501611"
---
# <a name="tutorial-implement-a-geo-distributed-database-azure-sql-database"></a>Esercitazione: implementare un database con distribuzione geografica (database SQL di Azure)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Configurare un database nel database SQL e nell'applicazione client per il failover in un'area remota e testare un piano di failover. Si apprenderà come:

> [!div class="checklist"]
>
> - Creazione di un [gruppo di failover](auto-failover-group-overview.md)
> - Eseguire un'applicazione Java per eseguire query su un database nel database SQL
> - Failover di test

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato da Database SQL di Azure, ma tutte le attività di sviluppo future sono incentrate sul modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Per completare l'esercitazione, verificare di avere installato i componenti seguenti:

- [Azure PowerShell](/powershell/azure/)
- Un database singolo nel database SQL di Azure. Per crearne uno, usare:
  - [Portale di Azure](single-database-create-quickstart.md)
  - [L’interfaccia della riga di comando di Azure](az-cli-script-samples-content-guide.md)
  - [PowerShell](powershell-script-content-guide.md)

  > [!NOTE]
  > In questa esercitazione viene usato il database di esempio *AdventureWorksLT*.

- Java e Maven. Vedere [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Creare un'app con SQL Server), evidenziare **Java**, selezionare l'ambiente in uso e quindi seguire i passaggi.

> [!IMPORTANT]
> Assicurarsi di configurare le regole del firewall in modo da usare l'indirizzo IP pubblico del computer in cui si eseguono i passaggi dell'esercitazione. Le regole del firewall a livello di database verranno replicate automaticamente nel server secondario.
>
> Per informazioni, vedere creare una [regola del firewall a livello di database](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) o per determinare l'indirizzo IP usato per la regola del firewall a livello di server per il computer, vedere [creare un firewall a livello di server](firewall-create-server-level-portal-quickstart.md).  

## <a name="create-a-failover-group"></a>Creare un gruppo di failover

Utilizzando Azure PowerShell, creare [gruppi di failover](auto-failover-group-overview.md) tra un server esistente e un nuovo server in un'altra area. Aggiungere quindi il database di esempio al gruppo di failover.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

Per creare un gruppo di failover, eseguire lo script seguente:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

> [!IMPORTANT]
> Eseguire `az login` per accedere ad Azure.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

È anche possibile modificare le impostazioni di replica geografica nella portale di Azure, selezionando il database, quindi **Impostazioni**  >  **replica geografica**.

![Impostazioni di replica geografica](./media/geo-distributed-application-configure-tutorial/geo-replication.png)

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

1. Usando l'editor preferito, aprire il file *pom.xml* nella cartella del progetto.

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

1. Salvare e chiudere il file *app. Java* .

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

## <a name="test-failover"></a>Failover di test

Eseguire gli script seguenti per simulare un failover e osservare i risultati dell'applicazione. Si noti come alcune operazioni di inserimento e selezione avranno esito negativo durante la migrazione del database.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

È possibile controllare il ruolo del server di ripristino di emergenza durante il test con il comando seguente:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Per testare un failover:

1. Avviare un failover manuale del gruppo di failover:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Ripristinare il gruppo di failover nel server primario:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile controllare il ruolo del server di ripristino di emergenza durante il test con il comando seguente:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Per testare un failover:

1. Avviare un failover manuale del gruppo di failover:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Ripristinare il gruppo di failover nel server primario:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato configurato un database nel database SQL di Azure e un'applicazione per il failover in un'area remota ed è stato testato un piano di failover. Si è appreso come:

> [!div class="checklist"]
>
> - Creare un gruppo di failover con replica geografica
> - Eseguire un'applicazione Java per eseguire query su un database nel database SQL
> - Failover di test

Passare all'esercitazione successiva su come aggiungere un'istanza di Azure SQL Istanza gestita a un gruppo di failover:

> [!div class="nextstepaction"]
> [Aggiungere un'istanza di Istanza gestita SQL di Azure a un gruppo di failover](../managed-instance/failover-group-add-instance-tutorial.md)
