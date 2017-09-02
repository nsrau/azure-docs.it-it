---
title: Implementare una soluzione di database SQL di Azure con distribuzione geografica | Microsoft Docs
description: Informazioni su come configurare il database SQL di Azure e l'applicazione per il failover in un database replicato e su come testare il failover.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,business continuity
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/26/2017
ms.author: carlrab
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 9f53f318e20dac9248906bdbe898ba4dacb286ac
ms.contentlocale: it-it
ms.lasthandoff: 06/30/2017

---

# <a name="implement-a-geo-distributed-database"></a>Implementare un database con distribuzione geografica

In questa esercitazione vengono configurati un database SQL di Azure e l'applicazione per il failover in un'area remota e viene quindi testato il piano di failover. Si apprenderà come: 

> [!div class="checklist"]
> * Creare utenti del database e concedere loro le autorizzazioni
> * Configurare una regola del firewall a livello di database
> * Creare un [gruppo di failover con replica geografica](sql-database-geo-replication-overview.md)
> * Creare e compilare un'applicazione Java per eseguire query su un database SQL di Azure
> * Eseguire un'esercitazione sul ripristino di emergenza

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.


## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Installazione della versione più recente di [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs). 
- Installazione di un database SQL di Azure. Questa esercitazione usa il database di esempio AdventureWorksLT con il nome **mySampleDatabase** da una di queste guide introduttive:

   - [Creare un database: portale](sql-database-get-started-portal.md)
   - [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)
   - [Creare un database: PowerShell](sql-database-get-started-powershell.md)

- Individuazione di un metodo per eseguire script SQL sul database. È possibile usare uno degli strumenti di query seguenti:
   - Editor di query nel [portale di Azure](https://portal.azure.com). Per altre informazioni sull'uso dell'editor di query nel portale di Azure, vedere la sezione su come [connettersi ed eseguire query con l'editor di query](sql-database-get-started-portal.md#query-the-sql-database).
   - Versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), un ambiente integrato per la gestione di qualsiasi infrastruttura SQL, da SQL Server al database SQL per Microsoft Windows.
   - Versione più recente di [Visual Studio Code](https://code.visualstudio.com/docs), un editor grafico di codice per Linux, macOS e Windows che supporta estensioni, tra cui l'[estensione mssql](https://aka.ms/mssql-marketplace), per l'esecuzione di query su Microsoft SQL Server, database SQL di Azure e SQL Data Warehouse. Per altre informazioni sull'uso di questo strumento con il database SQL di Azure, vedere l'articolo su come [connettersi ed eseguire query con Visual Studio Code](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Creare utenti del database e concedere autorizzazioni

Connettersi al database e creare account utente usando uno degli strumenti di query seguenti:

- Editor di query nel portale di Azure
- SQL Server Management Studio
- Visual Studio Code

Questi account utente vengono replicati automaticamente nel server secondario e vengono mantenuti sincronizzati. Per usare SQL Server Management Studio o Visual Studio Code potrebbe essere necessario configurare una regola del firewall, in caso di connessione da un client con un indirizzo IP per il quale non si è ancora configurato un firewall. Per la procedura dettagliata, vedere [Creare una regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- In una finestra di query eseguire questa query per creare due account utente nel database. Questo script concede autorizzazioni **db_owner** all'account **app_admin** e autorizzazioni **SELECT** e **UPDATE** all'account **app_user**. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Creare il firewall a livello di database

Creare una [regola del firewall a livello di database](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) per il database SQL. Questa regola del firewall a livello di database viene replicata automaticamente nel server secondario creato in questa esercitazione. Per semplicità, in questa esercitazione usare l'indirizzo IP pubblico del computer in cui si eseguono i passaggi dell'esercitazione. Per determinare l'indirizzo IP usato per la regola del firewall a livello di server per il computer corrente, vedere [Creare una regola del firewall a livello di server](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).  

- Nella finestra di query aperta sostituire la query precedente con la query seguente, sostituendo gli indirizzi IP con gli indirizzi IP appropriati per l'ambiente in uso.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Creare un gruppo di failover automatico con replica geografica attiva 

Usando Azure PowerShell, creare un [gruppo di failover automatico con replica geografica attiva](sql-database-geo-replication-overview.md) tra il server SQL di Azure esistente e il nuovo server SQL di Azure vuoto in un'area di Azure e quindi aggiungere il database di esempio al gruppo di failover.

> [!IMPORTANT]
> Per questi cmdlet è necessario Azure PowerShell 4.0. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Inserire le variabili per gli script di PowerShell usando i valori del server esistente e del database di esempio e specificare un valore univoco globale come nome del gruppo di failover.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Creare un server di backup vuoto nell'area di failover.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Creare un gruppo di failover tra i due server.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. Aggiungere il database al gruppo di failover.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Installare il software Java

Le procedure descritte in questa sezione presuppongono che si abbia familiarità con lo sviluppo con Java ma non con il database SQL di Azure. 

### <a name="mac-os"></a>**Mac OS**
Aprire il terminale in uso e passare alla directory in cui si prevede di creare il progetto Java. Installare **brew** e **Maven** immettendo i comandi seguenti: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Per indicazioni dettagliate sull'installazione e la configurazione dell'ambiente Java e Maven, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **Java** e quindi **MacOS** e infine seguire le istruzioni dettagliate per la configurazione di Java e Maven riportate nei passaggi 1.2 e 1.3.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Aprire il terminale in uso e passare alla directory in cui si prevede di creare il progetto Java. Installare **Maven** immettendo i comandi seguenti:

```bash
sudo apt-get install maven
```

Per indicazioni dettagliate sull'installazione e la configurazione dell'ambiente Java e Maven, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **Java** e quindi **Ubuntu** e infine seguire le istruzioni dettagliate per la configurazione di Java e Maven riportate nei passaggi 1.2, 1.3 e 1.4.

### <a name="windows"></a>**Windows**
Installare [Maven](https://maven.apache.org/download.cgi) tramite il programma di installazione ufficiale. Usare Maven per gestire le dipendenze e compilare, testare ed eseguire il progetto Java. Per indicazioni dettagliate sull'installazione e la configurazione dell'ambiente Java e Maven, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **Java** e quindi Windows e infine seguire le istruzioni dettagliate per la configurazione di Java e Maven riportate nei passaggi 1.2 e 1.3.

## <a name="create-sqldbsample-project"></a>Creare il progetto SqlDbSample

1. Nella console dei comandi (ad esempio, Bash) creare un progetto Maven. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Digitare **Y** e premere **INVIO**.
3. Passare alla directory del progetto appena creato.

   ```bash
   cd SqlDbSamples
   ```

4. Usando l'editor preferito, aprire il file pom.xml file nella cartella del progetto. 

5. Aggiungere la dipendenza di Microsoft JDBC Driver per SQL Server al progetto Maven aprendo l'editor di testo preferito e copiando e incollando le righe seguenti nel file pom.xml. Non sovrascrivere i valori esistenti già inseriti nel file. La dipendenza JDBC deve essere incollata all'interno della più estesa sezione "dependencies".   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Specificare la versione di Java in base alla quale compilare il progetto aggiungendo la sezione "properties" seguente nel file pom.xml, dopo la sezione "dependencies". 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Aggiungere la sezione "build" seguente nel file pom.xml, dopo la sezione "properties", per supportare i file manifesto in file JAR.       

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
8. Salvare e chiudere il file pom.xml.
9. Aprire il file App.java (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java) e sostituire il contenuto con il contenuto seguente. Sostituire il nome del gruppo di failover con il nome del proprio gruppo di failover. Se si sono modificati i valori per il nome del database, l'utente o la password, modificare anche tali valori.

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

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data that was previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
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
6. Salvare e chiudere il file App.java.

## <a name="compile-and-run-the-sqldbsample-project"></a>Compilare ed eseguire il progetto SqlDbSample

1. Nella console dei comandi eseguire questo comando:

   ```bash
   mvn package
   ```
2. Al termine, eseguire questo comando per eseguire l'applicazione (a meno che non venga arrestata manualmente, l'esecuzione dura circa 1 ora):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Eseguire un'esercitazione sul ripristino di emergenza

1. Chiamare il failover manuale del gruppo di failover. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Osservare i risultati dell'applicazione durante il failover. Alcuni inserimenti avranno esito negativo durante l'aggiornamento della cache DNS.     

3. Individuare il ruolo eseguito dal server di ripristino di emergenza.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Effettuare il failback.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Osservare i risultati dell'applicazione durante il failback. Alcuni inserimenti avranno esito negativo durante l'aggiornamento della cache DNS.     

6. Individuare il ruolo eseguito dal server di ripristino di emergenza.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```
## <a name="next-steps"></a>Passaggi successivi 

Per altre informazioni, vedere l'articolo relativo a [gruppi di failover e replica geografica attiva](sql-database-geo-replication-overview.md).

