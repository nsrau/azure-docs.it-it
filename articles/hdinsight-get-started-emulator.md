<properties urlDisplayName="Get Started" pageTitle="Introduzione all'uso di HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Informazioni su come.usare HDInsight Emulator | Azure" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Introduzione all'uso di HDInsight Emulator" author="nitinme" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="nitinme" />

# Introduzione all'uso di HDInsight Emulator

In questa esercitazione sono usate le operazioni iniziali per l'utilizzo di cluster Hadoop in Microsoft HDInsight Emulator per Azure (in precedenza HDInsight Server Developer Preview). HDInsight Emulator include gli stessi componenti dell'ecosistema Hadoop disponibili in Azure HDInsight. Per informazioni dettagliate, incluse informazioni sulle versioni distribuite, vedere [Versione di Hadoop inclusa in Azure HDInsight][Versione di Hadoop inclusa in Azure HDInsight].

> [WACOM.NOTE] HDInsight Emulator include solo un cluster Hadoop, ma non include HBase.

HDInsight Emulator offre un ambiente di sviluppo locale per Azure HDInsight. Se si ha familiarità con Hadoop, sarà possibile iniziare a usare HDInsight Emulator tramite HDFS. In HDInsight, il file system predefinito corrisponde all'archivio BLOB di Azure. Sarà quindi necessario, prima o poi, sviluppare i processi usando tale file system. Per usare l'archivio BLOB di Azure con HDInsight Emulator, è necessario apportare modifiche alla configurazione di HDInsight Emulator.

> [WACOM.NOTE] HDInsight Emulator è in grado di usare solo una distribuzione a nodo singolo.

**Prerequisiti**
 Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

-   HDInsight Emulator richiede una versione a 64 bit di Windows. È quindi necessario disporre di una delle versioni seguenti:

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack1
    -   Windows 8
    -   Windows Server 2012
-   Installare e configurare Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][Come installare e configurare Azure PowerShell].

## Contenuto dell'esercitazione:

-   [Installazione di HDInsight Emulator][Installazione di HDInsight Emulator]
-   [Esecuzione dell'esempio relativo al conteggio di parole][Esecuzione dell'esempio relativo al conteggio di parole]
-   [Esecuzione degli esempi introduttivi][Esecuzione degli esempi introduttivi]
-   [Connessione all'archivio BLOB di Azure][Connessione all'archivio BLOB di Azure]
-   [Esecuzione di HDInsight PowerShell][Esecuzione di HDInsight PowerShell]
-   [Passaggi successivi][Passaggi successivi]

## <a name="install"></a>Installazione di HDInsight Emulator

È possibile installare Microsoft HDInsight Emulator tramite Installazione guidata piattaforma Web Microsoft.

> [WACOM.NOTE] HDInsight Emulator supporta attualmente solo sistemi operativi in lingua inglese. Se una versione precedente dell'emulatore è stata installata in precedenza, sarà innanzitutto necessario disinstallare i due componenti riportati di seguito da Pannello di controllo/Programmi e funzionalità prima di installare la versione più recente dell'emulatore.
>
> -   Microsoft HDInsight Emulator per Microsoft Azure o Microsoft HDInsight Developer Preview, indipendentemente da quale sia installato.
> -   Hortonworks Data Platform
>
> </p>

**Per installare HDInsight Emulator**

1.  Aprire Internet Explorer, quindi passare alla [pagina di installazione di Microsoft HDInsight Emulator per Azure][pagina di installazione di Microsoft HDInsight Emulator per Azure].
2.  Fare clic su **Installa ora**.
3.  Fare clic su **Esegui** quando richiesto per l'installazione di HDINSIGHT.exe nella parte inferiore della pagina.
4.  Per completare l'installazione, fare clic sul pulsante **Sì** nella finestra **Controllo dell'account utente** visualizzata. Verrà visualizzata la finestra di dialogo Installazione guidata piattaforma Web.
5.  Fare clic su **Installa** nella parte inferiore della pagina.
6.  Fare clic su **Accetto** per accettare i termini del Contratto di licenza.
7.  Verificare che sia visualizzato lo stato **Installazione riuscita dei seguenti prodotti**, quindi fare clic su **Fine**.
8.  Fare clic su **Esci** per chiudere la finestra di Installazione guidata piattaforma Web.

**Per verificare l'installazione di HDInsight Emulator**

Al termine dell'installazione dovrebbero essere visibili tre icone sul desktop, con i collegamenti seguenti:

-   **Hadoop Command Line**: prompt dei comandi di Hadoop, da cui vengono eseguiti i processi MapReduce, Pig e Hive in HDInsight Emulator.

-   **Hadoop Name Node Status**: NameNode gestisce una directory basata su albero per tutti i file in HDFS e tiene traccia delle posizioni di archiviazione dei dati per tutti i file in un cluster Hadoop. I client comunicano con NameNode per individuare le posizioni di archiviazione dei nodi dati per tutti i file.

-   **Stato di Hadoop Yarn**: funzionalità di analisi dei processi che alloca le attività MapReduce ai nodi di un cluster.

Durante l'installazione vengono installati anche alcuni servizi locali. Nella figura seguente viene mostrata la finestra Services:

![HDI.Emulator.Services][HDI.Emulator.Services]

I servizi relativi a HDInsight Emulator non vengono avviati per impostazione predefinita. Per avviare i servizi, dalla riga di comando Hadoop, eseguire **start\_local\_hdp\_services.cmd** sotto <system drive\>\\hdp. Per avviare automaticamente i servizi dopo aver riavviato il computer, eseguire **set-onebox-autostart.cmd**.

Per informazioni sui problemi noti relativi all'installazione e all'esecuzione di HDInsight Server, vedere le [Note sulla versione di HDInsight Emulator][Note sulla versione di HDInsight Emulator]. Il file di log dell'installazione è disponibile in **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

## <a name="runwordcount"></a>Esecuzione di un processo MapReduce relativo al conteggio di parole

A questo punto, dopo aver configurato HDInsight Emulator nella workstation, è possibile eseguire un processo MapReduce per verificare l'installazione. Caricare innanzitutto alcuni file di dati in HDFS, quindi eseguire un processo MapReduce per il conteggio delle parole, in modo da contare la frequenza delle parole specifiche in tali file.

Il programma MapReduce per il conteggio delle parole è stato incluso nel pacchetto *hadoop-mapreduce-examples-2.4.0.SNAPSHOT.jar*. Il file con estensione jar si trova nella cartella *C:\\hdp\\hadoop-2.4.0.SNAPSHOT\\share\\hadoop\\mapreduce*.

Il processo MapReduce per il conteggio delle parole accetta due argomenti:

-   Una cartella di input. Come cartella di input verrà usata *hdfs://localhost/user/HDIUser*.
-   Una cartella di output. Come cartella di output verrà usata *hdfs://localhost/user/HDIUser/WordCount\_Output*. La cartella di output non può essere una cartella esistente. In caso contrario, il processo MapReduce avrà esito negativo. Se si desidera eseguire il processo MapReduce una seconda volta, sarà necessario specificare una cartella di output diversa o eliminare la cartella di output esistente.

**Per eseguire il processo MapReduce relativo al conteggio di parole**

1.  Dal desktop fare doppio clic su **Hadoop Command Line** per aprire la finestra della riga di comando di Hadoop. La cartella corrente dovrebbe essere la seguente:

        c:\hdp\hadoop-2.4.0.SNAPSHOT

    In caso contrario, eseguire il comando seguente:

        cd %hadoop_home%

2.  Eseguire i comandi seguenti di Hadoop per creare una cartella HDFS per l'archiviazione dei file di input e di output:

        hadoop fs -mkdir /user
        hadoop fs -mkdir /user/HDIUser

3.  Eseguire il comando seguente di Hadoop per copiare alcuni file di testo locali in HDFS:

        hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.SNAPSHOT\share\doc\hadoop\common\*.txt /user/HDIUser

4.  Eseguire il comando seguente per elencare i file nella cartella /user/HDIUser:

        hadoop fs -ls /user/HDIUser

    Dovrebbero essere visualizzati i file seguenti:

        C:\hdp\hadoop-2.4.0.SNAPSHOT>hadoop fs -ls /user/HDIUser
        Found 4 items
        -rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
        -rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
        -rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
        -rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5.  Eseguire il comando seguente per eseguire il processo MapReduce relativo al conteggio di parole:

        C:\hdp\hadoop-2.4.0.SNAPSHOT> hadoop jar C:\hdp\hadoop-2.4.0.SNAPSHOT\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.SNAPSHOT.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Eseguire il comando seguente per elencare il numero di parole contenenti "windows" dal file di output:

        hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    L'output dovrebbe essere analogo al seguente:

        C:\hdp\hadoop-2.4.0.SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
        windows 4
        windows.        2
        windows/cygwin. 1

Per altre informazioni sui comandi Hadoop, vedere il [manuale relativo ai comandi Hadoop][manuale relativo ai comandi Hadoop].

## <a name="rungetstartedsamples"></a> Esecuzione degli esempi introduttivi

L'installazione di HDInsight Emulator rende disponibili alcuni esempi per consentire gli utenti di apprendere il funzionamento dei servizi basati su Apache Hadoop su Windows. Tali esempi illustrano alcune attività che risultano in genere necessarie per l'elaborazione di un set di Big Data. È consigliabile esaminare gli esempi, in modo da acquisire familiarità con i concetti associati al modello di programmazione di MapReduce e al relativo ecosistema.

Gli esempi sono organizzati in base agli scenari di elaborazione dei dati di log W3C IIS. È disponibile uno strumento di generazione di dati, che consente di creare e importare set di dati di varie dimensioni in HDFS o nell'archivio BLOB di Azure. Per altre informazioni, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight]. È possibile eseguire i processi MapReduce, Pig o Hive sulle pagine di dati generati dallo script di PowerShell. Notare che gli script Pig e Hive forniscono un livello di astrazione superiore a MapReduce e verranno infine compilati nei programmi MapReduce. Gli utenti possono eseguire alcuni processi per osservare gli effetti derivanti dall'utilizzo delle diverse tecnologie e l'impatto della dimensione dei dati sull'esecuzione delle attività di elaborazione.

### Contenuto della sezione

-   [Scenari relativi ai dati di log W3C IIS][Scenari relativi ai dati di log W3C IIS]
-   [Caricare dati di log W3C di esempio][Caricare dati di log W3C di esempio]
-   [Eseguire processi MapReduce Java][Eseguire processi MapReduce Java]
-   [Eseguire processi Hive][Eseguire processi Hive]
-   [Eseguire processi Pig][Eseguire processi Pig]
-   [Ricompilare gli esempi][Ricompilare gli esempi]

### <a name="scenarios"></a>Scenari relativi ai dati di log W3C IIS

Lo scenario W3C consente di generare e importare dati di log W3C IIS in HDFS o nell'archivio BLOB di Azure in tre dimensioni diverse, 1 MB (piccola), 500 MB (media) e 2 GB (grande). Include tre tipi di processo e consente di implementarli in C#, Java, Pig e Hive.

-   **totalhits**: consente di calcolare il numero totale di richieste per una pagina specifica.
-   **avgtime**: consente di calcolare il tempo medio necessario (in secondi) per una richiesta per ogni pagina.
-   **errors**: consente di calcolare il numero di errori per pagina, per ora o per le richieste con stato 404 o 500.

Questi esempi e la relativa documentazione non consentono di ottenere una conoscenza approfondita o un'implementazione completa delle tecnologie chiave di Hadoop. Il cluster usato include solo un singolo nodo. Non è pertanto possibile osservare, in questa versione, l'effetto dell'aggiunta di più nodi.

### <a name="loaddata"></a>Caricare dati di log W3C di esempio

La generazione e l'importazione dei dati in HDFS vengono eseguite tramite lo script importdata.ps1 di PowerShell.

**Per caricare dati di log W3C di esempio:**

1.  Aprire la riga di comando di Hadoop dal desktop.
2.  Passare alla directory **C:\\hdp\\GettingStarted**.
3.  Per generare e importare i dati in HDFS, eseguire il comando seguente:

        powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Se si desidera invece caricare i dati nell'archivio BLOB di Azure, vedere [Connessione all'archivio BLOB di Azure][Connessione all'archivio BLOB di Azure].

4.  Eseguire il comando seguente dalla riga di comando di Hadoop per elencare i file importati in HDFS:

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

5.  Per verificare il contenuto del file, eseguire il comando seguente per visualizzare uno dei file di dati nella finestra della console:

        hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Il file di dati è stato creato e importato in HDFS. È possibile iniziare a eseguire diversi processi Hadoop.

### <a name="javamapreduce"></a> Eseguire processi MapReduce Java

MapReduce è il motore di calcolo di base per Hadoop. Per impostazione predefinita, viene implementato in Java, ma sono disponibili anche esempi che si avvalgono di .NET ed esempi di streaming Hadoop in C#. La sintassi per l'esecuzione di un processo MapReduce è la seguente:

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Il file con estensione jar e i file di origine si trovano nella cartella C:\\Hadoop\\GettingStarted\\Java.

**Per eseguire un processo MapReduce per il calcolo degli accessi alle pagine Web**

1.  Aprire la riga di comando di Hadoop.
2.  Passare alla directory **C:\\hdp\\GettingStarted**.
3.  Eseguire il comando seguente per rimuovere la directory di output, nel caso in cui esista già. Se tale cartella esiste già, il processo MapReduce avrà esito negativo.

        hadoop fs -rm -r /w3c/output

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
    Il file con estensione jar si trova nella cartella C:\\hdp\\GettingStarted\\Java.

    </td>
    </tr>

    <tr>
    <td>
    microsoft.hadoop.w3c.TotalHitsForPage

    </td>
    <td>
    È possibile sostituire il tipo con uno dei valori seguenti:

    -   microsoft.hadoop.w3c.AverageTimeTaken
    -   microsoft.hadoop.w3c.ErrorsByPage

    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    È possibile sostituire il file di input con uno dei valori seguenti:

    -   /w3c/input/medium/data\_w3c\_medium.txt
    -   /w3c/input/large/data\_w3c\_large.txt

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
        /Default.aspx   3360
        /Info.aspx      1156
        /UserService    1137

    Vengono effettuati 3360 accessi alla pagina Default.aspx e così via. Provare a eseguire di nuovo i comandi sostituendo i valori come suggerito nella tabella precedente e osservare in che modo l'output cambia in base al tipo di processo e alle dimensioni dei dati.

### <a name="hive"></a>Eseguire processi Hive

Il motore query Hive potrebbe risultare familiare ai business analyst con competenze relative a SQL, poiché offre un'interfaccia analoga a quella di SQL e un modello di dati relazionale per HDFS. Hive usa un linguaggio denominato HiveQL, che è molto simile a SQL. Hive fornisce un livello di astrazione superiore al framework MapReduce basato su Java e le query Hive vengono compilate in MapReduce al runtime.

**Per eseguire un processo Hive**

1.  Aprire la riga di comando di Hadoop.
2.  Passare alla directory **C:\\hdp\\GettingStarted**.
3.  Eseguire il comando seguente per rimuovere la cartella **/w3c/hive/input**, nel caso in cui esista già. Se tale cartella esiste già, il processo Hive avrà esito negativo.

        hadoop fs -rmr /w3c/hive/input

4.  Eseguire il comando seguente per creare le cartelle **/w3c/hive/input** e quindi copiare i file di dati nella cartella /hive/input:

        hadoop fs -mkdir /w3c/hive
        hadoop fs -mkdir /w3c/hive/input

        hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Eseguire il comando seguente per eseguire il file di script **w3ccreate.hql**. Lo script crea una tabella di Hive e carica i dati in tale tabella:

        C:\hdp\hive-0.13.0.SNAPSHOT\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    L'output dovrebbe essere simile al seguente:

        Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.SNAPSHOT    /conf/hive-log4j.properties
        OK
        Time taken: 1.137 seconds
        OK
        Time taken: 4.403 seconds
        Loading data to table default.w3c
        Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
        Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
        OK
        Time taken: 2.881 seconds

6.  Eseguire il comando seguente per eseguire il file di script **w3ctotalhitsbypage.hql** di HiveQL.

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

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
    C:\\hdp\\hive-0.13.0.SNAPSHOT\\bin\\hive.cmd

    </td>
    <td>
    Script del comando Hive.

    </td>
    </tr>

    <tr>
    <td>
    C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql

    </td>
    <td>
    È possibile sostituire il file di script Hive con uno dei file seguenti:

    -   C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql
    -   C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql

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

        MapReduce Total cumulative CPU time: 5 seconds 391 msec
        Ended Job = job_1410201800143_0008
        MapReduce Jobs Launched:
        Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
        Total MapReduce CPU Time Spent: 5 seconds 391 msec
        OK
        /Default.aspx   3360
        /Info.aspx      1156
        /UserService    1137
        Time taken: 49.304 seconds, Fetched: 3 row(s)

Si noti che il primo passaggio in ogni processo consiste nella creazione di una tabella e nel caricamento di dati nella tabella dal file creato in precedenza. È possibile passare al file creato cercando sotto il nodo /Hive in HDFS mediante il comando seguente:

    hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Eseguire processi Pig

I processi Pig usano un linguaggio di flusso di dati denominato *Pig Latin*. Le astrazioni di Pig Latin offrono strutture di dati più avanzate rispetto a MapReduce, e svolgono per Hadoop la stessa funzione svolta da SQL per i sistemi RDBMS.

**Per eseguire i processi Pig:**

1.  Aprire la riga di comando di Hadoop.
2.  Cambiare la directory, specificando la cartella **C:\\hdp\\GettingStarted**.
3.  Eseguire il comando seguente per inviare il processo Pig:

        C:\hdp\pig-0.12.1.SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

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
    C:\\hdp\\pig-0.12.1.SNAPSHOT\\bin\\pig.cmd

    </td>
    <td>
    Script del comando Pig.

    </td>
    </tr>

    <tr>
    <td>
    C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.pig

    </td>
    <td>
    È possibile sostituire il file di script Pig Latin con uno dei file seguenti:

    -   C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig
    -   C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig

    </td>
    </tr>

    <tr>
    <td>
    /w3c/input/small/data\_w3c\_small.txt

    </td>
    <td>
    È possibile sostituire il parametro con un file di dimensioni maggiori:

    </p>
    -   /w3c/input/medium/data\_w3c\_medium.txt
    -   /w3c/input/large/data\_w3c\_large.txt

    </td>
    </tr>
    </table>
    L'output dovrebbe essere simile al seguente:

        (/Info.aspx,1156)
        (/UserService,1137)
        (/Default.aspx,3360)

Poiché gli script Pig vengono compilati in processi MapReduce e potenzialmente in più processi MapReduce, è possibile che siano visibili più processi MapReduce in esecuzione durante l'elaborazione di un processo Pig.

### <a name="rebuild"></a>Ricompilare gli esempi

Gli esempi includono attualmente tutti i file binari necessari, quindi la compilazione non è necessaria. Se si desidera apportare modifiche agli esempi relativi a Java o .NET, è possibile ricompilarli usando msbuild o lo script di PowerShell incluso.

**Per ricompilare gli esempi**

1.  Aprire la riga di comando di Hadoop.
2.  Eseguire il comando seguente:

        powershell -F buildsamples.ps1

## <a name="blobstorage"></a>Connessione all'archivio BLOB di Azure

HDInsight Emulator usa HDFS come file system predefinito. Tuttavia, Azure HDInsight usa l'archivio BLOB di Azure come file system predefinito. È possibile configurare HDInsight Emulator in modo che usi l'archivio BLOB di Azure invece dell'archiviazione locale. Seguire le istruzioni riportate di seguito per creare un contenitore di archiviazione in Azure e connetterlo a HDInsight Emulator.

> [WACOM.NOTE] Per altre informazioni sul modo in cui HDInsight usa l'archivio BLOB di Azure, vedere [Usare l'archivio BLOB di Azure con HDInsight][Usare l'archivio BLOB di Azure con HDInsight].

Prima di iniziare con le istruzioni riportate di seguito è necessario aver creato un account di archiviazione. Per istruzioni, vedere [Come creare un account di archiviazione][Come creare un account di archiviazione].

**Per creare un contenitore**

1.  Accedere al [portale di gestione][portale di gestione].
2.  Fare clic su **STORAGE** a sinistra. Verrà visualizzato un elenco di account di archiviazione disponibili nella sottoscrizione.
3.  Selezionare nell'elenco l'account di archiviazione in cui si desidera creare il contenitore.
4.  Fare clic su **CONTAINERS** nella parte superiore della pagina.
5.  Fare clic su **ADD** nella parte inferiore della pagina.
6.  Immettere un valore per **NAME** e selezionare **ACCESS**. È possibile usare uno qualsiasi dei tre livelli di accesso disponibili. Il valore predefinito è **Private**.
7.  Fare clic su **OK** per salvare le modifiche. Il nuovo contenitore sarà incluso nell'elenco nel portale.

Prima di accedere all'account di archiviazione di Azure è necessario aggiungere il nome dell'account e la chiave dell'account al file di configurazione.

**Per configurare la connessione a un account di archiviazione di Azure**

1.  Aprire **C:\\hdp\\hadoop-2.4.0.SNAPSHOT\\etc\\hadoop\\core-site.xml** in Notepad.
2.  Aggiungere il seguente <property\> tag accanto agli altri <property\> tag:

        <property>
            <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
            <value><StorageAccountKey></value>
        </property>

    È necessario sostituire <storageaccountname\> e <storageaccountkey\> con i valori corrispondenti alle informazioni relative all'account di archiviazione in uso.

3.  Salvare la modifica. Sarà necessario riavviare i servizi Hadoop.

Usare la sintassi seguente per accedere all'account di archiviazione:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Ad esempio:

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

## <a name="powershell"></a> Esecuzione di HDInsight PowerShell

Alcuni dei cmdlet di Azure HDInsight PowerShell sono supportati anche in HDInsight Emulator, inclusi i seguenti:

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

Quando si chiama Get-Credential, verrà visualizzato un prompt. È necessario usare **hadoop** come nome utente. Per la password è possibile usare qualsiasi stringa. Il nome del cluster è sempre **http://localhost:50111**.

Per altre informazioni sull'invio di processi Hadoop, vedere [Inviare processi Hadoop a livello di codice][Inviare processi Hadoop a livello di codice]. Per altre informazioni sui cmdlet PowerShell per HDInsight, vedere [Documentazione di riferimento di cmdlet di HDInsight][Documentazione di riferimento di cmdlet di HDInsight].

## <a name="nextsteps"></a>Passaggi successivi

In questa esercitazione è stato installato HDInsight Emulator e sono stati eseguiti alcuni processi Hadoop. Per altre informazioni, vedere gli articoli seguenti:

-   [Introduzione all'utilizzo di Azure HDInsight][Introduzione all'utilizzo di Azure HDInsight]
-   [Sviluppare programmi MapReduce Java per HDInsight][Sviluppare programmi MapReduce Java per HDInsight]
-   [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight][Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight]
-   [Note sulla versione di HDInsight Emulator][Note sulla versione di HDInsight Emulator]
-   [Forum MSDN relativo a HDInsight][Forum MSDN relativo a HDInsight]

  [Versione di Hadoop inclusa in Azure HDInsight]: ../hdinsight-component-versioning/
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
  [Come creare un account di archiviazione]: ../storage-create-storage-account/
  [portale di gestione]: https://manage.windowsazure.com/
  [Inviare processi Hadoop a livello di codice]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Documentazione di riferimento di cmdlet di HDInsight]: http://msdn.microsoft.com/it-it/library/windowsazure/dn479228.aspx
  [Introduzione all'utilizzo di Azure HDInsight]: ../hdinsight-get-started/
  [Sviluppare programmi MapReduce Java per HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Sviluppare programmi MapReduce di streaming Hadoop in C# per HDInsight]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
  [Forum MSDN relativo a HDInsight]: http://social.msdn.microsoft.com/Forums/en-US/hdinsight
