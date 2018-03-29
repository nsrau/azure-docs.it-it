---
title: Apache Sqoop con Hadoop - Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Apache Sqoop per importare ed esportare tra Hadoop in HDInsight e un database SQL di Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: ''
author: Blackmist
tags: azure-portal
keywords: hadoop sqoop, sqoop
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: f98345c012b8507818d0218fe43bc29badf0d4c8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2018
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Usare Apache Sqoop per importare ed esportare dati tra Hadoop su HDInsight e un database SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Apache Sqoop per eseguire importazioni ed esportazioni tra un cluster Hadoop in Azure HDInsight e un database SQL di Azure o un database Microsoft SQL Server. La procedura descritta in questo documento usa il comando `sqoop` direttamente dal nodo head del cluster Hadoop. Usare SSH per connettersi al nodo head ed eseguire i comandi di questo documento.

> [!IMPORTANT]
> I passaggi descritti in questo documento funzionano solo con i cluster HDInsight che usano Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

> [!WARNING]
> Le procedure in questo documento presuppongono che si sia già creato un database SQL di Azure denominato `sqooptest`.
>
> Questo documento fornisce le istruzioni SQL usate per creare una tabella nel database SQL ed eseguire una query su di essa. Esistono molti client in cui è possibile usare queste istruzioni con il database SQL. È consigliabile usare i client seguenti:
>
> * [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md)
> * [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md)
> * Utilità [sqlcmd](https://docs.microsoft.com/sql/tools/sqlcmd-utility)

## <a name="create-the-table-in-sql-database"></a>Creare la tabella nel database SQL

> [!IMPORTANT]
> Se si usa il cluster HDInsight e il database SQL creato in [Creare cluster e database SQL](hdinsight-use-sqoop.md), saltare i passaggi descritti in questa sezione. Il database e la tabella sono stati creati come parte della procedura del documento [Creare cluster e database SQL](hdinsight-use-sqoop.md).

Usare un client SQL per connettersi al database `sqooptest` nel database SQL. Usare quindi il codice T-SQL seguente per creare una tabella denominata `mobiledata`:

```sql
CREATE TABLE [dbo].[mobiledata](
[clientid] [nvarchar](50),
[querytime] [nvarchar](50),
[market] [nvarchar](50),
[deviceplatform] [nvarchar](50),
[devicemake] [nvarchar](50),
[devicemodel] [nvarchar](50),
[state] [nvarchar](50),
[country] [nvarchar](50),
[querydwelltime] [float],
[sessionid] [bigint],
[sessionpagevieworder] [bigint])
GO
CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)
GO
```

## <a name="sqoop-export"></a>Esportazione con Sqoop

1. Connettersi al cluster HDInsight usando SSH. Ad esempio, il comando seguente si connette al nodo head primario di un cluster denominato `mycluster`:

    ```bash
    ssh mycluster-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Per verificare che Sqoop possa visualizzare il database SQL, usare il comando seguente:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> -P
    ```
    Quando richiesto, immettere la password per l’accesso al database SQL.

    Questo comando restituisce un elenco di database, compreso il database **sqooptest** usato in precedenza.

3. Per esportare dati dalla tabella **hivesampletable** di Hive alla tabella **mobiledata** nel database SQL, usare il comando seguente:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Per verificare che i dati siano stati esportati, usare la query seguente dal client SQL per visualizzare i dati esportati:

    ```sql
    SET ROWCOUNT 50;
    SELECT * FROM mobiledata;
    ```

    Questo comando elenca 50 righe che sono state importate nella tabella.

## <a name="sqoop-import"></a>Importazione con Sqoop

1. Usare il comando seguente per importare i dati dalla tabella **mobiledata** nel database SQL alla directory **wasb:///tutorials/usesqoop/importeddata** in HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    I campi nei dati sono separati da un carattere di tabulazione e le righe terminano con un carattere di nuova riga.

    > [!IMPORTANT]
    > Il percorso `wasb:///` funziona con i cluster che usano Archiviazione di Azure come risorsa di archiviazione cluster predefinita. Per i cluster che usano Azure Data Lake Store, usare invece `adl:///`.

2. Una volta completata l'importazione, usare il comando seguente per elencare i dati della nuova directory:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Uso di SQL Server

È anche possibile usare Sqoop per importare ed esportare dati da SQL Server. Le differenze tra l'uso del database SQL e SQL Server sono:

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure.

    Per avere un esempio, vedere il documento [Connettere HDInsight alla rete locale](./../connect-on-premises-network.md).

    Per altre informazioni sull'uso di HDInsight con le reti virtuali di Azure, vedere il documento [Estendere HDInsight con la rete virtuale di Azure](../hdinsight-extend-hadoop-virtual-network.md). Per altre informazioni su Rete virtuale di Azure, vedere il documenti di [panoramica sulle reti virtuali](../../virtual-network/virtual-networks-overview.md).

* SQL Server deve essere configurato per consentire l'autenticazione SQL. Per altre informazioni, consultare il documento [Scegliere una modalità di autenticazione](https://msdn.microsoft.com/ms144284.aspx).

* Potrebbe essere necessario configurare SQL Server affinché accetti le connessioni remote. Per altre informazioni, vedere il documento [Come risolvere i problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Usare le seguenti istruzioni Transact-SQL per creare la tabella **mobiledata**:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder] [bigint])
    ```

* Quando ci si connette a SQL Server da HDInsight, è necessario usare l'indirizzo IP del Server SQL. Ad esempio: 

    ```bash
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> -P -table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitazioni

* Esportazione in blocco: con HDInsight basato su Linux, il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti bulk.

* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Oozie con HDInsight](../hdinsight-use-oozie.md): usare un'azione di Sqoop nel flusso di lavoro di Oozie.
* [Analizzare i dati sui ritardi dei voli usando HDInsight](../hdinsight-analyze-flight-delay-data.md): usare Hive nell'analisi dei dati sui ritardi dei voli e quindi usare Sqoop per esportare dati nel database SQL di Azure.
* [Caricare i dati in HDInsight](../hdinsight-upload-data.md): per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.

[hdinsight-versions]:  ../hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
