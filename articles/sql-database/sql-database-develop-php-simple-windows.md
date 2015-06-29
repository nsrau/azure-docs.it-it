<properties
	pageTitle="PHP su Windows al database SQL | Microsoft Azure"
	description="Presentazione di un programma PHP di esempio che si connette al database SQL di Azure da un client Windows e fornisce collegamenti ai componenti software necessari per il client."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="mebha"/>


# Connettersi al database SQL tramite PHP in Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Questo argomento illustra come connettersi al database SQL di Azure da un'applicazione client scritta in PHP che viene eseguita in Windows.


## Prerequisiti


Per eseguire l'esempio di codice PHP fornito in questo argomento, nel computer client devono essere installati gli elementi software seguenti:


- [Driver Microsoft per PHP per Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) \(SQLSRV32.EXE contiene i bit più recenti\)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- [Driver Microsoft ODBC](https://www.microsoft.com/it-it/download/details.aspx?id=36434)
- IIS Express
- [5\.6 PHP per IIS Express](http://www.microsoft.com/web/downloads/platform.aspx): scaricare usando l'installazione guidata piattaforma. Assicurarsi di usare Internet Explorer per scaricare il programma di installazione piattaforma

Per informazioni su come installare e configurare i requisiti menzionati, vedere il [blog del team](http://blogs.msdn.com/b/sqlphp/archive/2015/05/11/getting-started-with-php-and-microsoft-sql-server.aspx) e il [video](https://www.youtube.com/watch?v=0oCjiRK_tUk).


## Creare un database e recuperare la stringa di connessione


Vedere l'[argomento introduttivo](sql-database-get-started.md) per informazioni su come creare un database di esempio e recuperare la stringa di connessione. È importante seguire le istruzioni per creare un **modello di database AdventureWorks**. Gli esempi illustrati di seguito funzionano solo con lo **schema di AdventureWorks**.


## Connettersi al database SQL


Questa funzione **OpenConnection** viene chiamata nella parte superiore in tutte le funzioni che seguono.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Eseguire una query e recuperare il set di risultati

Per recuperare un set di risultati di una query sul database SQL è possibile usare la funzione [sqlsrv\_query\(\)](http://php.net/manual/en/function.sqlsrv-query.php). Questa funzione accetta essenzialmente qualsiasi query e l'oggetto connection e restituisce un set di risultati su cui è possibile eseguire l'iterazione usando [sqlsrv\_fetch\_array\(\)](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
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
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}
	

## Inserire una riga, passare i parametri e recuperare la chiave primaria generata


Nel database SQL, per generare automaticamente i valori di [chiave primaria](https://msdn.microsoft.com/library/ms179610.aspx), è possibile usare la proprietà [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e l'oggetto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx).


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
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
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## Transazioni


Questo esempio di codice illustra l'uso di transazioni con le operazioni seguenti:

\-Avvio di una transazione

\-Inserimento di una riga di dati, aggiornamento di un'altra riga di dati

\-Commit della transazione se l'inserimento e l'aggiornamento vengono eseguiti correttamente e il rollback della transazione se una di tali operazioni non è riuscita


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice) 
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);
			
			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);
			
			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Altre informazioni


Per altre informazioni sull'installazione e l'uso di PHP, vedere [Accesso ai database di SQL Server con PHP](http://technet.microsoft.com/library/cc793139.aspx).

 

<!---HONumber=58_postMigration-->