<properties
	pageTitle="Introduzione a un emulatore di Hadoop per HDInsight | Microsoft Azure"
	description="Utilizzare un emulatore installato con un'esercitazione MapReduce e altri esempi per informazioni sull'ecosistema di Hadoop. L’emulatore di HDInsight funziona come un ambiente sandbox di Hadoop."
	keywords="emulatore, ecosistema di hadoop, ambiente sandbox di hadoop, esercitazione mapreduce"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="11/29/2015"
	ms.author="nitinme"/>

# Introduzione all’ecosistema Hadoop con HDInsight Emulator, un ambiente sandbox Hadoop

In questa esercitazione sono usate le operazioni iniziali per l'utilizzo di cluster Hadoop in Microsoft HDInsight Emulator per Azure (in precedenza HDInsight Server Developer Preview). HDInsight Emulator include gli stessi componenti dell'ecosistema Hadoop disponibili in Azure HDInsight. Per informazioni dettagliate, incluse informazioni sulle versioni distribuite, vedere [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md).

Una volta installato l'emulatore, seguire un'esercitazione MapReduce per il conteggio delle parole e quindi eseguire gli esempi.

> [AZURE.NOTE] HDInsight Emulator include solo un cluster Hadoop, ma non HBase o Storm.


HDInsight Emulator offre un ambiente di sviluppo locale molto simile a un ambiente sandbox Hadoop. Se si ha familiarità con Hadoop, è possibile iniziare a usare HDInsight Emulator tramite il file system distribuito Hadoop (HDFS). In HDInsight il file system predefinito è l'archivio BLOB di Azure. Di conseguenza, in ultima analisi sarà preferibile sviluppare i processi usando l'archivio BLOB di Azure. Per usare l'archivio BLOB di Azure con HDInsight Emulator, è necessario apportare modifiche alla configurazione dell'emulatore.

> [AZURE.NOTE] HDInsight Emulator è in grado di usare solo una distribuzione a nodo singolo.


## Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

- HDInsight Emulator richiede una versione a 64 bit di Windows. È quindi necessario disporre di una delle versioni seguenti:

	- Windows 10
	- Windows 8
	- Windows Server 2012

- **Azure PowerShell**. Vedere [Installare e usare Azure PowerShell](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a name="install"></a>Installare HDInsight Emulator

È possibile installare Microsoft HDInsight Emulator tramite Installazione guidata piattaforma Web Microsoft.

> [AZURE.NOTE] HDInsight Emulator supporta attualmente solo sistemi operativi in lingua inglese. Se nel computer è installata una versione precedente dell'emulatore, è necessario disinstallare i due componenti seguenti da Pannello di controllo/Programmi e funzionalità prima di installare la versione più recente: <ul> <li>Microsoft HDInsight Emulator per Azure o HDInsight Developer Preview, a seconda di quale è installato</li> <li>Hortonworks Data Platform</li> </ul>


**Per installare HDInsight Emulator**

1. Aprire Internet Explorer, quindi passare alla [pagina di installazione di Microsoft HDInsight Emulator per Azure][hdinsight-emulator-install].
2. Fare clic su **Installa ora**.
3. Fare clic su **Esegui** quando richiesto per l'installazione di HDINSIGHT.exe nella parte inferiore della pagina.
4. Per completare l'installazione, fare clic sul pulsante **Sì** nella finestra **Controllo dell'account utente** visualizzata. Viene visualizzata la finestra Installazione guidata piattaforma Web.
6. Fare clic su **Installa** nella parte inferiore della pagina.
7. Fare clic su **Accetto** per accettare i termini del Contratto di licenza.
8. Verificare che in Installazione guidata piattaforma Web sia visualizzato il messaggio **Installazione riuscita dei seguenti prodotti**, quindi fare clic su **Fine**.
9. Fare clic su **Esci** per chiudere la finestra di Installazione guidata piattaforma Web.

**Per verificare l'installazione di HDInsight Emulator**

Al termine dell'installazione dovrebbero essere visibili tre icone sul desktop, con i collegamenti seguenti:

- **Riga dei comandi di Hadoop**: prompt dei comandi di Hadoop da cui vengono eseguiti i processi MapReduce, Pig e Hive in HDInsight Emulator.

- **Stato NameNode di Hadoop**: NameNode gestisce una directory basata su albero per tutti i file in HDFS e tiene traccia delle posizioni di archiviazione dei dati per tutti i file in un cluster Hadoop. I client comunicano con NameNode per individuare le posizioni di archiviazione dei nodi dati per tutti i file.

- **Stato di Hadoop Yarn**: utilità di analisi dei processi che alloca attività MapReduce ai nodi in un cluster.

Durante l'installazione vengono installati anche alcuni servizi locali. Nella figura seguente viene mostrata la finestra Services:

![Servizi dell’ecosistema di Hadoop elencati nella finestra dell'emulatore.][image-hdi-emulator-services]

I servizi correlati a HDInsight Emulator non vengono avviati per impostazione predefinita. Per avviare i servizi, dalla riga di comando di Hadoop eseguire **start\_local\_hdp\_services.cmd** in C:\\hdp (percorso predefinito). Per avviare automaticamente i servizi dopo aver riavviato il computer, eseguire **set-onebox-autostart.cmd**.

Per informazioni sui problemi noti relativi all'installazione e all'esecuzione di HDInsight Emulator, vedere [Note sulla versione di HDInsight Emulator](hdinsight-emulator-release-notes.md). Il file di log dell'installazione è disponibile in **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

##<a name="vstools"></a>Usare l'emulatore con HDInsight Tools per Visual Studio

È possibile usare HDInsight Tools per Visual Studio per connettersi a HDInsight Emulator. Per informazioni su come usare Strumenti di Visual Studio con cluster HDInsight in Azure, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

### Installare gli strumenti di HDInsight per l'emulatore

Per istruzioni su come installare Strumenti di Visual Studio per HDInsight, vedere [qui](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation).

### Connettersi a HDInsight Emulator

1. Aprire Visual Studio.
2. Scegliere **Esplora server** dal menu **Visualizza** per aprire la finestra Esplora server.
3. Espandere **Azure**, fare clic con il pulsante destro del mouse su **HDInsight** e quindi scegliere **Connetti a HDInsight Emulator**.

	 ![Vista di Visual Studio: connettersi all’emulatore di HDInsight dal menu.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. Nella finestra di dialogo verificare i valori per gli endpoint WebHCat, HiveServer2 e WebHDFS e quindi fare clic su **Avanti**. I valori immessi per impostazione predefinita funzionano se non sono state apportate modifiche alla configurazione predefinita dell'emulatore. Se sono state apportate modifiche, aggiornare i valori nella finestra di dialogo e quindi fare clic su Avanti.

	![Connettersi alla finestra di dialogo dell’emulatore di HDInsight con le impostazioni.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. Una volta stabilita la connessione, fare clic su **Fine**. HDInsight Emulator dovrebbe ora essere visualizzato in Esplora server.

	![Esplora server con l’emulatore locale di HDInsight, un ambiente sandbox di Hadoop, connesso.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

Una volta stabilita la connessione, è possibile usare Strumenti di Visual Studio per HDInsight con l'emulatore, proprio allo stesso modo in cui verrebbe usato con un cluster Azure HDInsight. Per istruzioni su come usare Strumenti di Visual Studio con cluster Azure HDInsight, vedere [Introduzione all'uso di HDInsight Hadoop Tools per Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## Risoluzione dei problemi: connessione di HDInsight Tools a HDInsight Emulator

1. Durante la connessione a HDInsight Emulator, anche se la finestra di dialogo mostra che la connessione di HiveServer2 è avvenuta correttamente, è necessario impostare manualmente la proprietà **hive.security.authorization.enabled** su **false** nel file di configurazione Hive in C:\\hdp\\hive-*versione*\\conf\\hive-site.xml e quindi riavviare l'emulatore locale. HDInsight Tools per Visual Studio si connette a HiveServer2 solo quando si visualizzano in anteprima le prime 100 righe della tabella. Se non si intende usare questa query, è possibile lasciare la configurazione Hive così com'è.

2. Se si usa l'allocazione degli IP dinamici (DHCP) nel computer che esegue HDInsight Emulator, potrebbe essere necessario aggiornare C:\\hdp\\hadoop-*versione*\\etc\\hadoop\\core-site.xml e modificare il valore della proprietà **hadoop.proxyuser.hadoop.hosts** in (*). In questo modo, l'utente di Hadoop può connettersi da tutti gli host per rappresentare l'utente immesso in Visual Studio.

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Potrebbe essere visualizzato un errore quando Visual Studio tenta di connettersi al servizio WebHCat ("errore": "Impossibile trovare il processo job\_XXXX\_0001"). In questo caso, è necessario riavviare il servizio WebHCat e riprovare. Per riavviare il servizio WebHCat, avviare la console MMC **Servizi**, fare clic con il pulsante destro del mouse su **Apache Hadoop Templeton** (nome precedente del servizio WebHCat) e scegliere **Riavvia**.

##<a name="runwordcount"></a>Esercitazione MapReduce per il conteggio delle parole

A questo punto, dopo aver configurato HDInsight Emulator nella workstation, provare l’esercitazione MapReduce per verificare l'installazione. Caricare innanzitutto alcuni file di dati in HDFS, quindi eseguire un processo MapReduce per il conteggio delle parole, in modo da contare la frequenza delle parole specifiche in tali file.

Il programma MapReduce per il conteggio di parole è stato incluso nel pacchetto *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*. Il file JAR si trova nella cartella *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce*.

Il processo MapReduce per il conteggio delle parole accetta due argomenti:

- Una cartella di input. Come cartella di input verrà usata **hdfs://localhost/user/HDIUser*.
- Una cartella di output. Come cartella di output si userà **hdfs://localhost/user/HDIUser/WordCount_Output*. La cartella di output non può essere una cartella esistente. In caso contrario, il processo MapReduce non riuscirà. Se si desidera eseguire il processo MapReduce una seconda volta, sarà necessario specificare una cartella di output diversa o eliminare la cartella di output esistente.

**Per eseguire il processo MapReduce per il conteggio di parole**

1. Dal desktop fare doppio clic su **Riga di comando di Hadoop** per aprire la finestra della riga di comando di Hadoop. La cartella corrente dovrebbe essere la seguente:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	In caso contrario, eseguire il comando seguente:

		cd %hadoop_home%

2. Eseguire i comandi seguenti di Hadoop per creare una cartella HDFS per l'archiviazione dei file di input e di output:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. Eseguire il comando seguente di Hadoop per copiare alcuni file di testo locali in HDFS:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. Eseguire il comando seguente per elencare i file nella cartella /user/HDIUser:

		hadoop fs -ls /user/HDIUser

	Dovrebbero essere visualizzati i file seguenti:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Eseguire il comando seguente per eseguire il processo MapReduce per il conteggio di parole:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Eseguire il comando seguente per elencare il numero di parole contenenti "windows" dal file di output:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	L'output dovrebbe essere analogo al seguente:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Per altre informazioni sui comandi Hadoop, vedere il [manuale relativo ai comandi Hadoop][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Analizzare i dati di log Web di esempio

L'installazione di HDInsight Emulator include alcuni esempi che permettono agli utenti di apprendere il funzionamento dei servizi basati su Apache Hadoop in Windows. Questi esempi mostrano alcune attività necessarie in genere per l'elaborazione di un set di Big Data. Grazie agli esempi dell’esercitazione MapReduce, sarà possibile acquisire familiarità con il modello di programmazione di MapReduce e con il suo ecosistema.

I dati di esempio sono organizzati in base agli scenari di elaborazione dei dati di log IIS World Wide Web Consortium (W3C). È disponibile uno strumento di generazione di dati, che permette di creare e importare set di dati di varie dimensioni in HDFS o nell'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure per HDInsight](../hdinsight-use-blob-storage.md). È quindi possibile eseguire i processi MapReduce, Pig o Hive sulle pagine di dati generati dallo script di Azure PowerShell. Gli script Pig e Hive forniscono un livello di astrazione superiore a MapReduce e verranno infine compilati nei programmi MapReduce. L’utente può eseguire alcuni processi per osservare gli effetti derivanti dall'utilizzo delle diverse tecnologie e l'impatto della dimensione dei dati sull'esecuzione delle attività di elaborazione.

### Contenuto della sezione

- [Scenari relativi ai dati di log IIS W3C](#scenarios)
- [Caricare dati di log W3C di esempio](#loaddata)
- [Eseguire processi MapReduce Java](#javamapreduce)
- [Eseguire processi Hive](#hive)
- [Eseguire processi Pig](#pig)
- [Ricompilare gli esempi](#rebuild)

###<a name="scenarios"></a>Scenari relativi ai dati di log IIS W3C

Lo scenario W3C permette di generare e importare dati di log IIS W3C in HDFS o nell'archivio BLOB di Azure in tre dimensioni diverse: 1 MB (piccola), 500 MB (media) e 2 GB (grande). Include tre tipi di processo e consente di implementarli in C#, Java, Pig e Hive.

- **totalhits**: calcola il numero totale di richieste per una pagina specifica.
- **avgtime**: calcola il tempo medio (in secondi) impiegato per una richiesta per ogni pagina.
- **errors**: calcola il numero di errori per ogni pagina all'ora per le richieste per cui è stato generato lo stato 404 o 500.

Questi esempi e la relativa documentazione non consentono di ottenere una conoscenza approfondita o un'implementazione completa delle tecnologie chiave di Hadoop. Il cluster usato include solo un singolo nodo. Non è pertanto possibile osservare, in questa versione, l'effetto dell'aggiunta di più nodi.

###<a name="loaddata"></a>Caricare dati di log W3C di esempio

La generazione e l'importazione dei dati in HDFS avviene tramite lo script importdata.ps1 di Azure PowerShell.

**Per importare dati di log W3C di esempio**

1. Aprire la riga di comando di Hadoop dal desktop.
2. Passare alla directory **C:\\hdp\\GettingStarted**.
3. Per generare e importare i dati in HDFS, eseguire il comando seguente:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	Se invece si vuole caricare i dati nell'archivio BLOB di Azure, vedere [Connettersi all'archivio BLOB di Azure](#blobstorage).

4. Eseguire il comando seguente dalla riga di comando di Hadoop per elencare i file importati in HDFS:

		hadoop fs -ls -R /w3c

	L'output dovrebbe essere simile al seguente:

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Per verificare il contenuto del file, eseguire il comando seguente per visualizzare uno dei file di dati nella finestra della console:

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Il file di dati è stato creato e importato in HDFS. È possibile iniziare a eseguire diversi processi Hadoop.

###<a name="javamapreduce"></a> Eseguire processi MapReduce Java

MapReduce è il motore di calcolo di base per Hadoop. Per impostazione predefinita, viene implementato in Java, ma sono disponibili anche esempi che si avvalgono di .NET ed esempi di streaming Hadoop in C#. La sintassi per l'esecuzione di un processo MapReduce è la seguente:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Il file con estensione jar e i file di origine si trovano nella cartella C:\\Hadoop\\GettingStarted\\Java.

**Per eseguire un processo MapReduce per il calcolo degli accessi alle pagine Web**

1. Aprire la riga di comando di Hadoop.
2. Passare alla directory **C:\\hdp\\GettingStarted**.
3. Eseguire il comando seguente per rimuovere la directory di output, nel caso in cui esista già. Se tale cartella esiste già, il processo MapReduce avrà esito negativo.

		hadoop fs -rm -r /w3c/output

3. Eseguire il comando seguente:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	La tabella seguente illustra gli elementi del comando: <table border="1"> <tr><td>Parametro</td><td>Nota</td></tr> <tr><td>w3c\_scenarios.jar</td><td>Il file JAR si trova nella cartella C:\\hdp\\GettingStarted\\Java.</td></tr> <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Il tipo può essere sostituito da uno dei seguenti: <ul> <li>microsoft.hadoop.w3c.AverageTimeTaken</li> <li>microsoft.hadoop.w3c.ErrorsByPage</li> </ul></td></tr> <tr><td>/w3c/input/small/data\_w3c\_small.txt</td><td>Il file di input può essere sostituito dai seguenti: <ul> <li>/w3c/input/medium/data\_w3c\_medium.txt</li> <li>/w3c/input/large/data\_w3c\_large.txt</li> </ul></td></tr> <tr><td>/w3c/output</td><td>Nome della cartella di output.</td></tr> </table>

4. Eseguire il comando seguente per visualizzare il file di output:

		hadoop fs -cat /w3c/output/part-00000

	L'output deve essere analogo al seguente:

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	Vengono effettuati 3360 accessi alla pagina Default.aspx e così via. Provare a eseguire di nuovo i comandi sostituendo i valori come suggerito nella tabella precedente e osservare in che modo l'output cambia in base al tipo di processo e alle dimensioni dei dati.

### <a name="hive"></a>Eseguire processi Hive
Il motore di query Hive può risultare familiare agli analisti con competenze avanzate in Structured Query Language (SQL), poiché offre un'interfaccia analoga a quella di SQL e un modello di dati relazionale per HDFS. Hive usa un linguaggio denominato HiveQL, che è molto simile a SQL. Hive fornisce un livello di astrazione nel framework MapReduce basato su Java e le query Hive vengono compilate in MapReduce in fase di esecuzione.

**Per eseguire un processo Hive**

1. Aprire la riga di comando di Hadoop.
2. Passare alla directory **C:\\hdp\\GettingStarted**.
3. Eseguire il comando seguente per rimuovere la cartella **/w3c/hive/input**, nel caso in cui esista già. Se la cartella esiste già, il processo Hive non riuscirà.

		hadoop fs -rmr /w3c/hive/input

4. Eseguire il comando seguente per creare le cartelle **/w3c/hive/input** e quindi copiare i file di dati nella cartella /hive/input:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Eseguire il comando seguente per eseguire il file di script **w3ccreate.hql**. Lo script crea una tabella di Hive e carica i dati in tale tabella:

	> [AZURE.NOTE] In questa fase è anche possibile usare Strumenti di Visual Studio per HDInsight per eseguire la query Hive. Aprire Visual Studio, creare un nuovo progetto e dal modello di HDInsight selezionare **Applicazione Hive**. Una volta aperto il progetto, aggiungere la query come nuovo elemento. La query è disponibile in **C:/hdp/GettingStarted/Hive/w3c**. Dopo aver aggiunto la query al progetto, sostituire **${hiveconf:input}** con **/w3c/hive/input** e quindi fare clic su **Invia**.

		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	L'output sarà simile al seguente:

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Eseguire il comando seguente per eseguire il file di script HiveQL **w3ctotalhitsbypage.hql**.

	> [AZURE.NOTE] Come spiegato prima, è possibile eseguire questa query anche usando Strumenti di Visual Studio per HDInsight.

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	La tabella seguente descrive gli elementi del comando: <table border="1"> <tr><td>File</td><td>Descrizione</td></tr> <tr><td>C:\\hdp\\hive-0.13.0.2.1.3.0-1981\\bin\\hive.cmd</td><td>Script del comando Hive.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql</td><td> È possibile sostituire il file di script Hive con uno dei seguenti: <ul> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li> </ul> </td></tr>

	</table>

	Di seguito è riportato lo script HiveQL di w3ctotalhitsbypage.hql:

		SELECT filtered.cs_uri_stem,COUNT(*)
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	La fine dell'output sarà simile alla seguente:

		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Si noti che il primo passaggio in ogni processo consiste nella creazione di una tabella e nel caricamento di dati nella tabella dal file creato in precedenza. È possibile passare al file creato cercando all'interno del nodo /Hive in HDFS mediante il comando seguente:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Eseguire processi Pig

I processi Pig usano un linguaggio di flusso di dati chiamato *Pig Latin*. Le astrazioni Pig Latin offrono strutture di dati più avanzate rispetto a MapReduce e svolgono per Hadoop la stessa funzione di SQL per i sistemi di gestione di database relazionali.


**Per eseguire i processi Pig**

1. Aprire la riga di comando di Hadoop.
2. Cambiare la directory, specificando la cartella **C:\\hdp\\GettingStarted**.
3. Eseguire il comando seguente per inviare il processo Pig:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	La tabella seguente illustra gli elementi del comando: <table border="1"> <tr><td>File</td><td>Descrizione</td></tr> <tr><td>C:\\hdp\\pig-0.12.1.2.1.3.0-1981\\bin\\pig.cmd</td><td>Script del comando Pig.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig</td><td> È possibile sostituire il file di script Pig Latin con uno dei seguenti: <ul> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li> </ul> </td></tr> <tr><td>/w3c/input/small/data\_w3c\_small.txt</td><td> È possibile sostituire il parametro con un file più grande:

	<ul>
<li>/w3c/input/medium/data_w3c_medium.txt</li>
<li>/w3c/input/large/data_w3c_large.txt</li>
</ul></td></tr> </table>

	L'output dovrebbe essere simile al seguente:

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

Poiché gli script Pig vengono compilati in processi MapReduce e potenzialmente in più processi MapReduce, è possibile che siano visibili più processi MapReduce in esecuzione durante l'elaborazione di un processo Pig.

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Connettersi all'archivio BLOB di Azure
HDInsight Emulator usa HDFS come file system predefinito. Tuttavia, Azure HDInsight usa l'archivio BLOB di Azure come file system predefinito. È possibile configurare HDInsight Emulator in modo da usare l'archivio BLOB di Azure invece dell'archiviazione locale. Seguire le istruzioni riportate di seguito per creare un contenitore di archiviazione in Azure e connetterlo a HDInsight Emulator.

>[AZURE.NOTE] Per altre informazioni sul modo in cui HDInsight usa l'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight](../hdinsight-use-blob-storage.md).

Prima di iniziare con le istruzioni riportate di seguito è necessario aver creato un account di archiviazione. Per istruzioni, vedere [Come creare un account di archiviazione](../storage-create-storage-account.md).

**Per creare un contenitore**

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Fare clic su **NUOVO** a sinistra, fare clic su **Dati + archiviazione**, quindi fare clic su **Archiviazione**.
3. Nel pannello Account di archiviazione, configurare le proprietà come illustrato nella schermata riportata di seguito.
	
	![Creare un account di archiviazione](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.storage.png)

	Selezionare **Aggiungi alla schermata iniziale**, quindi scegliere **Crea**.
4. Una volta creato l'account di archiviazione, dal pannello del nuovo account di archiviazione, fare clic su **Contenitori** per aprire il pannello di contenitori e fare clic su **Aggiungi**.
5. Immettere il nome del contenitore e quindi fare clic su **Seleziona**.

	![Creare un contenitore](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.create.container.png)

Prima di accedere all'account di archiviazione di Azure è necessario aggiungere il nome dell'account e la chiave dell'account al file di configurazione.

**Per configurare la connessione a un account di archiviazione di Azure**

1. Aprire **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml** nel Blocco note.
2. Aggiungere il tag <property> seguente accanto agli altri tag <property>:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	È necessario sostituire <StorageAccountName> e <StorageAccountKey> con i valori corrispondenti alle informazioni sull'account di archiviazione in uso.

3. Salvare la modifica. Sarà necessario riavviare i servizi Hadoop.

Usare la sintassi seguente per accedere all'account di archiviazione:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Ad esempio:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Eseguire Azure PowerShell
Alcuni dei cmdlet di Azure PowerShell sono supportati anche in HDInsight Emulator, inclusi i seguenti:

- Cmdlet di definizione dei processi HDInsight

	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition
	- New-AzureHDInsightHiveJobDefinition
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Di seguito viene riportato un esempio per l'invio di un processo Hadoop:

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Quando si chiama Get-Credential, verrà visualizzato un prompt. È necessario usare **hadoop** come nome utente. Per la password è possibile usare qualsiasi stringa. Il nome del cluster è sempre ****http://localhost:50111**.

Per altre informazioni sull'invio di processi Hadoop, vedere [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md). Per altre informazioni sui cmdlet di Azure PowerShell per HDInsight, vedere [Documentazione di riferimento relativa ai cmdlet di HDInsight][hdinsight-powershell-reference].


##<a name="remove"></a> Rimuovere HDInsight Emulator
Nel computer in cui è installato l'emulatore aprire il Pannello di controllo e in **Programmi** fare clic su **Disinstalla un programma**. Nell'elenco dei programmi installati fare clic con il pulsante destro del mouse su **Microsoft HDInsight Emulator per Azure**, quindi scegliere **Disinstalla**.


##<a name="nextsteps"></a> Passaggi successivi
In questa esercitazione MapReduce è stato installato HDInsight Emulator, un ambiente sandbox di Hadoop, e sono stati eseguiti alcuni processi Hadoop. Per altre informazioni, vedere gli articoli seguenti:

- [Introduzione all'uso di Azure HDInsight](../hdinsight-get-started.md)
- [Sviluppare programmi MapReduce Java per HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Note sulla versione di HDInsight Emulator](hdinsight-emulator-release-notes.md)
- [Forum MSDN relativo a HDInsight](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: ../install-configure-powershell.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=AcomDC_0128_2016-->