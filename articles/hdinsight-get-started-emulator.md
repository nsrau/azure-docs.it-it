<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" author="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Introduzione all'utilizzo di HDInsight Emulator

In questa esercitazione sono usate le operazioni iniziali per l'utilizzo di cluster Hadoop in Microsoft HDInsight Emulator per Azure (in precedenza HDInsight Server Developer Preview). HDInsight Emulator include gli stessi componenti dell'ecosistema Hadoop disponibili in Azure HDInsight. Per informazioni dettagliate, incluse informazioni sulle versioni distribuite, vedere [Versione di Hadoop inclusa in Azure HDInsight][].

HDInsight Emulator offre un ambiente di sviluppo locale per Azure HDInsight. Se si ha familiarità con Hadoop, sarà possibile iniziare a utilizzare HDInsight Emulator tramite HDFS. In HDInsight, tuttavia, il file system predefinito corrisponde all'archivio BLOB di Azure. Sarà quindi necessario, prima o poi, sviluppare i processi utilizzando tale file system. È possibile iniziare lo sviluppo sull'archiviazione BLOB mediante l'emulatore di archiviazione di Azure e utilizzando solo un sottoinsieme ridotto dei dati disponibili. In HDInsight Emulator non sono necessarie modifiche a livello di configurazione, ma solo un nome di account di archiviazione diverso. È quindi possibile verificare localmente i processi sull'archivio BLOB di Azure, utilizzando anche in questo caso solo un sottoinsieme dei dati. Sarà necessario apportare una modifica a livello di configurazione in HDInsight Emulator. Sarà infine possibile spostare la parte del processo relativa al calcolo in HDInsight ed eseguire il processo sui dati di produzione.

> [WACOM.NOTE] HDInsight Emulator è in grado di utilizzare solo una distribuzione a nodo singolo.

Per un'esercitazione relativa all'utilizzo di HDInsight, vedere [Introduzione all'utilizzo di Azure HDInsight][].

**Prerequisiti**
 Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   HDInsight Emulator richiede una versione a 64 bit di Windows. È quindi necessario disporre di una delle versioni seguenti:

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack1
    -   Windows 8
    -   Windows Server 2012
-   Installare e configurare Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][].

## Contenuto dell'esercitazione:

-   [Installazione di HDInsight Emulator][]
-   [Esecuzione dell'esempio relativo al conteggio di parole][]
-   [Esecuzione degli esempi introduttivi][]
-   [Connessione all'archivio BLOB di Azure][]
-   [Esecuzione di HDInsight PowerShell][]
-   [Passaggi successivi][]

## <a name="install"></a>Installazione di HDInsight Emulator

È possibile installare Microsoft HDInsight Emulator tramite Installazione guidata piattaforma Web Microsoft.

> [WACOM.NOTE] HDInsight Emulator supporta attualmente solo sistemi operativi in lingua inglese.

> [WACOM.NOTE] Se Microsoft HDInsight Developer Preview è stato installato in precedenza, sarà innanzitutto necessario disinstallare i due componenti precedenti da Pannello di controllo/Programmi e funzionalità.
>
> -   HDInsight Developer Preview
> -   Hortonworks Data Platform Developer Preview
>


**Per installare HDInsight Emulator**

1.  Aprire Internet Explorer, quindi passare alla [pagina di installazione di Microsoft HDInsight Emulator per Azure][].
2.  Fare clic su **Installa ora**.
3.  Fare clic su **Esegui** quando richiesto per l'installazione di HDINSIGHT.exe nella parte inferiore della pagina.
4.  Per completare l'installazione, fare clic sul pulsante **Sì** nella finestra **Controllo dell'account utente** visualizzata. Verrà visualizzata la finestra di Installazione guidata piattaforma Web 4.6.
5.  Fare clic su **Installa** nella parte inferiore della pagina.
6.  Fare clic su **Accetto** per accettare i termini del Contratto di licenza.
7.  Verificare che sia visualizzato il messaggio **Installazione riuscita dei seguenti prodotti**, quindi fare clic su **Fine**.
8.  Fare clic su **Esci** per chiudere la finestra di Installazione guidata piattaforma Web 4.6.

    Al termine dell'installazione dovrebbero essere visibili tre icone sul desktop, con i collegamenti seguenti:

    -   **Hadoop Command Line**: prompt dei comandi di Hadoop, da cui vengono eseguiti i processi MapReduce, Pig e Hive in HDInsight Emulator.

    -   **Hadoop Name Node Status**: NameNode gestisce una directory basata su albero per tutti i file in HDFS e tiene traccia delle posizioni di archiviazione dei dati per tutti i file in un cluster Hadoop. I client comunicano con NameNode per individuare le posizioni di archiviazione dei nodi dati per tutti i file.

    -   **Hadoop MapReduce Status**: funzionalità di analisi dei processi che alloca le attività MapReduce ai nodi di un cluster.

    Durante l'installazione vengono installati anche alcuni servizi locali. Nella figura seguente viene mostrata la finestra Services:

    ![HDI.Emulator.Services][]

    Per informazioni sui problemi noti relativi all'installazione e all'esecuzione di HDInsight Server, vedere le [Note sulla versione di HDInsight Emulator][]. Il file di log dell'installazione è disponibile in **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

## <a name="runwordcount"></a>Esecuzione di un processo MapReduce relativo al conteggio di parole

HDInsight Emulator è stato configurato nella workstation. È possibile eseguire un processo MapReduce per verificare l'installazione. Caricare innanzitutto alcuni file di testo in HDFS, quindi eseguire un processo MapReduce per il conteggio delle parole, in modo da contare la frequenza delle parole in tali file.

Il programma MapReduce per il conteggio delle parole è stato incluso nel pacchetto *hadoop-examples.jar*. Il file con estensione jar si trova nella cartella *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT*.

Di seguito è riportata la sintassi per il comando jar:

    hadoop jar <jar> [mainClass] args...

Verranno utilizzati anche alcuni comandi fs. Per altre informazioni sui comandi Hadoop, vedere il [manuale relativo ai comandi Hadoop][].

Il processo MapReduce per il conteggio delle parole accetta due argomenti: una cartella di input e una cartella di output. Come cartella di input verrà utilizzata la cartella *hdfs://localhost/user/HDIUser* e *hdfs://localhost/user/HDIUser/WordCount\_Output* verrà utilizzata come directory di output. La cartella di output non può essere una cartella esistente. In caso contrario, il processo MapReduce avrà esito negativo. Se si desidera eseguire il processo MapReduce una seconda volta, sarà necessario specificare una cartella di output diversa o eliminare la cartella di output esistente.

**Per eseguire il processo MapReduce relativo al conteggio di parole**

1.  Dal desktop fare doppio clic su **Hadoop Command Line** per aprire la finestra della riga di comando di Hadoop. La cartella corrente dovrebbe essere la seguente:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    In caso contrario, eseguire il comando seguente:

        cd %hadoop_home%

2.  Eseguire il comando seguente di Hadoop per creare una cartella HDFS per l'archiviazione dei file di input e di output:

        hadoop fs -mkdir /user/HDIUser

3.  Eseguire il comando seguente di Hadoop per copiare alcuni file locali in HDFS:

        hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  Eseguire il comando seguente per elencare i file nella cartella /user/HDIUser:

        hadoop fs -ls /user/HDIUser

    Dovrebbero essere visualizzati i file seguenti:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 8 items
        -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
        -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
        -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
        -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
        -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  Eseguire il comando seguente per eseguire il processo MapReduce relativo al conteggio di parole:

        hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Eseguire il comando seguente per elencare le parole contenenti "windows" dal file di output:

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    L'output dovrebbe essere analogo al seguente:

        c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
        rt-r-00000 | findstr "windows"
        windows 12
        windows+java6.  1
        windows.        3

## <a name="rungetstartedsamples"></a> Esecuzione degli esempi introduttivi

L'installazione di HDInsight Emulator rende disponibili alcuni esempi per consentire ai nuovi utenti di apprendere rapidamente il funzionamento dei servizi basati su Apache Hadoop su Windows. Tali esempi illustrano alcune attività che risultano in genere necessarie per l'elaborazione di un set di Big Data. È consigliabile esaminare gli esempi, in modo da acquisire familiarità con i concetti associati al modello di programmazione di MapReduce e al relativo ecosistema.

Gli esempi sono organizzati in base agli scenari di elaborazione dei dati di log W3C IIS. È disponibile uno strumento di generazione di dati, che consente di creare e importare set di dati di varie dimensioni in HDFS o nell'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][]. È possibile eseguire i processi MapReduce, Pig o Hive sulle pagine di dati generati dallo script di PowerShell. Si noti che gli script di Pig e Hive vengono utilizzati per compilare i programmi MapReduce. Gli utenti possono eseguire alcuni processi per osservare autonomamente gli effetti derivanti dall'utilizzo delle diverse tecnologie e l'impatto della dimensione dei dati sull'esecuzione delle attività di elaborazione.

### Contenuto della sezione

-   [Scenari relativi ai dati di log W3C IIS][]
-   [Caricare dati di log W3C di esempio][]
-   [Eseguire processi MapReduce Java][]
-   [Eseguire processi Hive][]
-   [Eseguire processi Pig][]
-   [Ricompilare gli esempi][]

### <a name="scenarios"></a>Scenari relativi ai dati di log W3C IIS

Lo scenario W3C consente di generare e importare dati di log W3C IIS in HDFS o nell'archivio BLOB di Azure in tre dimensioni diverse, ovvero 1 MB, 500 MB e 2 GB. Include tre tipi di processo e consente di implementarli in C#, Java, Pig e Hive.

-   **totalhits**: consente di calcolare il numero totale di richieste per una pagina specifica.
-   **avgtime**: consente di calcolare il tempo medio necessario (in secondi) per una richiesta per ogni pagina.
-   **errors**: consente di calcolare il numero di errori per pagina, per ora o per le richieste con stato 404 o 500.

Questi esempi e la relativa documentazione non consentono di ottenere una conoscenza approfondita o un'implementazione completa delle tecnologie chiave di Hadoop. Il cluster utilizzato include solo un singolo nodo. Non è pertanto possibile osservare, in questa versione, l'effetto dell'aggiunta di più nodi.

### <a name="loaddata"></a>Caricare dati di log W3C di esempio

La generazione e l'importazione dei dati in HDFS vengono eseguite tramite lo script importdata.ps1 di PowerShell.

**Per caricare dati di log W3C di esempio:**

1.  Aprire la riga di comando di Hadoop dal desktop.
2.  Eseguire il comando seguente per passare alla directory **C:\\Hadoop\\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Per generare e importare i dati in HDFS, eseguire il comando seguente:

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Se si desidera invece caricare i dati nell'archivio BLOB di Azure, vedere [Connessione all'archivio BLOB di Azure][].

4.  Eseguire il comando seguente dalla riga di comando di Hadoop per elencare i file importati in HDFS:

        hadoop fs -lsr /w3c

    L'output dovrebbe essere simile al seguente:

        c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
        -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
        -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
        drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
        -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  Eseguire il comando seguente per visualizzare uno dei file di dati nella finestra della console:

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Il file di dati è stato creato e importato in HDFS. È possibile eseguire diversi processi Hadoop.

### <a name="javamapreduce"></a> Eseguire processi MapReduce Java

MapReduce è il motore di calcolo di base per Hadoop. Per impostazione predefinita, viene implementato in Java, ma sono disponibili anche esempi che si avvalgono di .NET ed esempi di streaming Hadoop in C#. La sintassi per l'esecuzione di un processo MapReduce è la seguente:

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Il file con estensione jar e i file di origine si trovano nella cartella C:\\Hadoop\\GettingStarted\\Java.

**Per eseguire un processo MapReduce per il calcolo degli accessi alle pagine Web**

1.  Aprire la riga di comando di Hadoop.
2.  Eseguire il comando seguente per passare alla directory **C:\\Hadoop\\GettingStarted**:

        cd \Hadoop\GettingStarted

3.  Eseguire il comando seguente per rimuovere la directory di output, nel caso in cui esista già. Se tale cartella esiste già, il processo MapReduce avrà esito negativo.

        hadoop fs -rmr /w3c/output

4.  Eseguire il comando seguente:

        hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    Nella tabella seguente sono illustrati gli elementi di tale comando:

    <table border="1">

    <tr>
    <td>
    Parametro

    </td>
    <td>
    Nota

    </td>
    </tr>

    <tr>
    <td>
    w3c\_scenarios.jar

    </td>
    <td>
    Il file con estensione jar si trova nella cartella C:\\Hadoop\\GettingStarted\\Java.

    </td>
    </tr>

    <tr>
    <td>
    microsoft.hadoop.w3c.TotalHitsForPage

    </td>
    <td>
    È possibile sostituire il tipo con uno dei valori seguenti:
	<ul>
    <li>microsoft.hadoop.w3c.AverageTimeTaken</li>
    <li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul>
    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    È possibile sostituire il file di input con uno dei valori seguenti:
	<ul>
    <li>/w3c/input/medium/data\_w3c\_medium.txt</li>
    <li>/w3c/input/large/data\_w3c\_large.txt</li>
	</ul>
    </td>
    </tr>

    <tr>
    <td>
    /w3c/output

    </td>
    <td>
    Nome della cartella di output.

    </td>
    </tr>

    </table>
    </p>
5.  Eseguire il comando seguente per visualizzare il file di output:

        hadoop fs -cat /w3c/output/part-00000

    L'output deve essere analogo al seguente:

        c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130

    Vengono quindi effettuati 3409 accessi alla pagina Default.aspx e così via.

### <a name="hive"></a>Eseguire processi Hive

Il motore query Hive risulterà familiare ai business analyst con competenze relative a SQL, poiché offre un'interfaccia analoga a quella di SQL e un modello di dati relazionale per HDFS. Hive utilizza un linguaggio denominato HiveQL (o HQL), che è un dialetto di SQL.

**Per eseguire un processo Hive**

1.  Aprire la riga di comando di Hadoop.
2.  Cambiare la directory, specificando la cartella **C:\\Hadoop\\GettingStarted**.
3.  Eseguire il comando seguente per rimuovere la cartella **/w3c/hive/input**, nel caso in cui esista già. Se tale cartella esiste già, il processo Hive avrà esito negativo.

        hadoop fs -rmr /w3c/hive/input

4.  Eseguire il comando seguente per creare la cartella **/w3c/hive/input** e copiare il file di dati dalla workstation in HDFS:

        hadoop fs -mkdir /w3c/hive/input
        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Eseguire il comando seguente per eseguire il file di script **w3ccreate.hql**. Lo script crea una tabella di Hive e carica i dati in tale tabella:

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    Di seguito è riportato lo script HiveQL:

        DROP TABLE w3c;

        CREATE TABLE w3c(
         logdate string,
         logtime string,
         c_ip string,
         cs_username string,
         s_ip string,
         s_port string,
         cs_method string,
         cs_uri_stem string,
         cs_uri_query string,
         sc_status int,
         sc_bytes int,
         cs_bytes int,
         time_taken int,
         cs_agent string, 
         cs_Referrer string)
        ROW FORMAT delimited
        FIELDS TERMINATED BY ' ';

        LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    L'output dovrebbe essere simile al seguente:

        c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea    te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
        Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
        Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
        OK
        Time taken: 0.616 seconds
        OK
        Time taken: 0.139 seconds
        Loading data to table default.w3c
        Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
        OK
        Time taken: 0.573 seconds

6.  Eseguire il comando seguente per eseguire il file di script **w3ctotalhitsbypate.hql** di HiveQL.

        C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    Nella tabella seguente sono illustrati gli elementi di tale comando:

    <table border="1">

    <tr>
    <td>
    File

    </td>
    <td>
    Descrizione

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\hive-0.9.0\\bin\\hive.cmd

    </td>
    <td>
    Script del comando Hive.

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql

    </td>
    <td>
    È possibile sostituire il file di script Hive con uno dei file seguenti:
	<ul>
    <li>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li>
    <li>C:\\Hadoop\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li>
	</ul>
    </td>
    </tr>
    </p>
    </table>
    Di seguito è riportato lo script HiveQL di w3ctotalhitsbypage.hql:

        SELECT filtered.cs_uri_stem,COUNT(*) 
        FROM (
          SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
        ) filtered
        GROUP BY (filtered.cs_uri_stem);

    La fine dell'output dovrebbe essere simile alla seguente:

        MapReduce Total cumulative CPU time: 3 seconds 47 msec
        Ended Job = job_201310291309_0006
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
        rite: 53 SUCCESS
        Total MapReduce CPU Time Spent: 3 seconds 47 msec
        OK
        /Default.aspx   3409
        /Info.aspx      1115
        /UserService    1130
        Time taken: 34.68 seconds

Si noti che il primo passaggio in ogni processo consiste nella creazione di una tabella e nel caricamento di dati nella tabella dal file creato in precedenza. È possibile passare al file creato cercando sotto il nodo /Hive in HDFS mediante il comando seguente:

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Eseguire processi Pig

I processi Pig utilizzano un linguaggio di flusso di dati denominato *Pig Latin*. Le astrazioni di Pig Latin offrono strutture di dati più avanzate rispetto a MapReduce, e svolgono per Hadoop la stessa funzione svolta da SQL per i sistemi RDBMS.

**Per eseguire i processi Pig:**

1.  Aprire la riga di comando di Hadoop.
2.  Cambiare la directory, specificando la cartella C:\\Hadoop\\GettingStarted.
3.  Eseguire il comando seguente per inviare il processo Pig:

        C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    Nella tabella seguente sono illustrati gli elementi di tale comando:

    <table border="1">

    <tr>
    <td>
    File

    </td>
    <td>
    Descrizione

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\pig-0.9.3-SNAPSHOT\\bin\\pig.cmd

    </td>
    <td>
    Script del comando Pig.

    </td>
    </tr>

    <tr>
    <td>
    C:\\Hadoop\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig

    </td>
    <td>
    È possibile sostituire il file di script Pig Latin con uno dei file seguenti:
	<ul>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li>
    <li>C:\\Hadoop\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li>
	</ul>
    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    È possibile sostituire il parametro con un file di dimensioni maggiori:

    <ul>
    <li>/w3c/input/medium/data\_w3c\_medium.txt</li>
    <li>/w3c/input/large/data\_w3c\_large.txt</li>
	</ul>
    </td>
    </tr>
    </table>
    L'output dovrebbe essere simile al seguente:

        (/Info.aspx,1115)
        (/UserService,1130)
        (/Default.aspx,3409)

Poiché gli script Pig vengono compilati in processi MapReduce e potenzialmente in più processi MapReduce, è possibile che gli utenti vedano più processi MapReduce in esecuzione durante l'elaborazione di un processo Pig.

### <a name="rebuild"></a>Ricompilare gli esempi

Gli esempi includono attualmente tutti i file binari necessari, quindi la compilazione non è necessaria. Se si desidera apportare modifiche agli esempi relativi a Java o .NET, è possibile ricompilarli utilizzando msbuild o lo script di PowerShell incluso.

**Per ricompilare gli esempi**

1.  Aprire la riga di comando di Hadoop.
2.  Eseguire il comando seguente:

        powershell -F buildsamples.ps1

## <a name="blobstorage"></a>Connessione all'archivio BLOB di Azure

Azure HDInsight utilizza l'archivio BLOB di Azure come file system predefinito. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][].

È possibile configurare un cluster locale in HDInsight Emulator in modo che utilizzi l'archivio BLOB di Azure invece dell'archiviazione locale. Contenuto della sezione:

-   Effettuare la connessione all'emulatore di archiviazione
-   Connessione all'archivio BLOB di Azure
-   Configurare l'archivio BLOB di Azure come file system predefinito per HDInsight Emulator

### Effettuare la connessione all'emulatore di archiviazione

L'emulatore di archiviazione di Azure è incluso in [Azure SDK per .NET][]. L'emulatore di archiviazione non viene avviato automaticamente. È necessario avviarlo manualmente. Il nome dell'applicazione è *Emulatore di archiviazione di Azure*. Per avviare o interrompere gli emulatori, fare clic con il pulsante destro del mouse sull'icona blu di Azure sulla barra delle applicazioni di Windows, quindi scegliere Show Storage Emulator UI.

> [WACOM.NOTE] Quando si avvia l'emulatore di archiviazione, è possibile che venga visualizzato il messaggio di errore seguente:

>     The process cannot access the file because it is being used by another process.

> Tale errore è dovuto al fatto che anche uno dei servizi Hive di Hadoop utilizza la porta 10000. Per risolvere il problema, eseguire la procedura seguente:

> 1.  Interrompere i due servizi Hive di Hadoop che utilizzano services.msc, ovvero Apache Hadoop Hiveserver e Apache Hadoop Hiveserver2.
> 2.  Avviare l'emulatore di archiviazione BLOB.
> 3.  Riavviare i due servizi Hive di Hadoop.

La sintassi per l'accesso all'emulatore di archiviazione è la seguente:

    wasb://<ContainerName>@storageemulator

Ad esempio:

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] Se viene visualizzato il messaggio di errore seguente:

>     ls: No FileSystem for scheme: wasb

> L'errore è dovuto al fatto che si utilizza ancora la versione Developer Preview. Seguire le istruzioni disponibili nella sezione Installazione di HDInsight Emulator di questo articolo per disinstallare la versione Developer Preview, quindi reinstallare l'applicazione.

### Connessione all'archivio BLOB di Azure

Per istruzioni sulla creazione di un account di archiviazione, vedere [Come creare un account di archiviazione][].

**Per creare un contenitore**

1.  Accedere al [portale di gestione][].
2.  Fare clic su **STORAGE** a sinistra. Verrà visualizzato un elenco di account di archiviazione disponibili nella sottoscrizione.
3.  Selezionare nell'elenco l'account di archiviazione in cui si desidera creare il contenitore.
4.  Fare clic su **CONTAINERS** nella parte superiore della pagina.
5.  Fare clic su **ADD** nella parte inferiore della pagina.
6.  Immettere un valore per **NAME** e selezionare **ACCESS**. È possibile utilizzare uno qualsiasi dei tre livelli di accesso disponibili. Il valore predefinito è **Private**.
7.  Fare clic su **OK** per salvare le modifiche. Il nuovo contenitore sarà incluso nell'elenco nel portale.

Prima di accedere all'account di archiviazione di Azure è necessario aggiungere il nome dell'account e la chiave dell'account al file di configurazione.

**Per configurare la connessione a un account di archiviazione di Azure**

1.  Aprire **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** nel Blocco note.
2.  Aggiungere il tag \<property\> seguente accanto agli altri tag \<property\>:

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    È necessario sostituire \<StorageAccountName\> e \<StorageAccountKey\> con i valori corrispondenti alle informazioni relative all'account di archiviazione in uso.

3.  Salvare la modifica. Sarà necessario riavviare i servizi Hadoop.

Utilizzare la sintassi seguente per accedere all'account di archiviazione:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Ad esempio:

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### Utilizzare un contenitore di archiviazione BLOB di Azure come file system predefinito

È anche possibile utilizzare un contenitore di archiviazione BLOB di Azure come file system predefinito, ad esempio per Azure HDInsight.

**Per configurare il file system predefinito per l'uso di un contenitore di archiviazione BLOB di Azure**

1.  Aprire **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** nel Blocco note.
2.  Individuare il tag \<property\> seguente:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <value>hdfs://localhost:8020</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

3.  Sostituirlo con i due tag \<property\> seguenti:

        <property>
          <name>fs.default.name</name>
          <!-- cluster variant -->
          <!--<value>hdfs://localhost:8020</value>-->
          <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
          <description>The name of the default file system.  Either the literal string "local" or a host:port for NDFS.</description>
          <final>true</final>
        </property>

        <property>
          <name>dfs.namenode.rpc-address</name>
          <value>hdfs://localhost:8020</value>
          <description>A base for other temporary directories.</description>
        </property>

    È necessario sostituire \<StorageAccountName\> e \<StorageAccountKey\> con i valori corrispondenti alle informazioni relative all'account di archiviazione in uso.

4.  Salvare le modifiche.
5.  Aprire la riga di comando Hadoop sul desktop con privilegi elevati (Esegui come amministratore).
6.  Eseguire i comandi seguenti per riavviare i servizi Hadoop:

        C:\Hadoop\stop-onebox.cmd
        C:\Hadoop\start-onebox.cmd

7.  Eseguire il comando seguente per verificare la connessione al file system predefinito:

        hadoop fs -ls /

    I comandi seguenti consentono di elencare i contenuti nella stessa cartella:

        hadoop fs -ls wasb:///
        hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
        hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    Per accedere a HDFS, utilizzare il comando seguente:

        hadoop fs -ls hdfs://localhost:8020/

## <a name="powershell"></a> Esecuzione di HDInsight PowerShell

Alcuni dei cmdlet PowerShell per HDInsight sono supportati in HDInsight Emulator, inclusi i seguenti:

-   Cmdlet di definizione dei processi HDInsight

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

Di seguito viene riportato un esempio per l'invio di un processo Hadoop:

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Quando si chiama Get-Credential, verrà visualizzato un prompt. È necessario utilizzare **hadoop** come nome utente. Per la password è possibile utilizzare qualsiasi stringa. Il nome del cluster è sempre **http://localhost:50111**.

Per altre informazioni sull'invio di processi Hadoop, vedere [Inviare processi Hadoop a livello di codice][]. Per altre informazioni sui cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][].

## <a name="nextsteps"></a>Passaggi successivi

In questa esercitazione è stato installato HDInsight Emulator e sono stati eseguiti alcuni processi Hadoop. Per ulteriori informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight][]
-   [Sviluppare programmi MapReduce Java per HDInsight][]
-   [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight][]
-   [Note sulla versione di HDInsight Emulator][]
-   [Forum MSDN relativo a HDInsight][]

  [Versione di Hadoop inclusa in Azure HDInsight]: ../hdinsight-component-versioning/
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
  [Come installare e configurare Azure PowerShell]: ../install-configure-powershell/
  [Installazione di HDInsight Emulator]: #install
  [Esecuzione dell'esempio relativo al conteggio di parole]: #runwordcount
  [Esecuzione degli esempi introduttivi]: #rungetstartedsamples
  [Connessione all'archivio BLOB di Azure]: #blobstorage
  [Esecuzione di HDInsight PowerShell]: #powershell
  [Passaggi successivi]: #nextsteps
  [pagina di installazione di Microsoft HDInsight Emulator per Azure]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
  [HDI.Emulator.Services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png
  [Note sulla versione di HDInsight Emulator]: ../hdinsight-emulator-release-notes/
  [manuale relativo ai comandi Hadoop]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html
  [Usare l'archivio BLOB di Azure con HDInsight]: ../hdinsight-use-blob-storage/
  [Scenari relativi ai dati di log W3C IIS]: #scenarios
  [Caricare dati di log W3C di esempio]: #loaddata
  [Eseguire processi MapReduce Java]: #javamapreduce
  [Eseguire processi Hive]: #hive
  [Eseguire processi Pig]: #pig
  [Ricompilare gli esempi]: #rebuild
  [Azure SDK per .NET]: http://azure.microsoft.com/en-us/downloads/
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [portale di gestione]: https://manage.windowsazure.com/
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Documentazione di riferimento di cmdlet di HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Forum MSDN relativo a HDInsight]: http://social.msdn.microsoft.com/Forums/en-US/hdinsight
