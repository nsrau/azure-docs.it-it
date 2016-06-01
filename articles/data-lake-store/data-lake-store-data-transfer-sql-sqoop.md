<properties 
   pageTitle="Copiare i dati tra Archivio Data Lake e il database SQL di Azure tramite Sqoop | Microsoft Azure"
   description="Usare Sqoop per copiare i dati tra il database SQL di Azure e Archivio Data Lake" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/11/2016"
   ms.author="nitinme"/>

# Copiare i dati tra Archivio Data Lake e un database SQL di Azure tramite Sqoop

Informazioni su come usare Apache Sqoop per importare ed esportare dati tra un database SQL di Azure e Archivio Data Lake.
 

## Informazioni su Sqoop

Le applicazioni Big Data sono una scelta naturale per l'elaborazione di dati non strutturati e semi-strutturati, ad esempio log e file. Tuttavia, potrebbe anche essere necessario elaborare i dati strutturati archiviati nei database relazionali.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) è uno strumento progettato per trasferire dati tra database relazionali e un repository Big Data, ad esempio Archivio Data Lake. È possibile usarlo per importare dati da un sistema di gestione di database relazionali (RDBMS), ad esempio un database SQL di Azure, in Archivio Data Lake. È quindi possibile trasformare e analizzare i dati mediante carichi di lavoro Big Data e quindi esportarli nuovamente in un sistema RDBMS. In questa esercitazione viene usato un database SQL di Azure come database relazionale da cui importare/esportare dati.
 

## Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Abilitare la sottoscrizione di Azure** per l'anteprima pubblica di Data Lake Store. Vedere le [istruzioni](data-lake-store-get-started-portal.md#signup). 
- **Cluster Azure HDInsight** con accesso a un account di Archivio Data Lake. Vedere [Creare un cluster HDInsight con Archivio Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md). Questo articolo presuppone che si abbia un cluster HDInsight Linux con accesso ad Archivio Data Lake.
- **Database SQL di Azure**. Per istruzioni su come crearne uno, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started.md).

## Apprendimento rapido con i video

[Guardare questo video](https://mix.office.com/watch/1butcdjxmu114) su come copiare dati tra i BLOB di archiviazione di Azure e Archivio Data Lake con DistCp.

## Creare tabelle di esempio nel database SQL di Azure

1. Per iniziare, creare due tabelle di esempio nel database SQL di Azure. Usare [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio per connettersi al database SQL di Azure e quindi eseguire le query seguenti.

	**Create Table1**

		CREATE TABLE [dbo].[Table1]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

	**Create Table2**

		CREATE TABLE [dbo].[Table2]( 
	    [ID] [int] NOT NULL, 
	    [FName] [nvarchar](50) NOT NULL, 
	    [LName] [nvarchar](50) NOT NULL, 
	 	CONSTRAINT [PK_Table_4] PRIMARY KEY CLUSTERED 
			( 
		   		[ID] ASC 
			) 
		) ON [PRIMARY] 
		GO

2. In **Table1** aggiungere alcuni dati di esempio. Lasciare vuota **Table2**. Verranno importati dati da **Table1** ad Archivio Data Lake. Quindi, verranno esportati dati da Archivio Data Lake a **Table2**. Eseguire il frammento di codice seguente.

		 
		INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson'); 
  

## Usare Sqoop da un cluster HDInsight con accesso ad Archivio Data Lake

In un cluster HDInsight sono già disponibili i pacchetti di Sqoop. Se il cluster HDInsight è stato configurato per l'uso di Archivio Data Lake come archiviazione aggiuntiva, è possibile usare Sqoop (senza apportare modifiche alla configurazione) per importare o esportare dati tra un database relazionale (in questo esempio, un database SQL di Azure) e un account Archivio Data Lake.

1. Per questa esercitazione si presuppone che sia stato creato un cluster Linux, quindi è consigliabile usare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).

2. Verificare se è possibile accedere all'account Archivio Data Lake dal cluster. Eseguire il comando seguente dal prompt SSH:

		
		hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

	Dovrebbe essere visualizzato un elenco di file/cartelle nell'account Archivio Data Lake.

### Importare dati da un database SQL di Azure ad Archivio Data Lake

3. Passare alla directory in cui sono disponibili i pacchetti di Sqoop. In genere, questa corrisponde a `/usr/hdp/<version>/sqoop/bin`. 

4. Importare i dati da **Table1** all'account di Archivio Data Lake. Usare la sintassi seguente:

		
		sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

	Si noti che il segnaposto **sql-database-server-name** rappresenta il nome del server in cui è in esecuzione il database SQL di Azure. Il segnaposto **sql-database-name** rappresenta il nome effettivo del database.

	Ad esempio,

		
		sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

5. Verificare che i dati siano stati trasferiti all'account Archivio Data Lake. Eseguire il comando seguente:

		
		hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

	Viene visualizzato l'output seguente.

		
		-rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
		-rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
		-rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
		-rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
		-rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

	Ogni file **part-m-*** corrisponde a una riga nella tabella di origine, **Table1**. È possibile visualizzare i contenuti dei file part-m-* per la verifica.


### Esportare dati da Archivio Data Lake a un database SQL di Azure

6. Esportare i dati dall'account di Archivio Data Lake alla tabella vuota, **Table2**, nel database SQL di Azure. Usare la sintassi seguente.

		
		sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

	Ad esempio,

		
		sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

6. Verificare che i dati siano stati caricati nella tabella del database SQL. Usare [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio per connettersi al database SQL di Azure e quindi eseguire la query seguente.

		
		SELECT * FROM TABLE2

	L'output della query corrisponde al seguente.

	 	ID  FName   LName
		------------------
		1	Neal	Kell
		2	Lila	Fulton
		3	Erna	Myers
		4	Annette	Simpson

## Vedere anche

- [Copiare i dati da BLOB di archiviazione di Azure ad Archivio Data Lake](data-lake-store-copy-data-azure-storage-blob.md)
- [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
- [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0518_2016-->