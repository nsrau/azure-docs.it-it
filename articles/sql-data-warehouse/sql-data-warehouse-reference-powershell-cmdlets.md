<properties
   pageTitle="Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse"
   description="Sospendere e riavviare SQL Data Warehouse usando i cmdlet di PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/03/2016"
   ms.author="barbkess;mausher;sonyama"/>

# Usare i cmdlet di PowerShell e le API REST con SQL Data Warehouse

SQL Data Warehouse può essere gestito tramite i cmdlet di Azure PowerShell o le API REST.

I comandi definiti per il **database SQL di Azure** vengono usati anche per **SQL Data Warehouse**. Per un elenco aggiornato, vedere l'articolo relativo ai [cmdlet di Azure SQL](https://msdn.microsoft.com/library/mt574084.aspx). I cmdlet **Suspend-AzureRmSqlDatabase** e **Resume-AzureRmSqlDatabase** illustrati di seguito sono delle aggiunte progettate per SQL Data Warehouse.

Allo stesso modo, le API REST per il **database di Azure SQL** possono essere usate anche per le istanze di **SQL Data Warehouse**. Per l'elenco aggiornato, vedere [Operazioni per i database SQL di Azure](https://msdn.microsoft.com/library/azure/dn505719.aspx).

## Ottenere ed eseguire i cmdlet di Azure PowerShell

1. Per scaricare il modulo di Azure PowerShell, eseguire l'[Installazione guidata piattaforma Web Microsoft](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409).
2. Per eseguire il modulo, nella finestra di avvio digitare **Windows PowerShell**.
3. Se non si è ancora provveduto ad aggiungere il proprio account al computer, eseguire il cmdlet seguente. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell]().

	```
	Login-AzureRmAccount
	```

3. Selezionare la sottoscrizione per il database che si desidera sospendere o riprendere. In questo modo si seleziona la sottoscrizione denominata "MySubscription".

	```
	Select-AzureRmSubscription -SubscriptionName "MySubscription"
	```

## Suspend-AzureRmSqlDatabase

Per i riferimenti ai comandi, vedere [Suspend-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619337.aspx).

### Esempio 1: Sospensione di un database per nome in un server

Questo esempio sospende un database denominato "Database02" ospitato in un server denominato "Server01". Il server si trova in un gruppo di risorse di Azure denominato "ResourceGroup1".

```
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
```

### Esempio 2: Sospensione di un oggetto di database

Questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". In questo modo si invia tramite pipe l'oggetto recuperato a **Suspend-AzureRmSqlDatabase**. Il database viene pertanto sospeso. Il comando finale mostra i risultati.

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

## Resume-AzureSqlDatabase

Per i riferimenti ai comandi, vedere [Resume-AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt619347.aspx)

### Esempio 1: Ripresa di un database per nome in un server

Questo esempio fa riprendere le operazioni di un database denominato "Database02" ospitato in un server denominato "Server01". Il server è incluso in un gruppo di risorse denominato "ResourceGroup1".

```
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" -DatabaseName "Database02"
```

### Esempio 2: Ripresa di un oggetto di database

Questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". L'oggetto viene inviato tramite pipe a **Resume-AzureRmSqlDatabase**.

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
```

## Get-AzureRmSqlDatabaseRestorePoints

Questo cmdlet elenca i punti di ripristino di backup per un database Azure SQL Data Warehouse. I punti di ripristino vengono usati per ripristinare il database. Le proprietà dell'oggetto restituito sono le seguenti.

Proprietà|Descrizione
---|---
RestorePointType|DISCRETE/CONTINUOUS. I punti di ripristino discreti descrivono i momenti possibili a cui può essere riportato un database Azure SQL Data Warehouse tramite il ripristino. I punti di ripristino continui descrivono i primi momenti possibili a cui può essere riportato un database SQL di Azure tramite il ripristino. Il database può essere riportato a qualsiasi momento dopo il primo punto.
EarliestRestoreDate|Ora del primo ripristino (popolato quando restorePointType = CONTINUOUS)
RestorePointCreationDate |Ora dello snapshot di backup (popolato quando restorePointType = DISCRETE)

### Esempio 1: Recupero dei punti di ripristino di un database per nome in un server
Questo esempio recupera i punti di ripristino per un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1".

```
$restorePoints = Get-AzureRmSqlDatabaseRestorePoints –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints
```


### Esempio 2: Ripresa di un oggetto di database

Questo esempio recupera un database denominato "Database02" da un server denominato "Server01" incluso in un gruppo di risorse denominato "ResourceGroup1". L'oggetto di database viene inviato tramite pipe a **Get-AzureRmSqlDatabase** e come risultato si ottengono i punti di ripristino del database. Il comando finale stampa i risultati.

```
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" –ServerName "Server01" –DatabaseName "Database02"
$restorePoints = $database | Get-AzureRmSqlDatabaseRestorePoints
$retorePoints
```


> [AZURE.NOTE] Se il server è foo.database.windows.net, usare "foo" come nome server nei cmdlet di PowerShell.


## Passaggi successivi
Per altre informazioni di riferimento, vedere la [panoramica degli argomenti di riferimento di SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[panoramica degli argomenti di riferimento di SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[How to install and configure Azure PowerShell]: ../articles/powershell-install-configure.md

<!--MSDN references-->


<!--Other Web references-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/

<!---HONumber=AcomDC_0309_2016-->