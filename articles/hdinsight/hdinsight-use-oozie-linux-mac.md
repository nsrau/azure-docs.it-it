<properties
	pageTitle="Usare i flussi di lavoro Oozie di Hadoop in HDInsight basati su Linux | Microsoft Azure"
	description="Usare Oozie di Hadoop in HDInsight basati su Linux. Informazioni su come definire un flusso di lavoro di Oozie e inviare un processo Oozie."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/28/2016"
	ms.author="larryfr"/>


# Usare Oozie con Hadoop per definire ed eseguire un flusso di lavoro in HDInsight basato su Linux (anteprima)

[AZURE.INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Informazioni su come usare Apache Oozie per definire un flusso di lavoro che usa Hive e Sqoop e quindi eseguire il flusso di lavoro in un cluster HDInsight basato su Linux.

Apache Oozie è un sistema di flusso di lavoro/coordinamento che consente di gestire i processi Hadoop. È integrato nello stack di Hadoop e supporta i processi Hadoop per Apache MapReduce, Apache Pig, Apache Hive e Apache Sqoop. Può anche essere usato per pianificare processi specifici di un sistema, come i programmi Java o gli script della shell.

> [AZURE.NOTE] Un'altra opzione per definire i flussi di lavoro con HDInsight è Data factory di Azure. Per altre informazioni su Data factory di Azure, vedere [Usare Pig e Hive con Data factory][azure-data-factory-pig-hive].

##Prerequisiti

Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- **Una sottoscrizione di Azure**: vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

- **Interfaccia della riga di comando di Azure**: vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md).

- **Un cluster di HDInsight**: vedere [Introduzione all'uso di HDInsight in Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

- **Un database SQL di Azure**: verrà creato usando la procedura descritta in questo documento.

##Esempio di flusso di lavoro

Il flusso di lavoro che si implementerà seguendo le istruzioni in questo documento contiene due azioni. Le azioni sono definizioni per attività, ad esempio l'esecuzione di Hive, Sqoop, MapReduce o altri processi:

![Diagramma del flusso di lavoro][img-workflow-diagram]

1. Un'azione di Hive esegue uno script HiveQL per estrarre i record da **hivesampletable** inclusa in HDInsight. Ogni riga di dati descrive una visita da un dispositivo mobile specifico. Il formato del record risulterà simile al seguente:

		8       18:54:20        it-IT   Android Samsung SCH-i500        California     United States    13.9204007      0       0
		23      19:19:44        it-IT   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
		23      19:19:46        it-IT   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

	Lo script Hive usato in questo documento conta le visite totali per ogni piattaforma (ad esempio Android o iPhone) e archivia i conteggi in una nuova tabella Hive.

	Per altre informazioni su Hive, vedere [Usare Hive con HDInsight][hdinsight-use-hive].

2.  Un'azione di Sqoop esporta il contenuto della nuova tabella Hive in una tabella di un database SQL di Azure. Per altre informazioni su Sqoop, vedere [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop].

> [AZURE.NOTE] Per informazioni sulle versioni di Oozie supportate nei cluster HDInsight, vedere [Novità delle versioni cluster di Hadoop incluse in HDInsight][hdinsight-versions].

##Creare la directory di lavoro

Oozie prevede che le risorse necessarie per un processo siano archiviate nella stessa directory. Questo esempio usa ****wasb:///tutorials/useoozie**. Usare il comando seguente per creare questa directory e la directory dati che conterrà la nuova tabella Hive creata da questo flusso di lavoro:

	hadoop fs -mkdir -p /tutorials/useoozie/data

> [AZURE.NOTE] Il parametro `-p` causa la creazione di tutte le directory nel percorso, se non esistono già. La directory **data** verrà usata per contenere i dati usati dallo script **useooziewf.hql**.

Eseguire anche il comando seguente, per assicurare che Oozie possa rappresentare l'account utente durante l'esecuzione di processi Hive e Sqoop. Sostituire **USERNAME** con il proprio nome di accesso:

	sudo adduser USERNAME users

Se viene visualizzato errore che indica che l'utente è già membro del gruppo utenti, è possibile ignorarlo.

##Aggiungere un driver di database

Poiché questo flusso di lavoro usa Sqoop per esportare dati nel database SQL, è necessario fornire una copia del driver JDBC usato per comunicare con il database SQL. Usare il comando seguente per copiarlo nella directory di lavoro:

	hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /tutorials/useoozie/sqljdbc4.jar

Se il flusso di lavoro usa altre risorse, ad esempio un file jar contenente un'applicazione MapReduce, è necessario aggiungere anche questi elementi.

##Definire la query Hive

Usare i passaggi seguenti per creare uno script HiveQL che definisca una query, che verrà usata in un flusso di lavoro di Oozie più avanti in questo documento.

1. Usare SSH per connettersi al cluster HDInsight basato su Linux:

    * **Client Linux, Unix o OS X**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, OS X o Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

    * **Client Windows**: vedere [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Usare il comando seguente per creare un nuovo file:

        nano useooziewf.hql

1. All'apertura dell'editor nano, usare quanto segue come contenuto del file:

		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
		FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;

	Nello script vengono usate due variabili:

	- **${hiveTableName}**: conterrà il nome della tabella da creare.
	- **${hiveDataFolder}**: conterrà il percorso in cui archiviare i file di dati per la tabella.

	Il file di definizione del flusso di lavoro (workflow.xml in questa esercitazione) passa questi valori allo script HiveQL in fase di esecuzione.

2. Premere CTRL+X per uscire dall'editor. Quando richiesto, selezionare **S** per salvare il file, quindi premere **INVIO** per usare il nome di file **useooziewf.hql**.

3. Usare i comandi seguenti per copiare **useooziewf.hql** in****wasb:///tutorials/useoozie/useooziewf.hql**:

		hadoop fs -copyFromLocal useooziewf.hql /tutorials/useoozie/useooziewf.hql

	Questi comandi consentono di archiviare il file **useooziewf.hql** nell'account di archiviazione di Azure associato al cluster, che conserva il file anche se il cluster viene eliminato. In questo modo è possibile risparmiare denaro eliminando i cluster quando non sono in uso, ma mantenendo i processi e i flussi di lavoro.

##Definire il flusso di lavoro

Le definizioni dei flussi di lavoro di Oozie sono scritte in linguaggio hPDL (XML Process Definition Language). Usare i passaggi seguenti per definire il flusso di lavoro:

1. Usare l'istruzione seguente per creare e modificare un nuovo file:

        nano workflow.xml

1. All'apertura dell'editor nano, immettere il codice seguente come contenuto del file:

		<workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
		  <start to = "RunHiveScript"/>
		  <action name="RunHiveScript">
		    <hive xmlns="uri:oozie:hive-action:0.2">
		      <job-tracker>${jobTracker}</job-tracker>
		      <name-node>${nameNode}</name-node>
		      <configuration>
		        <property>
		          <name>mapred.job.queue.name</name>
		          <value>${queueName}</value>
		        </property>
		      </configuration>
		      <script>${hiveScript}</script>
		      <param>hiveTableName=${hiveTableName}</param>
		      <param>hiveDataFolder=${hiveDataFolder}</param>
		    </hive>
		    <ok to="RunSqoopExport"/>
		    <error to="fail"/>
		  </action>
		  <action name="RunSqoopExport">
		    <sqoop xmlns="uri:oozie:sqoop-action:0.2">
		      <job-tracker>${jobTracker}</job-tracker>
		      <name-node>${nameNode}</name-node>
		      <configuration>
		        <property>
		          <name>mapred.compress.map.output</name>
		          <value>true</value>
		        </property>
		      </configuration>
		      <arg>export</arg>
		      <arg>--connect</arg>
		      <arg>${sqlDatabaseConnectionString}</arg>
		      <arg>--table</arg>
		      <arg>${sqlDatabaseTableName}</arg>
		      <arg>--export-dir</arg>
		      <arg>${hiveDataFolder}</arg>
		      <arg>-m</arg>
		      <arg>1</arg>
		      <arg>--input-fields-terminated-by</arg>
		      <arg>"\t"</arg>
		      <archive>sqljdbc4.jar</archive>
		      </sqoop>
		    <ok to="end"/>
		    <error to="fail"/>
		  </action>
		  <kill name="fail">
		    <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
		  </kill>
		  <end name="end"/>
		</workflow-app>

	Nel flusso di lavoro vengono definite due azioni:

	- **RunHiveScript**: questa è l'azione di avvio ed esegue lo script Hive **useooziewf.hql**

	- **RunSqoopExport**: esporta i dati creati dallo script Hive nel database SQL tramite Sqoop. Viene eseguita solo se l'azione **RunHiveScript** ha esito positivo.

		> [AZURE.NOTE] Per altre informazioni sul flusso di lavoro di Oozie e sull'uso di azioni del flusso di lavoro, vedere la [documentazione di Apache Oozie 4.0][apache-oozie-400] (per HDInsight versione 3.0) o la [documentazione di Apache Oozie 3.3.2][apache-oozie-332] (per HDInsight versione 2.1).

	Si noti che il flusso di lavoro include diverse voci, ad esempio `${jobTracker}`, che vengono sostituite da valori usati nella definizione del processo più avanti in questo documento.

	Notare anche la voce `<archive>sqljdbc4.jar</arcive>` nella sezione Sqoop. Indica a Oozie di rendere disponibile questo archivio per Sqoop quando l'azione viene eseguita.

2. Usare CTRL+X, quindi **S** e **INVIO** per salvare il file.

3. Usare il comando seguente per copiare il file **workflow.xml** in ****wasb:///tutorials/useoozie/workflow.xml**:

		hadoop fs -copyFromLocal workflow.xml wasb:///tutorials/useoozie/workflow.xml

##Creare il database

La procedura seguente crea il database SQL di Azure in cui saranno esportati i dati.

> [AZURE.IMPORTANT] Prima di eseguire questa procedura, è necessario [installare e configurare l'interfaccia della riga di comando di Azure](../xplat-cli-install.md). È possibile installare l'interfaccia della riga di comando e seguire la procedura per creare un database dal cluster HDInsight o dalla workstation locale.

1. Usare il comando seguente per creare un nuovo server di database SQL di Azure:

        azure sql server create <adminLogin> <adminPassword> <region>

    Ad esempio, `azure sql server create admin password "West US"`.

    Quando il comando viene completato, si riceverà una risposta simile alla seguente:

        info:    Executing command sql server create
        + Creating SQL Server
        data:    Server Name i1qwc540ts
        info:    sql server create command OK

    > [AZURE.IMPORTANT] Si noti il nome del server restituito da questo comando (**i1qwc540ts** nell'esempio precedente). è il nome breve del server di database SQL che è stato creato. Il nome di dominio completo è **&lt;nomebreve&gt;.database.windows.net**. Nel caso dell'esempio precedente, l'FQDN è **i1qwc540ts.database.windows.net**.

2. Usare il comando seguente per creare un database denominato **oozietest** nel server del database SQL:

        azure sql db create [options] <serverName> oozietest <adminLogin> <adminPassword>

    Quando viene completato il comando, verrà restituito un messaggio di conferma positiva.

	> [AZURE.NOTE] Se viene visualizzato un errore che indica che non si dispone dell'accesso, potrebbe essere necessario aggiungere l'indirizzo IP del sistema al firewall del database SQL tramite il comando seguente:
    >
    > `sql firewallrule create [options] <serverName> <ruleName> <startIPAddress> <endIPAddress>`

###Creare la tabella

> [AZURE.NOTE] Sono disponibili diversi modi per connettersi al database SQL per creare una tabella. Nei seguenti passaggi viene usato [FreeTDS](http://www.freetds.org/) dal cluster HDInsight.

3. Usare il comando seguente per installare FreeTDS nel cluster HDInsight:

        sudo apt-get --assume-yes install freetds-dev freetds-bin

4. Dopo aver installato FreeTDS, usare il comando seguente per connettersi al server del database SQL creato in precedenza:

        TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

    L'output sarà simile al seguente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

5. Al prompt di `1>`, immettere il codice seguente:

		CREATE TABLE [dbo].[mobiledata](
		[deviceplatform] [nvarchar](50),
		[count] [bigint])
		GO
		CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
		GO

    Dopo aver immesso l'istruzione `GO`, verranno valutate le istruzioni precedenti. Verrà creata una nuova tabella denominata **mobiledata**, in cui Sqoop effettuerà la scrittura.

    Per verificare la corretta creazione della tabella, usare il comando seguente:

        SELECT * FROM information_schema.tables
        GO

    L'output dovrebbe essere simile al seguente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

8. Per uscire dall'utilità psql, immettere `exit` al prompt di `1>`.

##Creare la definizione del processo

La definizione del processo descrive dove trovare il file workflow.xml e altri file usati dal flusso di lavoro (ad esempio useooziewf.hql). Definisce inoltre i valori delle proprietà usate nel flusso di lavoro e nei file associati.

1. Usare il comando seguente per ottenere l'indirizzo WASB completo della risorsa di archiviazione predefinita. Verrà usato nel file di configurazione tra poco:

		sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml

	Dovrebbero essere restituiti informazioni simili alle seguenti:

		<name>fs.defaultFS</name>
		<value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>

	Salvare il valore ****wasb://mycontainer@mystorageaccount.blob.core.windows.net**, perché verrà usato nei passaggi successivi.

2. Usare il comando seguente per ottenere l'FQDN del nodo head del cluster. Verrà usato per l'indirizzo di JobTracker per il cluster. Verrà usato nel file di configurazione tra poco:

		hostname -f

	Verranno restituite informazioni simili alle seguenti:

		hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net

	La porta usata per JobTracker è 8050, quindi l'indirizzo completo da usare per JobTracker sarà **hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8050**.

1. Usare quanto segue per creare la configurazione della definizione del processo Oozie:

		nano job.xml

2. All'apertura dell'editor nano, usare quanto segue come contenuto del file:

		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>

		  <property>
		    <name>nameNode</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
		  </property>

		  <property>
		    <name>jobTracker</name>
		    <value>JOBTRACKERADDRESS</value>
		  </property>

		  <property>
		    <name>queueName</name>
		    <value>default</value>
		  </property>

		  <property>
		    <name>oozie.use.system.libpath</name>
		    <value>true</value>
		  </property>

		  <property>
		    <name>hiveScript</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
		  </property>

		  <property>
		    <name>hiveTableName</name>
		    <value>mobilecount</value>
		  </property>

		  <property>
		    <name>hiveDataFolder</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
		  </property>

		  <property>
		    <name>sqlDatabaseConnectionString</name>
		    <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
		  </property>

		  <property>
		    <name>sqlDatabaseTableName</name>
		    <value>mobiledata</value>
		  </property>

		  <property>
		    <name>user.name</name>
		    <value>YourName</value>
		  </property>

		  <property>
		    <name>oozie.wf.application.path</name>
		    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		  </property>
		</configuration>

	* Sostituire tutte le istanze di ****wasb://mycontainer@mystorageaccount.blob.core.windows.net** con il valore ricevuto in precedenza.

	> [AZURE.WARNING] È necessario usare il percorso WASB completo, con l'account di archiviazione e il contenitore come parte del percorso. L'uso del formato breve (wasb:///) impedirà l'esecuzione dell'azione RunHiveScript all'avvio del processo.

	* Sostituire **JOBTRACKERADDRESS** con l'indirizzo di JobTracker/ResourceManager ricevuto in precedenza.

	* Sostituire **YourName** con il proprio nome di accesso per il cluster HDInsight.

	* Sostituire **serverName**, **adminLogin** e **adminPassword** con le informazioni per il database SQL di Azure.

	La maggior parte delle informazioni in questo file viene usata per popolare i valori usati nel file workflow.xml o ooziewf.hql (ad esempio ${nameNode}).

	> [AZURE.NOTE] La voce **oozie.wf.application.path** definisce la posizione del file workflow.xml, che contiene il flusso di lavoro eseguito da questo processo.

2. Usare CTRL+X, quindi **S** e **INVIO** per salvare il file.

##Inviare e gestire il processo

La procedura seguente usa il comando Oozie per inviare e gestire i flussi di lavoro di Oozie nel cluster. Il comando Oozie è un'interfaccia utente semplice per l'[API REST di Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [AZURE.IMPORTANT] Quando si usa il comando Oozie, è necessario usare l'FQDN per il nodo head di HDInsight. Questo FQDN è accessibile solo dal cluster o se il cluster si trova in una rete virtuale di Azure, da altri computer nella stessa rete.

1. Usare quanto segue per ottenere l'URL del servizio di Oozie:

		sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml

	Verrà restituito un valore simile al seguente:

		<name>oozie.base.url</name>
		<value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>

	La parte ****http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie** è l'URL da usare con il comando Oozie.

2. Usare quanto segue per creare una variabile di ambiente per l'URL, in modo che non sia necessario digitarlo per ogni comando:

		export OOZIE_URL=http://HOSTNAMEt:11000/oozie

	Sostituire l'URL con quello ricevuto in precedenza.

3. Usare quanto segue per inviare il processo:

		oozie job -config job.xml -submit

	Carica le informazioni sul processo da **job.xml** e le invia a Oozie, ma senza eseguirle.

	Dopo il completamento, il comando dovrebbe restituire l'ID del processo, ad esempio `0000005-150622124850154-oozie-oozi-W`. Verrà usato per gestire il processo.

4. Visualizzare lo stato del processo tramite il seguente comando seguente. Immettere l'ID processo restituito dal comando precedente:

		oozie job -info <JOBID>

	Verranno restituite informazioni simili alle seguenti.

		Job ID : 0000005-150622124850154-oozie-oozi-W
		------------------------------------------------------------------------------------------------------------------------------------
		Workflow Name : useooziewf
		App Path      : wasb:///tutorials/useoozie
		Status        : PREP
		Run           : 0
		User          : USERNAME
		Group         : -
		Created       : 2015-06-22 15:06 GMT
		Started       : -
		Last Modified : 2015-06-22 15:06 GMT
		Ended         : -
		CoordAction ID: -
		------------------------------------------------------------------------------------------------------------------------------------

	Lo stato di questo processo è `PREP`, che indica che è stato inviato, ma non ancora avviato.

4. Usare quanto segue per avviare il processo:

		oozie job -start JOBID

	Se si controlla lo stato dopo questo comando, risulterà in esecuzione e verranno restituite informazioni relative alle azioni all'interno del processo.

5. Dopo aver completato l'attività, è possibile verificare che i dati siano stati generati ed esportati nella tabella del database SQL tramite i comandi seguenti:

		TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest

	Al prompt di `1>`, immettere il codice seguente:

		SELECT * FROM mobiledata
		GO

	Dovrebbero essere visualizzate informazioni simili alle seguenti:

		deviceplatform  count
		Android 31591
		iPhone OS       22731
		proprietary development 3
		RIM OS  3464
		Unknown 213
		Windows Phone   1791
		(6 rows affected)

Per altre informazioni sul comando Oozie, vedere [Strumento da riga di comando di Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

##API REST di Oozie

L'API REST di Oozie consente di compilare strumenti personalizzati che funzionano con Oozie. Di seguito sono riportate informazioni specifiche di HDInsight sull'uso dell'API REST di Oozie:

* **URI**: è possibile accedere all'API REST all'esterno del cluster in `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticazione**: è necessario autenticarsi all'API con l'account (admin) e la password HTTP del cluster. Ad esempio:

		curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions

Per altre informazioni sull'uso dell'API REST di Oozie, vedere la pagina relativa all'[API dei servizi Web di Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

##Interfaccia utente Web di Oozie

L'interfaccia utente Web di Oozie fornisce una visualizzazione basata sul Web dello stato dei processi Oozie nel cluster. Consente di visualizzare lo stato del processo, la definizione del processo, la configurazione, un grafico delle azioni nel processo e i log del processo. È anche possibile visualizzare i dettagli delle azioni all'interno di un processo.

Per accedere all'interfaccia utente Web di Oozie, attenersi alla procedura seguente:

1. Creare un tunnel SSH per il cluster HDInsight. Per altre informazioni su come eseguire questa operazione, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce utente Web](hdinsight-linux-ambari-ssh-tunnel.md).

2. Dopo aver creato un tunnel, aprire l'interfaccia utente Web di Ambari nel Web browser. L'URI per il sito Ambari è ****https://CLUSTERNAME.azurehdinsight.net**. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight basato su Linux.

3. Nel lato sinistro della pagina selezionare **Oozie**, quindi **Quick Links** e infine **Oozie Web UI**.

	![immagine dei menu](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. L'interfaccia utente Web di Oozie passa alla visualizzazione predefinita dei processi del flusso di lavoro in esecuzione. Per visualizzare tutti i processi del flusso di lavoro, selezionare **All Jobs**.

	![Tutti i processi visualizzati](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Selezionare un processo per visualizzare altre informazioni specifiche.

	![Informazioni sul processo](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. Nella scheda Job Info è possibile visualizzare informazioni di base sul processo, oltre a singole azioni all'interno del processo. Usando le schede nella parte superiore è possibile visualizzare la definizione, la configurazione, il log del processo o un grafo aciclico diretto (DAG) del processo.

	* **Job Log**: selezionare il pulsante **GetLogs** per recuperare tutti i log relativi al processo o usare il campo **Enter Search Filter** per filtrare i log.

		![Log del processo](./media/hdinsight-use-oozie-linux-mac/joblog.png)

	* **JobDAG**: il DAG è una rappresentazione grafica dei percorsi dati rilevati nel flusso di lavoro.

		![DAG del processo](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Selezionando una delle azioni dalla scheda **Job Info**, verranno visualizzate informazioni sull'azione. Ad esempio, selezionare l'azione **RunHiveScript**.

	![Informazioni sull'azione](./media/hdinsight-use-oozie-linux-mac/action.png)

8. È possibile visualizzare i dettagli relativi all'azione, incluso un collegamento in **Console URL**, che è possibile usare per visualizzare le informazioni su JobTracker per il processo.

##Pianificazione di processi

Il coordinatore consente di specificare inizio, fine e frequenza dei processi, in modo da poterli pianificare per determinati orari.

Per definire una pianificazione per il flusso di lavoro,attenersi alla procedura seguente:

1. Usare quanto segue per creare un nuovo file denominato **coordinator.xml**:

		nano coordinator.xml

	Usare quanto segue come contenuto del file:

		<coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
	      <action>
	        <workflow>
	          <app-path>${workflowPath}</app-path>
	        </workflow>
	      </action>
		</coordinator-app>

	Notare che vengono usate variabili `${...}` che saranno sostituite da valori nella definizione del processo. Le variabili sono:

	* **${coordFrequency}**: tempo che intercorre tra l'esecuzione delle istanze del processo.
	* **${coordStart}**: ora di inizio del processo.
	* **${coordEnd}**: ora di fine del processo.
	* **${coordTimezone}**: i processi del coordinatore si trovano in un fuso orario predefinito che non tiene conto dell'ora legale (in genere rappresentato dall'acronimo UTC). Questo fuso orario è definito "fuso orario di elaborazione di Oozie".
	* **${wfPath}**: il percorso del file workflow.xml.

2. Usare CTRL+X, quindi **S** e **INVIO** per salvare il file.

3. Usare il comando seguente per copiarlo nella directory di lavoro del processo:

		hadoop fs -copyFromLocal coordinator.xml /tutorials/useoozie/coordinator.xml

4. Usare il comando seguente per modificare il file **job.xml**:

		nano job.xml

	Apportare le modifiche seguenti:

	* Cambiare `<name>oozie.wf.application.path</name>` in `<name>oozie.coord.application.path</name>`. Indica a Oozie di eseguire il file del coordinatore invece del file del flusso di lavoro.

	* Aggiungere il comando seguente che verrà impostato come variabile usata nel file coordinator.xml per puntare al percorso del file workflow.xml:

		    <property>
		      <name>workflowPath</name>
		      <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
		    </property>

		Sostituire i valori di **mycontainer** e **mystorageaccount** con i valori usati nelle altre voci del file job.xml.

	* Aggiungere quanto segue per definire l'inizio, la fine e la frequenza da usare per il file coordinator.xml:

		    <property>
		      <name>coordStart</name>
		      <value>2015-06-25T12:00Z</value>
		    </property>

		    <property>
		      <name>coordEnd</name>
		      <value>2015-06-27T12:00Z</value>
		    </property>

		    <property>
		      <name>coordFrequency</name>
		      <value>1440</value>
		    </property>

		    <property>
		      <name>coordTimezone</name>
		      <value>UTC</value>
		    </property>

		Imposta l'ora di inizio sulle 12:00 del 25 giugno 2015, la data di fine sul 27 giugno 2015 e l'intervallo per l'esecuzione di questo processo su ogni giorno (la frequenza è espressa in minuti, quindi 24 ore x 60 minuti = 1440 minuti). Infine, il fuso orario è impostato su UTC.

5. Usare CTRL+X, quindi **S** e **INVIO** per salvare il file.

6. Per eseguire il processo, usare il comando seguente:

		oozie job -config job.xml -run

	Il processo verrà inviato e avviato.

7. Visitando l'interfaccia utente Web di Oozie e selezionando la scheda **Coordinator Jobs**, è possibile ottenere informazioni simili alle seguenti:

	![scheda coordinator jobs](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

	Notare la voce **Next Materialization** che corrisponde all'esecuzione successiva del processo.

8. Come per il processo del flusso di lavoro precedente, selezionando la voce del processo nell'interfaccia utente Web, verranno visualizzate informazioni sul processo:

	![Informazioni sui processi coordinatore](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

	Si noti che sono visualizzate solo le esecuzioni riuscite del processo, non le singole azioni all'interno del flusso di lavoro pianificato. Per visualizzarle, selezionare una delle voci in **Action**. Verranno visualizzate informazioni simili a quelle recuperate per il processo del flusso di lavoro precedente.

	![Informazioni sull'azione](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

##Risoluzione dei problemi

Durante la risoluzione dei problemi relativi ai processi Oozie, l'interfaccia utente di Oozie risulta molto utile perché consente di visualizzare facilmente entrambi i log di Oozie, oltre ai collegamenti ai log di JobTracker per le attività di MapReduce, ad esempio le query Hive. In generale, il modello per la risoluzione dei problemi deve essere il seguente:

1. Visualizzare il processo nell'interfaccia utente Web di Oozie.

2. Se si verifica un errore o un problema per un'azione specifica, selezionare l'azione per vedere se il campo **Error Message** fornisce altre informazioni sull'errore.

3. Se disponibile, usare l'URL dall'azione per visualizzare altri dettagli (ad esempio, i log di JobTracker) relativi all'azione.

Di seguito sono riportati errori specifici che possono verificarsi e come risolverli.

###JA009: Cannot initialize cluster

**Sintomi**: lo stato del processo cambia in **SUSPENDED**. I dettagli del processo mostreranno lo stato di RunHiveScript come **START\_MANUAL**. Selezionando l'azione verrà visualizzato il messaggio di errore seguente:

	JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: gli indirizzi WASB usati nel file **job.xml** non includono il contenitore di archiviazione o il nome dell'account di archiviazione. Il formato dell'indirizzo WASB deve essere `wasb://containername@storageaccountname.blob.core.windows.net`.

**Risoluzione**: modificare gli indirizzi WASB usati dal processo.

###JA002: Oozie is not allowed to impersonate &lt;USER>

**Sintomi**: lo stato del processo cambia in **SUSPENDED**. I dettagli del processo mostreranno lo stato di RunHiveScript come **START\_MANUAL**. Selezionando l'azione verrà visualizzato il messaggio di errore seguente:

	JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: le impostazioni di autorizzazione correnti non consentono a Oozie di rappresentare l'account utente specificato.

**Risoluzione**: Oozie è autorizzato a rappresentare gli utenti nel gruppo **users**. Usare `groups USERNAME` per visualizzare i gruppi di cui l'account utente è membro. Se l'utente non è un membro del gruppo **users**, usare il comando seguente per aggiungere l'utente al gruppo:

	sudo adduser USERNAME users

> [AZURE.NOTE] Potrebbero essere necessari alcuni minuti prima che HDInsight riconosca che l'utente è stato aggiunto al gruppo.

###ERRORE dell'utilità di avvio (Sqoop)

**Sintomi**: lo stato del processo cambia in **KILLED**. I dettagli del processo mostreranno lo stato di RunSqoopExport come **ERROR**. Selezionando l'azione verrà visualizzato il messaggio di errore seguente:

	Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: Sqoop non è in grado di caricare il driver del database necessario per accedere al database.

**Risoluzione**: quando si usa Sqoop da un processo Oozie, è necessario includere il driver del database con le altre risorse (ad esempio, workflow.xml) usate dal processo.

È anche necessario fare riferimento all'archivio contenente il driver del database dalla sezione `<sqoop>...</sqoop>` di workflow.xml.

Ad esempio, per il processo in questo documento si useranno i passaggi seguenti:

1. Copiare il file sqljdbc4.jar nella directory /tutorials/useoozie:

		 hadoop fs -copyFromLocal /usr/share/java/sqljdbc_4.1/enu/sqljdbc4.jar /tutorials/useoozie/sqljdbc4.jar

2. Modificare il file workflow.xml per aggiungere il codice seguente in una nuova riga sopra `</sqoop>`:

		<archive>sqljdbc4.jar</archive>

##Passaggi successivi
In questa esercitazione si è appreso come definire un flusso di lavoro di Oozie e come eseguire un processo Oozie. Per altre informazioni sull'uso di HDInsight, vedere gli articoli seguenti:

- [Usare il coordinatore Oozie basato sul tempo con HDInsight][hdinsight-oozie-coordinator-time]
- [Caricare dati per processi Hadoop in HDInsight][hdinsight-upload-data]
- [Usare Sqoop con Hadoop in HDInsight][hdinsight-use-sqoop]
- [Usare Hive con Hadoop in HDInsight][hdinsight-use-hive]
- [Usare Pig con Hadoop in HDInsight][hdinsight-use-pig]
- [Sviluppare programmi MapReduce Java per HDInsight][hdinsight-develop-mapreduce]


[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563



[azure-data-factory-pig-hive]: data-factory-pig-hive-activities.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md


[hdinsight-use-sqoop]: hdinsight-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md

[hdinsight-develop-streaming-jobs]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: powershell-install-configure.md
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/it-IT/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

<!---HONumber=AcomDC_0218_2016-->