---
title: Copiare i dati tra Data Lake Storage Gen1 e Azure SQL - Sqoop Documenti Microsoft
description: Usare Sqoop per copiare i dati tra il database SQL di Azure e Azure Data Lake Storage Gen1
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839067"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Copiare i dati tra Data Lake Storage Gen1 e il database SQL di Azure usando SqoopCopy data between Data Lake Storage Gen1 and Azure SQL Database using Sqoop

Informazioni su come usare Apache Sqoop per importare ed esportare dati tra un database SQL di Azure e Azure Data Lake Storage Gen1.

## <a name="what-is-sqoop"></a>Informazioni su Sqoop

Le applicazioni Big Data sono una scelta naturale per l'elaborazione di dati non strutturati e semi-strutturati, ad esempio log e file. Tuttavia, potrebbe anche essere necessario elaborare i dati strutturati archiviati in database relazionali.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) è uno strumento progettato per trasferire dati tra database relazionali e un repository Big Data, ad esempio Data Lake Storage Gen1. È possibile usarlo per importare dati da un sistema di gestione di database relazionali (RDBMS), ad esempio un database SQL di Azure, in Data Lake Storage Gen1. È quindi possibile trasformare e analizzare i dati usando carichi di lavoro Big Data e quindi esportarli nuovamente in un sistema RDBMS. In questo articolo si usa un database SQL di Azure come database relazionale da cui importare/esportare.

## <a name="prerequisites"></a>Prerequisiti

Per eseguire le procedure descritte è necessario:

* **Una sottoscrizione di Azure.** Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come creare l'account, vedere Introduzione ad [Azure Data Lake Storage Gen1For](data-lake-store-get-started-portal.md) instructions on how to create the account, see Get started with Azure Data Lake Storage Gen1
* **Cluster HDInsight di Azure** con accesso a un account Data Lake Storage Gen1. Vedere [Creare un cluster HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Questo articolo presuppone un cluster HDInsight Linux con accesso a Data Lake Storage Gen1.
* **Database SQL di Azure**. Per istruzioni su come crearne uno, vedere [Creare un database SQL di Azure](../sql-database/sql-database-get-started.md)

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Creare tabelle di esempio nel database SQL di AzureCreate sample tables in the Azure SQL database

1. Per iniziare, creare due tabelle di esempio nel database SQL di Azure.To start, create two sample tables in the Azure SQL database. Utilizzare [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio per connettersi al database e quindi eseguire le query seguenti.

    **Create Table1**

       CREATE TABLE [dbo].[Table1](
       [ID] [int] NOT NULL,
       [FName] [nvarchar](50) NOT NULL,
       [LName] [nvarchar](50) NOT NULL,
        CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
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
        CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
           (
                  [ID] ASC
           )
       ) ON [PRIMARY]
       GO

1. Eseguire il comando seguente per aggiungere alcuni dati di esempio a **Table1**. Lasciare vuota **Table2** . Successivamente, importerai i dati da **Table1** in Data Lake Storage Gen1. Quindi, si esserverranno i dati da Data Lake Storage Gen1 in **Table2**.

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Usare Sqoop da un cluster HDInsight con accesso a Data Lake Storage Gen1

In un cluster HDInsight sono già disponibili i pacchetti di Sqoop. Se il cluster HDInsight è stato configurato per l'uso di Data Lake Storage Gen1 come spazio di archiviazione aggiuntivo, è possibile usare Sqoop (senza modifiche di configurazione) per importare/esportare dati tra un database relazionale, ad esempio un database SQL di Azure, e un account Data Lake Storage Gen1.

1. Per questo articolo, si presuppone che sia stato creato un cluster Linux in modo da utilizzare SSH per connettersi al cluster. Vedere [Connettersi a un cluster HDInsight basato su Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Analogamente, verificare se è possibile accedere all'account di Data Lake Storage Gen1 dal cluster. Eseguire il comando seguente dal prompt SSH:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Questo comando fornisce un elenco di file/cartelle nell'account Data Lake Storage Gen1.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Importare dati da un database SQL di Azure a Data Lake Storage Gen1

1. Passare alla directory in cui sono disponibili i pacchetti di Sqoop. In genere, `/usr/hdp/<version>/sqoop/bin`questa posizione è .

1. Importare i dati da **Table1** all'account di Data Lake Storage Gen1. Usare la sintassi seguente:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Il segnaposto **sql-database-server-name** rappresenta il nome del server in cui è in esecuzione il database SQL di Azure. **sql-database-name** rappresenta il nome effettivo del database.

   Ad esempio,

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Verificare che i dati siano stati trasferiti all'account Data Lake Storage Gen1. Eseguire il comando seguente:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   Viene visualizzato l'output seguente.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Ogni file **part-m-*** corrisponde a una riga nella tabella di origine, **Table1**. È possibile visualizzare i contenuti dei file part-m-* per la verifica.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Esportare dati da Data Lake Storage Gen1 a un database SQL di Azure

1. Esportare i dati dall'account di Data Lake Storage Gen1 alla tabella vuota, **Table2**, nel database SQL di Azure. Usare la sintassi seguente.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Ad esempio,

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Verificare che i dati siano stati caricati nella tabella del database SQL. Usare [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) o Visual Studio per connettersi al database SQL di Azure e quindi eseguire la query seguente.

       SELECT * FROM TABLE2

   Questo comando dovrebbe avere il seguente output.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Considerazioni sulle prestazioni per l'uso di Sqoop

Per informazioni sull'ottimizzazione delle prestazioni del processo Sqoop per copiare i dati in Data Lake Storage Gen1, vedere il post di [blog sulle prestazioni di Sqoop.](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)

## <a name="next-steps"></a>Passaggi successivi

* [Copiare i dati dai BLOB di Archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
