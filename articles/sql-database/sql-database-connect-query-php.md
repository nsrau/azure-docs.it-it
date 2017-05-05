---
title: Connettersi al database SQL di Azure tramite PHP | Documentazione Microsoft
description: Presenta un esempio di codice PHP da usare per connettersi al database SQL di Azure ed eseguire query.
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: d4d21297618d34aa301e4e1cc814afb15045d7f7
ms.lasthandoff: 04/21/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Database SQL di Azure: usare PHP per connettersi ed eseguire query sui dati

Questa guida introduttiva illustra come usare [PHP](http://php.net/manual/en/intro-whatis.php) per connettersi a un database SQL di Azure e quindi usare istruzioni Transact-SQL per eseguire query e inserire, aggiornare ed eliminare dati nel database da piattaforme Mac OS, Ubuntu Linux e Windows.

Questa guida introduttiva usa come punto di partenza le risorse create in una delle guide introduttive seguenti:

- [Creare un database: portale](sql-database-get-started-portal.md)
- [Creare un database: interfaccia della riga di comando](sql-database-get-started-cli.md)

## <a name="install-php-and-database-communications-software"></a>Installare PHP e il software di comunicazione del database

Le procedure descritte in questa sezione presuppongono che si abbia familiarità con lo sviluppo tramite PHP ma non con il database SQL di Azure. Se non si ha esperienza con lo sviluppo con PHP, andare alla pagina [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Creare un'app con SQL Server), selezionare **PHP** e quindi il sistema operativo in uso.

### <a name="mac-os"></a>**Mac OS**
Aprire il terminale e immettere i comandi seguenti per installare **brew**, il **driver ODBC di Microsoft per Mac** e **driver Microsoft PHP per SQL Server**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Immettere i comandi seguenti per installare il **driver ODBC di Microsoft per Linux**, e il **Driver Microsoft PHP per SQL Serve**.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Installare PHP 7.1.1 (x64) [dall'installazione guidata della piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Installare il [driver ODBC13.1 di Microsoft](https://www.microsoft.com/download/details.aspx?id=53339). 
- Scaricare la dll non thread-safe per il [driver Microsoft PHP per SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) e inserire i file binari nella cartella PHP\v7.x\ext.
- Modificare quindi il file php.ini (C:\Programmi\PHP\v7.1\php.ini) aggiungendo il riferimento alla dll. Ad esempio:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

A questo punto si disporrà della dll registrata con PHP.

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al database SQL di Azure. Nelle procedure successive saranno necessari il nome completo del server, il nome del database e le informazioni di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nella pagina **Panoramica** per il database, verificare il nome completo del server, come mostrato nell'immagine seguente. È possibile passare il puntatore sul nome del server per visualizzare l'opzione **Fare clic per copiare**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se si sono dimenticate le informazioni di accesso per il server del database SQL di Azure, passare alla pagina del server del database SQL per visualizzare il nome dell'amministratore del server e, se necessario, reimpostare la password.     
    
## <a name="select-data"></a>Selezionare i dati
Usare il codice seguente per eseguire una query per individuare i primi 20 prodotti per categoria tramite la funzione [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) con un'istruzione Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). Per recuperare un set di risultati da una query su un database SQL si usa la funzione sqlsrv_query. Questa funzione accetta una query e restituisce un set di risultati su cui è possibile eseguire l'iterazione tramite [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>Inserire dati
Usare il codice seguente per inserire un nuovo prodotto nella tabella SalesLT.Product tramite la funzione [sqlsrv_query ()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e l'istruzione Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>Aggiornare i dati
Usare il codice seguente per aggiornare i dati nel database SQL di Azure tramite la funzione [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e l'istruzione Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>Eliminare i dati
Usare il codice seguente per eliminare il nuovo prodotto aggiunto in precedenza usando la funzione [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e l'istruzione Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Sostituire il server, il database, il nome utente, e i parametri della password con i valori specificati al momento della creazione del database con i dati di esempio AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su [Microsoft PHP Driver per SQL Server](https://github.com/Microsoft/msphpsql/).
- [Segnalazione di problemi/domande](https://github.com/Microsoft/msphpsql/issues).
- Per connettersi ed effettuare una query usando SQL Server Management Studio, vedere [Connettersi ed eseguire una query con SSMS](sql-database-connect-query-ssms.md)
- Per connettersi ed eseguire query usando Visual Studio, vedere [Connettersi ed eseguire query con Visual Studio Code](sql-database-connect-query-vscode.md).
- Per connettersi ed eseguire query usando .NET, vedere [Connettersi ed eseguire query con .NET](sql-database-connect-query-dotnet.md).
- Per connettersi ed eseguire query usando Node.js, vedere [Connettersi ed eseguire query con Node.js](sql-database-connect-query-nodejs.md).
- Per connettersi ed eseguire query usando Java, vedere [Connettersi ed eseguire query con Java](sql-database-connect-query-java.md).
- Per connettersi ed eseguire query usando Python, vedere [Connettersi ed eseguire query con Python](sql-database-connect-query-python.md).
- Per connettersi ed eseguire query usando Ruby, vedere [Connettersi ed eseguire query con Ruby](sql-database-connect-query-ruby.md).

