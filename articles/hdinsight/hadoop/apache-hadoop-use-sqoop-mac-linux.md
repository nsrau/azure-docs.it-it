---
title: Apache Sqoop con Apache Hadoop - Azure HDInsight
description: Informazioni su come usare Apache Sqoop per importare ed esportare tra Apache Hadoop in HDInsight e un database SQL di Azure.
keywords: hadoop sqoop, sqoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 6dcb6853daf34fede590011d165c0ba9001cbac6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128975"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>Usare Apache Sqoop per importare ed esportare dati tra Apache Hadoop su HDInsight e un database SQL

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Apache Sqoop per eseguire importazioni ed esportazioni tra un cluster Apache Hadoop in Azure HDInsight e un database SQL di Azure o un database Microsoft SQL Server. La procedura descritta in questo documento usa il comando `sqoop` direttamente dal nodo head del cluster Hadoop. Usare SSH per connettersi al nodo head ed eseguire i comandi di questo documento. Questo articolo è una continuazione del [usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

## <a name="prerequisites"></a>Prerequisiti

* Completamento della [configurare l'ambiente di testing](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) dalla [usare Apache Sqoop con Hadoop in HDInsight](./hdinsight-use-sqoop.md).

* Un client di eseguire query sul database SQL di Azure. È consigliabile usare [SQL Server Management Studio](../../sql-database/sql-database-connect-query-ssms.md) oppure [Visual Studio Code](../../sql-database/sql-database-connect-query-vscode.md).

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sqoop-export"></a>Esportazione con Sqoop

Da Hive a SQL Server.

1. Connettersi al cluster HDInsight usando SSH. Sostituire `CLUSTERNAME` con il nome del cluster, quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Sostituire `MYSQLSERVER` con il nome dell'istanza di SQL Server. Per verificare che Sqoop possa visualizzare il Database SQL, immettere il comando seguente nella finestra di aprire una connessione SSH. Immettere la password per l'account di accesso di SQL Server quando richiesto. Questo comando restituisce un elenco di database.

    ```bash
    sqoop list-databases --connect jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433 --username sqluser -P
    ```

3. Sostituire `MYSQLSERVER` con il nome del Server SQL e `MYDATABASE` con il nome del database SQL. Per esportare dati da Hive `hivesampletable` alla tabella il `mobiledata` tabella nel Database SQL, immettere il comando seguente nella finestra di aprire una connessione SSH. Immettere la password per l'account di accesso di SQL Server quando richiesto

    ```bash
    sqoop export --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P -table 'mobiledata' --hcatalog-table hivesampletable
    ```

4. Per verificare che siano stati esportati i dati, usare le query seguenti dal client SQL per visualizzare i dati esportati:

    ```sql
    SELECT COUNT(*) FROM [dbo].[mobiledata] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[mobiledata] WITH (NOLOCK);
    ```

## <a name="sqoop-import"></a>Importazione con Sqoop

Da SQL Server in archiviazione di Azure.

1. Sostituire `MYSQLSERVER` con il nome del Server SQL e `MYDATABASE` con il nome del database SQL. Immettere il comando seguente nella finestra di aprire una connessione SSH per importare dati dal `mobiledata` tabella di Database SQL, il `wasb:///tutorials/usesqoop/importeddata` directory su HDInsight. Immettere la password per l'account di accesso di SQL Server quando richiesto. I campi nei dati sono separati da un carattere di tabulazione e le righe terminano con un carattere di nuova riga.

    ```bash
    sqoop import --connect 'jdbc:sqlserver://MYSQLSERVER.database.windows.net:1433;database=MYDATABASE' --username sqluser -P --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1
    ```

2. Al termine dell'importazione, immettere il comando seguente nella finestra di aprire una connessione SSH a elencare i dati nella nuova directory:

    ```bash
    hdfs dfs -text /tutorials/usesqoop/importeddata/part-m-00000
    ```

## <a name="limitations"></a>Limitazioni

* Esportazione in blocco: con HDInsight basato su Linux, il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti bulk.

* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="important-considerations"></a>Considerazioni importanti

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure.

    Per avere un esempio, vedere il documento [Connettere HDInsight alla rete locale](./../connect-on-premises-network.md).

    Per altre informazioni sull'uso di HDInsight con le reti virtuali di Azure, vedere il documento [Estendere HDInsight con la rete virtuale di Azure](../hdinsight-extend-hadoop-virtual-network.md). Per altre informazioni su Rete virtuale di Azure, vedere il documenti di [panoramica sulle reti virtuali](../../virtual-network/virtual-networks-overview.md).

* SQL Server deve essere configurato per consentire l'autenticazione SQL. Per altre informazioni, consultare il documento [Scegliere una modalità di autenticazione](https://msdn.microsoft.com/ms144284.aspx).

* Potrebbe essere necessario configurare SQL Server affinché accetti le connessioni remote. Per altre informazioni, vedere il documento [Come risolvere i problemi di connessione al motore di database di SQL Server](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Apache Oozie con HDInsight](../hdinsight-use-oozie-linux-mac.md): Usare l'azione Sqoop in un flusso di lavoro Oozie.
* [Analizzare i dati sui ritardi dei voli con HDInsight](../hdinsight-analyze-flight-delay-data-linux.md): usare Apache Hive nell'analisi dei dati sui ritardi dei voli e quindi usare Sqoop per esportare i dati nel database SQL di Azure.
* [Caricare dati in HDInsight](../hdinsight-upload-data.md): trovare altri metodi per il caricamento di dati in HDInsight o nell'archiviazione BLOB di Azure.
