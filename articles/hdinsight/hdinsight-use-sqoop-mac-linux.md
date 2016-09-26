<properties
	pageTitle="Usare Sqoop di Hadoop in HDInsight basati su Linux | Microsoft Azure"
	description="Informazioni su come eseguire l'importazione e l'esportazione con Sqoop tra un framework basato su Linux in un cluster HDInsight e un database SQL di Azure."
	editor="cgronlun"
	manager="jhubbard"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	tags="azure-portal"/>  

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="larryfr"/>

#Usare Sqoop con Hadoop in HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Sqoop per eseguire importazioni ed esportazioni tra un cluster HDInsight basato su Linux e un database di SQL Server.

> [AZURE.NOTE] I passaggi descritti in questo articolo illustrano come usare SSH per connettersi a un cluster HDInsight basato su Linux. I client Windows possono anche usare Azure PowerShell e l'SDK HDInsight .NET per usare Sqoop nei cluster basati su Linux. Usare il selettore di schede per aprire gli articoli.

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- Un **cluster Hadoop in HDInsight** e un __database SQL di Azure__: i passaggi in questo documento si basano sul cluster e sul database creato usando le indicazioni nella sezione [Creare un cluster e un database SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database). Se si ha già un cluster HDInsight e il database SQL, è possibile sostituirli con i valori usati in queste indicazioni.
- **Workstation**: un computer con un client SSH.

##Installare FreeTDS

1. Usare SSH per connettersi al cluster HDInsight basato su Linux. L'indirizzo da usare durante la connessione è `CLUSTERNAME-ssh.azurehdinsight.net` e la porta è `22`.

	Per altre informazioni sull'uso di SSH per connettersi a HDInsight, vedere i seguenti documenti:

    * **Client Linux, Unix o OS X**: vedere [Connettersi a un cluster HDInsight basato su Linux, Unix e OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Client Windows**: vedere [Connettersi a un cluster HDInsight basato su Linux da client Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Immettere il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

    FreeTDS verrà usato in diversi passaggi per connettersi al database SQL.

##Creare la tabella nel database SQL

> [AZURE.IMPORTANT] Se si usa un cluster HDInsight e il database SQL creato usando la procedura in [Creare un cluster e un database SQL](hdinsight-use-sqoop.md), ignorare i passaggi descritti in questa sezione poiché il database e la tabella sono stati creati come parte della procedura in tale documento.

1. Dalla connessione SSH a HDInsight, usare il comando seguente per connettersi al server del database SQL e creare una tabella che verrà usata negli altri passaggi:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    L'output sarà simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to sqooptest
        1>

5. Al prompt di `1>`, immettere il codice seguente:

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

    Dopo aver immesso l'istruzione `GO`, verranno valutate le istruzioni precedenti. Innanzitutto, verrà creata la tabella **mobiledata** a cui verrà aggiunto un indice cluster (richiesto dal Database SQL).

    Per verificare la corretta creazione della tabella, usare quanto segue:

        SELECT * FROM information_schema.tables
        GO

    L'output dovrebbe essere simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Per uscire dall'utilità psql, immettere `exit` al prompt di `1>`.

##Esportazione con Sqoop

3. Dalla connessione SSH a HDInsight, usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dovrebbe essere visualizzato un elenco di database, compreso il database **sqooptest** creato in precedenza

4. Utilizzare il comando seguente per esportare dati dalla tabella **hivesampletable** alla tabella **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasbs:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    In questo modo, si indicherà a Sqoop di connettersi al database SQL **sqooptest** e di esportare dati da **wasbs:///hive/warehouse/hivesampletable** (file fisici per la tabella *hivesampletable*) nella tabella **mobiledata**.

5. Al termine dell'esecuzione del comando, usare le informazioni seguenti per connettersi al database tramite TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una volta connessi, utilizzare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella **mobiledata**:

        SELECT * FROM mobiledata
        GO

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

##Importazione con Sqoop

1. Utilizzare le seguenti informazioni per importare dati dalla tabella **mobiledata** nel database SQL nella directory **wasbs:///tutorials/usesqoop/importeddata** in HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    I dati importati conterranno campi separati da un carattere di tabulazione e le righe verranno terminate da un carattere di nuova riga.

2. Una volta completata l'importazione, usare il comando seguente per elencare i dati della nuova directory:

        hadoop fs -text wasbs:///tutorials/usesqoop/importeddata/part-m-00000

##Uso di SQL Server

È inoltre possibile usare Sqoop per importare ed esportare dati da SQL Server nel data center o in una macchina virtuale ospitata in Azure. Le differenze tra l'uso del database SQL e SQL Server sono:

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure

    > [AZURE.NOTE] HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.

    Quando si usa SQL Server nel proprio data center, è necessario configurare la rete virtuale come *da sito a sito* o *da punto a sito*.

    > [AZURE.NOTE] Per le reti virtuali da **punto a sito**, è necessario che su SQL Server venga eseguita l'applicazione di configurazione del client VPN, disponibile nel **dashboard** di configurazione della rete virtuale di Azure.

    Per altre informazioni sulla creazione e la configurazione di una rete virtuale di Azure, vedere [Attività di configurazione della rete virtuale](../services/virtual-machines/).

* SQL Server deve essere configurato per consentire l'autenticazione SQL. Per altre informazioni, vedere [Scegliere un metodo di autenticazione](https://msdn.microsoft.com/ms144284.aspx).

* Potrebbe essere necessario configurare SQL Server affinché accetti le connessioni remote. Per altre informazioni, vedere l'argomento relativo alla [risoluzione dei problemi di connessione al motore di database di SQL Server](http://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx).

* È necessario creare il database **sqooptest** in SQL Server utilizzando un'utilità, ad esempio **SQL Server Management Studio** o **tsql**; la procedura per l'utilizzo dell'interfaccia della riga di comando di Azure funziona solo per un database SQL di Azure

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

* Quando ci si connette a SQL Server da HDInsight, potrebbe essere necessario usare l'indirizzo IP di SQL Server, a meno che non sia stato configurato un sistema DNS (Domain Name) per risolvere i nomi nella rete virtuale di Azure. ad esempio:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasbs:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Limitazioni

* Esportazione di massa: con HDInsight basato su Linux, attualmente il connettore Sqoop, usato per esportare dati in Microsoft SQL Server o nel database SQL di Azure, non supporta inserimenti di massa.

* Invio in batch: con HDInsight basato su Linux, quando si usa il comando `-batch` durante gli inserimenti, Sqoop esegue più inserimenti invece di suddividere in batch le operazioni di inserimento.

##Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per altre informazioni, vedere:

- [Usare Oozie con HDInsight][hdinsight-use-oozie]\: usare un'azione di Sqoop nel flusso di lavoro di Oozie.
- [Analizzare i dati sui ritardi dei voli usando HDInsight][hdinsight-analyze-flight-data]\: usare Hive nell'analisi dei dati sui ritardi dei voli e quindi usare Sqoop per esportare dati nel database SQL di Azure.
- [Caricare i dati in HDInsight][hdinsight-upload-data]\: per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.



[hdinsight-versions]: hdinsight-component-versioning.md
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
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!---HONumber=AcomDC_0914_2016-->