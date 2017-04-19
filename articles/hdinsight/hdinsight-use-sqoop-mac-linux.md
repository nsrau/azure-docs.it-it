---
title: Usare Sqoop di Hadoop in HDInsight basato su Linux | Documentazione Microsoft
description: Informazioni su come eseguire l&quot;importazione e l&quot;esportazione con Sqoop tra un framework basato su Linux in un cluster HDInsight e un database SQL di Azure.
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
author: Blackmist
tags: azure-portal
ms.assetid: 303649a5-4be5-4933-bf1d-4b232083c354
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 82e79e83103bbe01e4ddee405e195bd9f83c1717
ms.lasthandoff: 04/12/2017


---
# <a name="use-sqoop-with-hadoop-in-hdinsight-ssh"></a>Usare Sqoop con Hadoop in HDInsight (SSH)
[!INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Sqoop per eseguire importazioni ed esportazioni tra un cluster HDInsight e un database SQL di Azure o un database SQL Server.

> [!IMPORTANT]
> I passaggi descritti in questo documento funzionano solo con i cluster HDInsight che usano Linux. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Un cluster Hadoop in HDInsight** e un **database SQL di Azure**: i passaggi in questo documento si basano sul cluster e sul database creati seguendo le indicazioni contenute nella sezione [Creare un cluster e un database SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database). Se si ha già un cluster HDInsight e il database SQL, è possibile sostituirli con i valori usati in queste indicazioni.
* **Workstation**: un computer con un client SSH.

## <a name="install-freetds"></a>Installare FreeTDS
1. Usare SSH per connettersi al cluster HDInsight basato su Linux. L'indirizzo da usare durante la connessione è `CLUSTERNAME-ssh.azurehdinsight.net` e la porta è `22`.

    Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Immettere il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS verrà usato in diversi passaggi per connettersi al database SQL.

## <a name="create-the-table-in-sql-database"></a>Creare la tabella nel database SQL
> [!IMPORTANT]
> Se si usa un cluster HDInsight e il database SQL creato usando la procedura in [Creare un cluster e un database SQL](hdinsight-use-sqoop.md), ignorare i passaggi descritti in questa sezione poiché il database e la tabella sono stati creati come parte della procedura in tale documento.
>
>

1. Dalla connessione SSH a HDInsight, usare il comando seguente per connettersi al server del database SQL e creare una tabella che verrà usata negli altri passaggi:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    L'output sarà simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>
2. Al prompt di `1>` , immettere il codice seguente:

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

    Dopo aver immesso l'istruzione `GO` , verranno valutate le istruzioni precedenti. Innanzitutto, verrà creata la tabella **mobiledata** a cui verrà aggiunto un indice cluster (richiesto dal Database SQL).

    Per verificare la corretta creazione della tabella, usare il comando seguente:

        SELECT * FROM information_schema.tables
        GO

    L'output dovrebbe essere simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE
3. Per uscire dall'utilità tsql, immettere `exit` al prompt di `1>`.

## <a name="sqoop-export"></a>Esportazione con Sqoop
1. Dalla connessione SSH a HDInsight, usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dovrebbe essere visualizzato un elenco di database, compreso il database **sqooptest** creato in precedenza
2. Usare il comando seguente per esportare dati dalla tabella **hivesampletable** alla tabella **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    In questo modo si indica a Sqoop di connettersi al database SQL **sqooptest** e di esportare i dati da **wasbs:///hive/warehouse/hivesampletable** (file fisici per la tabella *hivesampletable*) alla tabella **mobiledata**.
3. Al termine dell'esecuzione del comando, usare le informazioni seguenti per connettersi al database tramite TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una volta connessi, utilizzare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella **mobiledata** :

        SELECT * FROM mobiledata
        GO

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

## <a name="sqoop-import"></a>Importazione con Sqoop
1. Usare il comando seguente per importare i dati dalla tabella **mobiledata** del database SQL alla directory **wasbs:///tutorials/usesqoop/importeddata** in HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    I dati importati conterranno campi separati da un carattere di tabulazione e le righe verranno terminate da un carattere di nuova riga.
2. Una volta completata l'importazione, usare il comando seguente per elencare i dati della nuova directory:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

## <a name="using-sql-server"></a>Uso di SQL Server
È inoltre possibile usare Sqoop per importare ed esportare dati da SQL Server nel data center o in una macchina virtuale ospitata in Azure. Le differenze tra l'uso del database SQL e SQL Server sono:

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure

  > [!NOTE]
  > HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.
  >
  >

    Quando si usa SQL Server nel proprio data center, è necessario configurare la rete virtuale come *da sito a sito* o *da punto a sito*.

  > [!NOTE]
  > Per le reti virtuali **da punto a sito**, è necessario che su SQL Server venga eseguita l'applicazione di configurazione del client VPN, disponibile nel **Dashboard** di configurazione della rete virtuale di Azure.
  >
  >

    Per altre informazioni su Rete virtuale di Azure, vedere la [panoramica sulle reti virtuali](../virtual-network/virtual-networks-overview.md).
* SQL Server deve essere configurato per consentire l'autenticazione SQL. Per altre informazioni, vedere [Scegliere un metodo di autenticazione](https://msdn.microsoft.com/ms144284.aspx)
* Potrebbe essere necessario configurare SQL Server affinché accetti le connessioni remote. Per altre informazioni, vedere l'argomento relativo alla [risoluzione dei problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx) .
* È necessario creare il database **sqooptest** in SQL Server usando un'utilità come **SQL Server Management Studio** o **tsql**. La procedura per l'uso dell'interfaccia della riga di comando di Azure funziona solo per un database SQL di Azure.

    Le istruzioni TSQL per creare la tabella **mobiledata** sono simili a quelle utilizzate per database SQL, fatta eccezione per la creazione di un indice clustrer (non è obbligatorio per SQL Server):

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
* Quando ci si connette a SQL Server da HDInsight, potrebbe essere necessario usare l'indirizzo IP di SQL Server, a meno che non sia stato configurato un sistema DNS (Domain Name) per risolvere i nomi nella rete virtuale di Azure. Ad esempio:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

## <a name="limitations"></a>Limitazioni
* Esportazione di massa: con HDInsight basato su Linux, attualmente il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti di massa.
* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

* [Usare Oozie con HDInsight][hdinsight-use-oozie]: usare un'azione di Sqoop nel flusso di lavoro di Oozie.
* [Analizzare i dati sui ritardi dei voli con HDInsight][hdinsight-analyze-flight-data]: usare Hive per analizzare i dati sui ritardi dei voli e quindi usare Sqoop per esportare dati in un database SQL di Azure.
* [Caricare dati in HDInsight][hdinsight-upload-data]: altri metodi per caricare dati in HDInsight e nell'archivio BLOB di Azure.

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
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

