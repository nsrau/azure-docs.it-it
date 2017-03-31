---
title: Connettersi al database SQL tramite PHP in Windows | Documentazione Microsoft
description: Presentazione di un programma PHP di esempio che si connette al database SQL di Azure da un client Windows e fornisce collegamenti ai componenti software necessari per il client.
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>Connettersi al database SQL tramite PHP
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Questo argomento illustra come connettersi ed eseguire query in un database SQL di Azure tramite PHP. È possibile eseguire questo esempio da Windows o Linux. 


## <a name="step-1-create-a-sql-database"></a>Passaggio 1: Creare un database SQL
Vedere la [pagina introduttiva](sql-database-get-started.md) per informazioni su come creare un database di esempio.  È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**. Dopo aver creato il database, abilitare l'accesso all'indirizzo IP attivando le regole del firewall come descritto nella [pagina introduttiva](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Passaggio 2: Configurare l'ambiente di sviluppo

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Aprire il terminale in uso e passare a una directory in cui si prevede di creare lo script python. Immettere i comandi seguenti per installare il **driver ODBC di Microsoft per Linux**, **pdo_sqlsrv** e **sqlsrv**. Il driver Microsoft PHP per SQL Server usa il driver ODBC di Microsoft su Linux per connettersi ai database SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
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

## <a name="step-3-run-sample-code"></a>Passaggio 3: Eseguire il codice di esempio
Creare un file denominato **sql_sample.php** e incollare il codice seguente all'interno. È possibile eseguire questa operazione dalla riga di comando tramite:

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>Connettersi al database SQL
Per connettersi al database SQL, viene usata la funzione [sqlsrv connect](http://php.net/manual/en/function.sqlsrv-connect.php).

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>Eseguire un'istruzione SQL SELECT
Per recuperare un set di risultati di una query sul database SQL è possibile usare la funzione [sqlsrv_query](http://php.net/manual/en/function.sqlsrv-query.php). 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";  
$getProducts = sqlsrv_query($conn, $tsql);  
if ($getProducts == FALSE)  
    die(FormatErrors(sqlsrv_errors()));  
$productCount = 0;  
while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))  
{  
    echo($row['CompanyName']);  
    echo("<br/>");  
    $productCount++;  
}  
sqlsrv_free_stmt($getProducts);  
sqlsrv_close($conn);    
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserire una riga, passare i parametri e recuperare la chiave primaria generata
Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx). 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
//Insert query  
$insertReview = sqlsrv_query($conn, $tsql);  
if($insertReview == FALSE)  
    die(FormatErrors( sqlsrv_errors()));  
echo "Product Key inserted is :";  
while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))  
{     
    echo($row['ProductID']);  
}  
sqlsrv_free_stmt($insertReview);  
sqlsrv_close($conn);  
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>Passaggi successivi
* Rivedere l'articolo [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* Altre informazioni su [Microsoft PHP Driver per SQL Server](https://github.com/Microsoft/msphpsql/)
* [Segnalazione di problemi/domande](https://github.com/Microsoft/msphpsql/issues).

## <a name="additional-resources"></a>Risorse aggiuntive
* [Schemi progettuali per applicazioni SaaS multi-tenant con il database SQL di Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Esplorare tutte le [funzionalità del database SQL](https://azure.microsoft.com/services/sql-database/)

