<properties
	pageTitle="Usare Sqoop di Hadoop in HDInsight basati su Linux | Microsoft Azure"
	description="Informazioni su come eseguire l'importazione e l'esportazione con Sqoop tra un framework basato su Linux in un cluster HDInsight e un database SQL di Azure."
	editor="cgronlun"
	manager="paulettm"
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
	ms.date="03/09/2016"
	ms.author="larryfr"/>

#Usare Sqoop con Hadoop in HDInsight (SSH)

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Informazioni su come usare Sqoop per eseguire importazioni ed esportazioni tra un cluster HDInsight basato su Linux e un database di SQL Server.

> [AZURE.NOTE] I passaggi descritti in questo articolo illustrano come usare SSH per connettersi a un cluster HDInsight basato su Linux. I client Windows possono anche usare Azure PowerShell e l'SDK HDInsight .NET per usare Sqoop nei cluster basati su Linux. Usare il selettore di schede per aprire gli articoli.

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:


- **Un cluster Hadoop in HDInsight**. Vedere [Creare un cluster e un database SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).
- **Workstation**: un computer con un client SSH.
- **Interfaccia della riga di comando di Azure**: per ulteriori informazioni, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md)

##Esportazione con Sqoop

2. Usare il comando seguente per creare un collegamento al driver JDBC di SQL Server dalla directory lib di Sqoop. Ciò consentirà a Sqoop di usare tale driver per comunicare con il database SQL:

        sudo ln /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /usr/hdp/current/sqoop-client/lib/sqljdbc41.jar

3. Usare il comando seguente per verificare che Sqoop possa visualizzare il database SQL:

        sqoop list-databases --connect jdbc:sqlserver://<serverName>.database.windows.net:1433 --username <adminLogin> --password <adminPassword>

    Dovrebbe essere visualizzato un elenco di database, compreso il database **sqooptest** creato in precedenza

4. Utilizzare il comando seguente per esportare dati dalla tabella **hivesampletable** alla tabella **mobiledata**:

        sqoop export --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --export-dir 'wasb:///hive/warehouse/hivesampletable' --fields-terminated-by '\t' -m 1

    In questo modo, si indicherà a Sqoop di connettersi al database **sqooptest** e di esportare dati da ****wasb:///hive/warehouse/hivesampletable** (file fisici per la tabella *hivesampletable*) alla tabella **mobiledata**.

5. Al termine dell'esecuzione del comando, usare le informazioni seguenti per connettersi al database tramite TSQL:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D sqooptest

    Una volta connessi, utilizzare le istruzioni seguenti per verificare che i dati siano stati esportati nella tabella **mobiledata**:

        SELECT * FROM mobiledata
        GO

    Dovrebbe essere visualizzato un elenco di dati della tabella. Digitare `exit` per uscire dall'utilità tsql.

##Importazione con Sqoop

1. Utilizzare le seguenti informazioni per importare dati dalla tabella **mobiledata** nel database SQL nella directory ****wasb:///tutorials/usesqoop/importeddata** in HDInsight:

        sqoop import --connect 'jdbc:sqlserver://<serverName>.database.windows.net:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

    I dati importati conterranno campi separati da un carattere di tabulazione e le righe verranno terminate da un carattere di nuova riga.

2. Una volta completata l'importazione, usare il comando seguente per elencare i dati della nuova directory:

        hadoop fs -text wasb:///tutorials/usesqoop/importeddata/part-m-00000

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

* Quando ci si connette a SQL Server da HDInsight, potrebbe essere necessario usare l'indirizzo IP di SQL Server, a meno che non sia stato configurato un sistema DNS (Domain Name) per risolvere i nomi nella rete virtuale di Azure. Ad esempio:

        sqoop import --connect 'jdbc:sqlserver://10.0.1.1:1433;database=sqooptest' --username <adminLogin> --password <adminPassword> --table 'mobiledata' --target-dir 'wasb:///tutorials/usesqoop/importeddata' --fields-terminated-by '\t' --lines-terminated-by '\n' -m 1

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

<!---HONumber=AcomDC_0413_2016-->