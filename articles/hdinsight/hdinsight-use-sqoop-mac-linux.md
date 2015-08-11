<properties
	pageTitle="Usare Sqoop di Hadoop in HDInsight | Microsoft Azure"
	description="Informazioni su come eseguire l'importazione e l'esportazione con Sqoop tra un framework basato su Linux in un cluster HDInsight e un database SQL di Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/15/2015"
	ms.author="larryfr"/>

#Usare Sqoop con Hadoop in HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come utilizzare Sqoop per effettuare importazioni ed esportazioni tra un cluster HDInsight basato su Linux e un database di SQL Server.

> [AZURE.NOTE]I passaggi descritti in questo articolo illustrano come utilizzare SSH per connettersi a un cluster HDInsight basato su Linux. Anche i client Windows possono utilizzare Azure PowerShell per utilizzare Sqoop nei cluster basati su Linux, secondo quanto documentato in [Utilizzare Sqoop con Hadoop in HDInsight (PowerShell)](hdinsight-use-sqoop.md).

##Informazioni su Sqoop

Benché Hadoop rappresenti una scelta ottimale per l'elaborazione di dati non strutturati e semistrutturati, ad esempio log e file, potrebbe essere necessario elaborare anche dati strutturati archiviati in database relazionali.

[Sqoop][sqoop-user-guide-1.4.4] è uno strumento progettato per il trasferimento di dati tra cluster Hadoop e database relazionali. Può essere usato per importare dati in HDFS (Hadoop Distributed File System) da un sistema di gestione di database relazionali (RDBMS), ad esempio SQL, MySQL oppure Oracle, trasformare i dati in Hadoop con MapReduce o Hive e quindi esportarli nuovamente in un sistema RDBMS. In questa esercitazione si userà un database SQL Server per il database relazionale.

Per informazioni sulle versioni di Sqoop supportate nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].


##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Workstation**: un computer con un client SSH.

- **Interfaccia della riga di comando di Azure**: per ulteriori informazioni, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli.md)

- **Cluster HDInsight basato su Linux**: per istruzioni sul provisioning dei cluster, vedere [Introduzione a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) o [Effettuare il provisioning di cluster HDInsight][hdinsight-provision].

- **Database SQL di Azure**: in questo documento vengono fornite istruzioni per la creazione di un database SQL di esempio. Per ulteriori informazioni sui database SQL, vedere [Introduzione all'uso del database SQL di Azure][sqldatabase-get-started].

* **SQL Server**: i passaggi descritti in questo documento consente inoltre, con alcune modifiche, con SQL Server. Per ulteriori informazioni sui requisiti specifici per l'utilizzo di questo articolo con SQL Server, vedere la sezione [Utilizzo di SQL Server](#using-sql-server).

##Informazioni sullo scenario

I cluster HDInsight vengono forniti con alcuni dati di esempio. Una tabella di Hive denominata **hivesampletable** che fa riferimento al file di dati presente in **wasb:///hive/warehouse/hivesampletable**. La tabella contiene alcuni dati relativi al dispositivo mobile. Lo schema della tabella Hive è il seguente:

| Campo | Tipo di dati |
| ----- | --------- |
| clientid | stringa |
| querytime | stringa |
| market | stringa |
| deviceplatform | stringa |
| devicemake | stringa |
| devicemodel | stringa |
| state | stringa |
| country | stringa |
| querydwelltime | double |
| sessionid | bigint |
| sessionpagevieworder | bigint |

Innanzitutto, sarà necessario esportare la tabella **hivesampletable** nel database SQL di Azure o in SQL Server in una tabella denominata **mobiledata**, quindi occorrerà importare nuovamente la tabella in HDInsight nel percorso **wasb:///tutorials/usesqoop/importeddata**.

##Creare un database

1. Aprire un terminale o un prompt dei comandi e utilizzare il comando seguente per creare un nuovo server di database SQL di Azure:

        azure sql server create <adminLogin> <adminPassword> <region>

    Ad esempio, `azure sql server create admin password "West US"`.

    Quando il comando viene completato, si riceverà una risposta simile alla seguente:

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT]Si noti il nome del server restituito da questo comando: è il nome breve del server di database SQL che è stato creato. Il nome di dominio completo è **&lt;nomebreve&gt;.database.windows.net**.

2. Utilizzare il comando seguente per creare un database denominato **sqooptest** nel server di database SQL:

        sql db create [options] <serverName> sqooptest <adminLogin> <adminPassword>

    Quando viene completato il comando, verrà restituito un messaggio di conferma positiva.

	> [AZURE.NOTE]se viene visualizzato un errore che indica che non si dispone dell'accesso, potrebbe essere necessario aggiungere l'indirizzo IP della workstation client per il firewall del database SQL utilizzando il seguente:
	>
	> `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

##Creare una tabella

> [AZURE.NOTE]Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene utilizzato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.

1. Usare SSH per connettersi al cluster HDInsight basato su Linux. L'indirizzo da usare durante la connessione è `CLUSTERNAME-ssh.azurehdinsight.net` e la porta è `22`.

	Per ulteriori informazioni sull'utilizzo di SSH per connettersi a HDInsight, vedere i seguenti documenti:

    * **Client Linux, Unix o OS X**: vedere [Connettersi a un cluster HDInsight basato su Linux, Unix e OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster)

    * **Client Windows**: vedere [Connettersi a un cluster HDInsight basato su Linux da client Windows](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster)

3. Immettere il comando seguente per installare FreeTDS:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Dopo aver installato  FreeTDS, utilizzare il comando seguente per connettersi al server di database SQL creato in precedenza:

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

    Per verificare la corretta creazione della tabella, utilizzare quanto segue:

        SELECT * FROM information_schema.tables
        GO

    L'output dovrebbe essere simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        sqooptest       dbo     mobiledata      BASE TABLE

8. Per uscire dall'utilità psql, immettere `exit` al prompt di `1>`.

##Esportazione con Sqoop

2. Utilizzare il comando seguente per creare un collegamento al driver JDBC di SQL Server dalla directory lib di Sqoop. Ciò consentirà a Sqoop utilizzare tale driver per comunicare con il database SQL:

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /usr/hdp/current/sqoop-client/lib/sqljdbc4.jar

3. Utilizzare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dovrebbe essere visualizzato un elenco di database, compreso il database **sqooptest** creato in precedenza

4. Utilizzare il comando seguente per esportare dati dalla tabella **hivesampletable** alla tabella **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    I questo modo, si indicherà a Sqoop di connettersi al database **sqooptest** e di esportare dati da **wasb:///hive/warehouse/hivesampletable** (file fisici per la tabella *hivesampletable*) alla tabella **mobiledata**.

5. Al termine dell'esecuzione del comando, utilizzare le informazioni seguenti per connettersi al database tramite TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una volta connessi, utilizzare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella **mobiledata**:

        SELECT * FROM mobiledata
        GO

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

##Importazione con Sqoop

1. Utilizzare le seguenti informazioni per importare dati dalla tabella **mobiledata** nel database SQL nella directory **wasb:///tutorials/usesqoop/importeddata** in HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    I dati importati conterranno campi separati da un carattere di tabulazione e le righe verranno terminate da un carattere di nuova riga.

2. Una volta completata l'importazione, utilizzare il comando seguente per elencare i dati della nuova directory:

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

##Utilizzo di SQL Server

È inoltre possibile utilizzare Sqoop per importare ed esportare dati da SQL Server nel data center o in una macchina virtuale ospitata in Azure. Le differenze tra l'utilizzo del database SQL e SQL Server sono:

* HDInsight e SQL Server devono trovarsi nella stessa rete virtuale di Azure

    > [AZURE.NOTE]HDInsight supporta solo reti virtuali basate sulla posizione e attualmente non funziona con le reti virtuali basate su gruppi di affinità.

    Quando si usa SQL Server nel proprio data center, è necessario configurare la rete virtuale come *da sito a sito* o *da punto a sito*.

    > [AZURE.NOTE]Per le reti virtuali da **punto a sito**, è necessario che su SQL Server venga eseguita l'applicazione di configurazione del client VPN, disponibile nel **dashboard** di configurazione della rete virtuale di Azure.

    Per altre informazioni sulla creazione e la configurazione di una rete virtuale di Azure, vedere [Attività di configurazione della rete virtuale](http://msdn.microsoft.com/library/azure/jj156206.aspx).

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

* Quando ci si connette a SQL Server da HDInsight, potrebbe essere necessario utilizzare l'indirizzo IP di SQL Server, a meno che non sia stato configurato un sistema DNS (Domain Name) per risolvere i nomi nella rete virtuale di Azure. Ad esempio:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

##Passaggi successivi

In questa esercitazione si è appreso come usare Sqoop. Per ulteriori informazioni, vedere:

- [Usare Oozie con HDInsight][hdinsight-use-oozie]: usare un'azione di Sqoop nel flusso di lavoro di Oozie.
- [Analizzare i dati sui ritardi dei voli usando HDInsight][hdinsight-analyze-flight-data]: usare Hive nell'analisi dei dati sui ritardi dei voli e quindi usare Sqoop per esportare dati nel database SQL di Azure.
- [Caricare i dati in HDInsight][hdinsight-upload-data]: per altri metodi per il caricamento di file in HDInsight o nell'archiviazione BLOB di Azure.




[azure-management-portal]: https://manage.windowsazure.com/

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database-create-configure.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: ../install-configure-powershell.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html

<!-----HONumber=July15_HO5-->