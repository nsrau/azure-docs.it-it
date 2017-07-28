---
title: Apache Sqoop con Hadoop - Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Apache Sqoop per importare ed esportare tra Hadoop in HDInsight e un database SQL di Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
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
ms.date: 04/14/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 74f34bdbf5707510c682814716aa0b95c19a5503
ms.openlocfilehash: 47fc62c767230f56c88a453fce168d74eb762a50
ms.contentlocale: it-it
ms.lasthandoff: 06/09/2017


---
# <a name="use-apache-sqoop-to-import-and-export-data-between-hadoop-on-hdinsight-and-sql-database"></a>Usare Apache Sqoop per importare ed esportare dati tra Hadoop su HDInsight e un database SQL

[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Apache Sqoop per eseguire importazioni ed esportazioni tra un cluster Hadoop in Azure HDInsight e un database SQL di Azure o un database Microsoft SQL Server. La procedura descritta in questo documento usa il comando `sqoop` direttamente dal nodo head del cluster Hadoop. Usare SSH per connettersi al nodo head ed eseguire i comandi di questo documento.

> [!IMPORTANT]
> I passaggi descritti in questo documento funzionano solo con i cluster HDInsight che usano Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-retirement-date).

## <a name="install-freetds"></a>Installare FreeTDS

1. Connettersi al cluster HDInsight usando SSH. Ad esempio, il comando seguente si connette al nodo head primario di un cluster denominato `mycluster`:

    ```bash
    ssh CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Immettere il comando seguente per installare FreeTDS:

    ```bash
    sudo apt install --assume-yes install freetds-dev freetds-bin
    ```

    FreeTDS viene usato in diversi passaggi per connettersi al database SQL.

## <a name="create-the-table-in-sql-database"></a>Creare la tabella nel database SQL

> [!IMPORTANT]
> Se si usa il cluster HDInsight e il database SQL creato in [Creare cluster e database SQL](hdinsight-use-sqoop.md), saltare i passaggi descritti in questa sezione. Il database e la tabella sono stati creati come parte della procedura del documento [Creare cluster e database SQL](hdinsight-use-sqoop.md).

1. Nella sessione SSH usare il comando seguente per connettersi al server del database SQL.

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    L'output che si riceve è simile al testo seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

2. Al prompt di `1>` immettere la query seguente:

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

    Dopo aver immesso l'istruzione `GO`, vengono valutate le istruzioni precedenti. Innanzitutto, verrà creata la tabella **mobiledata** a cui verrà aggiunto un indice cluster (richiesto dal Database SQL).

    Per verificare la corretta creazione della tabella, usare la query seguente:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    L'output sarà simile al seguente testo:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

3. Per uscire dall'utilità tsql, immettere `exit` al prompt di `1>`.

## <a name="sqoop-export"></a>Esportazione con Sqoop

1. Dalla connessione SSH al cluster, usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>
    ```

    Questo comando restituisce un elenco di database, compreso il database **sqooptest** creato in precedenza.

2. Per esportare dati dalla tabella **hivesampletable** alla tabella **mobiledata**, usare il comando seguente:

    ```bash
    sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1
    ```

    Questo comando indica a Sqoop di connettersi al database **sqooptest**. Sqoop esporta quindi i dati da **wasbs:///hive/warehouse/hivesampletable** nella tabella **mobiledata**.

3. Al termine dell'esecuzione del comando, usare il comando seguente per connettersi al database tramite TSQL:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest
    ```

    Una volta connessi, utilizzare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella **mobiledata** :

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

## <a name="sqoop-import"></a>Importazione con Sqoop

1. Usare il comando seguente per importare i dati dalla tabella **mobiledata** nel database SQL alla directory **wasbs:///tutorials/usesqoop/importeddata** in HDInsight:

    ```bash
    sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

    I campi nei dati sono separati da un carattere di tabulazione e le righe terminano con un carattere di nuova riga.

2. Una volta completata l'importazione, usare il comando seguente per elencare i dati della nuova directory:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="using-sql-server"></a>Uso di SQL Server

È inoltre possibile usare Sqoop per importare ed esportare dati da SQL Server nel data center o in una macchina virtuale ospitata in Azure. Le differenze tra l'uso del database SQL e SQL Server sono:

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure.

    Quando si usa SQL Server nel proprio data center, è necessario configurare la rete virtuale come *da sito a sito* o *da punto a sito*.

  > [!NOTE]
  > Quando si usa una rete virtuale **da punto a sito**, SQL Server deve eseguire l'applicazione di configurazione del client VPN. Il client VPN è disponibile nel **Dashboard** della configurazione di rete virtuale di Azure.

    Per altre informazioni sull'uso di HDInsight con le reti virtuali di Azure, vedere il documento [Estendere HDInsight con la rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md). Per altre informazioni su Rete virtuale di Azure, vedere il documenti di [panoramica sulle reti virtuali](../virtual-network/virtual-networks-overview.md).

* SQL Server deve essere configurato per consentire l'autenticazione SQL. Per altre informazioni, consultare il documento [Scegliere una modalità di autenticazione](https://msdn.microsoft.com/ms144284.aspx).

* Potrebbe essere necessario configurare SQL Server affinché accetti le connessioni remote. Per altre informazioni, vedere il documento [Come risolvere i problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* Creare il database **sqooptest** in SQL Server usando un'utilità, ad esempio **SQL Server Management Studio** o **tsql**. La procedura per usare l'interfaccia della riga di comando di Azure funzionano solo per il database SQL di Azure.

    Usare le seguenti istruzioni Transact-SQL per creare la tabella **mobiledata**:

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
    sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

## <a name="limitations"></a>Limitazioni

* Esportazione di massa: con HDInsight basato su Linux, attualmente il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti di massa.

* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Oozie con HDInsight][hdinsight-use-oozie]: usare un'azione di Sqoop nel flusso di lavoro di Oozie.
* [Analizzare i dati sui ritardi dei voli con HDInsight][hdinsight-analyze-flight-data]: usare Hive per analizzare i dati sui ritardi dei voli e quindi usare Sqoop per esportare dati in un database SQL di Azure.
* [Caricare dati in HDInsight][hdinsight-upload-data]: altri metodi per caricare dati in HDInsight e nell'archivio BLOB di Azure.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

