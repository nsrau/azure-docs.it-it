<properties
	pageTitle="Usare Python con Hive e Pig in HDInsight | Microsoft Azure"
	description="Informazioni su come usare le funzioni definite dall'utente di Python da Hive e Pig in HDInsight, lo stack di tecnologie Hadoop in Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>  

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="09/07/2016" 
	ms.author="larryfr"/>  

#Usare Python con Hive e Pig in HDInsight

Hive e Pig sono soluzioni ottimali per usare i dati in HDInsight, ma in alcuni casi è necessario un linguaggio più generico. Sia Hive che Pig consentono di creare funzioni definite dall'utente usando una vasta gamma di linguaggi di programmazione. In questo articolo verrà illustrato come usare una funzione definita dall'utente Python da Hive e Pig.

##Requisiti

* Un cluster HDInsight (Windows o basato su Linux)

* Un editor di testo

    > [AZURE.IMPORTANT] Se si usa un server HDInsight basato su Linux e i file Python vengono creati in un client Windows, è necessario usare un editor che usa LF come terminazione di riga. Se non si è certi se l'editor usa LF o CRLF, vedere la sezione [Risoluzione dei problemi](#troubleshooting), che include passaggi per la rimozione del carattere CR mediante le utilità nel cluster HDInsight.
    
##<a name="python"></a>Python in HDInsight

Python2.7 viene installato per impostazione predefinita nei cluster HDInsight 3.0 e versioni successive. Hive può essere usato con questa versione di Python per l'elaborazione dei flussi, in cui i dati vengono passati tra Hive e Python con STDOUT/STDIN.

HDInsight include anche Jython, un'implementazione di Python scritta in Java. Dal momento che Pig riconosce Jython senza dover ricorrere ai flussi, è preferibile scegliere Jython quando si usa Pig. Tuttavia, è possibile usare Python normale (C Python) anche con Pig.

##<a name="hivepython"></a>Hive e Python

Python può essere usato come funzione definita dall'utente da Hive tramite l'istruzione **TRANSFORM** di HiveQL. Ad esempio, il codice HiveQL seguente richiama uno script di Python archiviato nel file **streaming.py**.

**HDInsight basato su Linux**

	add file wasbs:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'python streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

**HDInsight basato su Windows**

	add file wasbs:///streaming.py;

	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

> [AZURE.NOTE] Nei cluster HDInsight basati su Windows, la clausola **USING** deve specificare il percorso completo di python.exe. Questo valore è sempre `D:\Python27\python.exe`.

Ecco cosa fa l'esempio:

1. L'istruzione **add file** all'inizio del file aggiunge il file **streaming.py** alla cache distribuita ed è quindi accessibile a tutti i nodi del cluster.

2. L'istruzione **SELECT TRANSFORM ... USING** seleziona i dati di **hivesampletable** e passa clientid, devicemake e devicemodel allo script **streaming.py**.

3. La clausola **AS** descrive i campi restituiti da **streaming.py**

<a name="streamingpy"></a> Questo è il file **streaming.py** usato nell'esempio di HiveQL.

	#!/usr/bin/env python

	import sys
	import string
	import hashlib

	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break

	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Dal momento che vengono usati i flussi, lo script deve eseguire le operazioni seguenti:

1. Leggere i dati da STDIN. A tale scopo, in questo esempio viene usato `sys.stdin.readline()`.

2. Per rimuovere il carattere di nuova riga finale, viene usato `string.strip(line, "\n ")`, dal momento che si è interessati solo ai dati di testo e non all'indicatore di fine riga.

2. Durante l'elaborazione di flussi tutti i valori sono contenuti in un'unica riga sono separati da un carattere di tabulazione. Si può quindi usare `string.split(line, "\t")` per dividere l'input in corrispondenza di ogni tabulazione, in modo da restituire solo i campi.

3. Al termine dell'elaborazione, l'output deve essere scritto in STDOUT in un'unica riga, con i campi separati da tabulazioni. A tale scopo viene usato `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Tutte queste operazioni vengono eseguite in un ciclo `while`, che viene ripetuto finché non sono più presenti elementi `line` da leggere. A questo punto, viene eseguito `break` per terminare il ciclo e di conseguenza lo script.

Oltre a quanto descritto, lo script concatena i valori di input per `devicemake` e `devicemodel` e calcola un hash del valore concatenato. In breve, il funzionamento di un qualsiasi script di Python richiamato da Hive è il seguente: viene riprodotto a ciclo continuo, legge l'input fino alla fine, interrompe ogni riga di input in corrispondenza delle tabulazioni, viene elaborato, scrive una singola riga di output delimitato da tabulazioni.

Per informazioni su come eseguire questo esempio nel cluster HDInsight, vedere [Esecuzione degli esempi](#running).

##<a name="pigpython"></a>Pig e Python

Si può usare uno script di Python come funzione definita dall'utente da Pig tramite l'istruzione **GENERATE**. Esistono due modi per farlo: usare Jython (Python implementato in Java Virtual Machine) e C Python (Python normale).

La differenza principale è che Jython viene eseguito su JVM e può essere chiamato in modo nativo da Pig (anch'esso in esecuzione su JVM). C Python è una procedura (scritta su C). Pertanto, i dati da Pig a JVM vengono inviati allo script in esecuzione in un processo di Python, quindi l'output viene inviato in Pig.

Per determinare se Pig usa Jython o C Python per eseguire lo script, usare __register__ quando si fa riferimento allo script Python da Pig Latin. In questo modo viene comunicato a Pig quale interprete usare e quale alias creare per lo script. Negli esempi seguenti gli script vengono registrati con Pig come __myfuncs__:

* __Per usare Jython__: `register '/path/to/pig_python.py' using jython as myfuncs;`
* __Per usare C Python__: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [AZURE.IMPORTANT] Quando si usa Jython, il percorso al file pig\_jython può essere un percorso locale o un percorso WASB://. Tuttavia, quando si usa C Python, è necessario fare riferimento un file nel file system locale del nodo che si usa per inviare il processo Pig.

Una volta trascorsa la registrazione, Pig Latin per questo esempio è lo stesso per entrambi:

    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Ecco cosa fa l'esempio:

1. La prima riga carica il file di dati di esempio, **sample.log** in **LOG**. Dal momento che lo schema del file di log non è coerente, definisce inoltre ogni record (in questo caso **LINE**) come **chararray**, che è essenzialmente una stringa.

2. La riga successiva esclude tutti i valori Null, archiviando il risultato dell'operazione in **LOG**.

3. Esegue quindi l'iterazione sui record in **LOG** e usa **GENERATE** per richiamare il metodo **create\_structure** contenuto nello script Python/Jython caricato come **myfuncs**. **LINE** viene usato per passare il record corrente alla funzione.

4. Viene infine eseguito il dump degli output in STDOUT con il comando **DUMP**. In questo modo i risultati verranno subito visualizzati al termine dell'operazione; in uno script effettivo in genere viene usato **STORE** per archiviare i dati in un nuovo file.

Il file di script Python effettivo è inoltre simile tra Python C e Jython; l'unica vera differenza è che è necessario importare da __pig\_util__ quando si usa C Python. Lo script __pig\_python.py__ è il seguente:

<a name="streamingpy"></a>

    # Uncomment the following if using C Python
    #from pig_util import outputSchema

    @outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
    def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail

> [AZURE.NOTE] Non è necessario installare 'pig\_util', perché è disponibile automaticamente per lo script.

In precedenza l'input di **LINE** è stato definito come chararray perché non conteneva uno schema coerente. Con lo script Python i dati vengono trasformati in uno schema coerente per l'output, come descritto di seguito:

1. L'istruzione **@outputSchema** definisce il formato dei dati che verranno restituiti a Pig. In questo caso si tratta di un **contenitore di dati**, ovvero un tipo di dati Pig. Il contenitore include i seguenti campi, che sono tutti chararray (stringhe):

	* date - data di creazione della voce del log
	* time - ora di creazione della voce del log
	* classname - nome della classe per cui è stata creata la voce
	* level - livello del log
	* detail - dettagli relativi alla voce di log

2. In seguito, **def create\_structure(input)** definisce la funzione a cui Pig passerà le voci.

3. I dati di esempio in **sample.log** sono per lo più conformi allo schema date, time, classname, level e detail che si intende restituire. Contengono però anche alcune righe che iniziano con la stringa '*java.lang.Exception*' e che è necessario modificare per adattarle allo schema. L'istruzione **if** verifica la presenta di queste righe, quindi forza i dati di input a spostare la stringa '*java.lang.Exception*' alla fine, portando i dati in linea con lo schema di output previsto.

4. Viene quindi usato il comando **split** per dividere i dati in corrispondenza dei primi quattro spazi. Si otterranno in tal modo cinque valori, che vengono assegnati a **date**, **time**, **classname**, **level** e **detail**.

5. I valori vengono infine restituiti a Pig.

Quando i dati vengono restituiti a Pig, lo schema sarà coerente, come definito nell'istruzione **@outputSchema**.

##<a name="running"></a>Esecuzione degli esempi

Se si usa un cluster HDInsight basato su Linux, eseguire la procedura con **SSH** riportata di seguito. Se si usa un cluster HDInsight basato su Windows e un client Windows, eseguire la procedura con **PowerShell**.

###SSH

Per altre informazioni sull'uso di SSH, vedere <a href="../hdinsight-hadoop-linux-use-ssh-unix/" target="_blank">Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix oppure OS X</a> o <a href="../hdinsight-hadoop-linux-use-ssh-windows/" target="_blank">Usare SSH con Hadoop basato su Linux in HDInsight da Windows</a>.

1. Usando gli esempi di Python [streaming.py](#streamingpy) e [pig\_python.py](#jythonpy), creare copie locali dei file nel computer di sviluppo.

2. Usare `scp` per copiare i file nel cluster HDInsight. Ad esempio, il comando seguente copia i file in un cluster denominato **mycluster**.

		scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. Usare SSH per connettersi al cluster. Ad esempio, il comando seguente stabilisce la connessione a un cluster denominato **mycluster** come utente **myuser**.

		ssh myuser@mycluster-ssh.azurehdinsight.net

4. Dalla sessione SSH, aggiungere i file python caricati in precedenza nell'archivio WASB per il cluster.

		hdfs dfs -put streaming.py /streaming.py
		hdfs dfs -put pig_python.py /pig_python.py

Dopo il caricamento dei file, usare la procedura seguente per eseguire i processi Hive e Pig.

####Hive

1. Usare il comando `hive` per avviare la shell di Hive. Una volta caricata la shell, verrà visualizzato un prompt `hive>`.

2. Al prompt `hive>` immettere il codice seguente.

		add file wasbs:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'python streaming.py' AS
		  (clientid string, phoneLabel string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Dopo l'immissione dell'ultima riga il processo dovrebbe essere avviato. Restituirà infine un output analogo al seguente.

		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100041	RIM 9650	d476f3687700442549a83fac4560c51c
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
		100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig

1. Usare il comando `pig` per avviare la shell. Una volta caricata la shell, verrà visualizzato un prompt `grunt>`.

2. Immettere le istruzioni seguenti come prompt `grunt>` per eseguire lo script Python usando l'interprete Jython.

		Register wasbs:///pig_python.py using jython as myfuncs;
	    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

3. Dopo l'immissione della riga seguente, il processo dovrebbe essere avviato. Restituirà infine un output analogo al seguente.

		((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
		((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
		((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
		((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
		((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Usare `quit` per chiudere la shell Grunt e quindi il prompt seguente per modificare il file pig\_python.py nel file system locale:

    nano pig\_python.py

5. Una volta nell'editor, rimuovere i simboli di commenti dalla riga seguente rimuovendo il carattere `#` dall'inizio della riga:

        #from pig_util import outputSchema

    Una volta apportata la modifica, usare Ctrl + X per uscire dall'editor. Selezionare Y e quindi INVIO per salvare le modifiche.

6. Usare il comando `pig` per avviare di nuovo la shell. Una volta al prompt `grunt>` usare la riga seguente per eseguire il script di Python con l'interprete C Python.

        Register 'pig_python.py' using streaming_python as myfuncs;
	    LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
	    LOG = FILTER LOGS by LINE is not null;
	    DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
	    DUMP DETAILS;

    Una volta completato questo processo, si dovrebbe vedere lo stesso risultato di quando è stato eseguito lo script in precedenza con Jython.

###PowerShell

Nella procedura seguente viene usato Azure PowerShell. Se questo non è già installato e configurato nel computer di sviluppo, prima di iniziare vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

1. Usando gli esempi di Python [streaming.py](#streamingpy) e [pig\_python.py](#jythonpy), creare copie locali dei file nel computer di sviluppo.

2. Usare lo script di PowerShell seguente per caricare i file **streaming.py** e **pig\_python.py** nel server. Sostituire il nome del cluster Azure HDInsight e il percorso per i file **streaming.py** e **pig\_python.py** nelle prime tre righe dello script.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\pig_python.py"

		$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

		#Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
        
        Set-AzureStorageBlobContent `
            -File $pathToStreamingFile `
            -Blob "streaming.py" `
            -Container $container `
            -Context $context
		
        Set-AzureStorageBlobContent `
            -File $pathToJythonFile `
            -Blob "pig_python.py" `
            -Container $container `
            -Context $context

	Questo script recupera le informazioni relative al cluster HDInsight, quindi estrae l'account e la chiave dell'account di archiviazione predefinito e carica i file nella radice del contenitore.

	> [AZURE.NOTE] Altri metodi per caricare gli script sono descritti nel documento [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md).

Dopo aver caricato i file, usare i seguenti script di PowerShell per avviare i processi. Al termine del processo, l'output dovrebbe essere scritto nella console di PowerShell.

####Hive

Lo script seguente eseguirà lo script __streaming.py__. Prima dell'esecuzione, verranno richieste le informazioni sull'account HTTPs/Admin per il cluster HDInsight.

    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName
    $creds=Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
    
    # If using a Windows-based HDInsight cluster, change the USING statement to:
    # "USING 'D:\Python27\python.exe streaming.py' AS " +
	$HiveQuery = "add file wasbs:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'python streaming.py' AS " +
	               "(clientid string, phoneLabel string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"

	$jobDefinition = New-AzureRmHDInsightHiveJobDefinition `
        -Query $HiveQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
    #   -Clustername $clusterName `
    #   -JobId $job.JobId `
    #   -DefaultContainer $container `
    #   -DefaultStorageAccountName $storageAccountName `
    #   -DefaultStorageAccountKey $storageAccountKey `
    #   -HttpCredential $creds `
    #   -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

L'output del processo **Hive** sarà simile al seguente:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

####Pig (Jython)

Nel seguente caso si userà lo script __pig\_python.py__ tramite l'interprete Jython. Prima dell'esecuzione, verranno richieste le informazioni su HTTPs/Admin per il cluster HDInsight.

> [AZURE.NOTE] Durante l'invio remoto di un processo con PowerShell, non è possibile usare C Python come interprete.

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

    $creds = Get-Credential
    #Get the cluster info so we can get the resource group, storage, etc.
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroup = $clusterInfo.ResourceGroup
    $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
    $container=$clusterInfo.DefaultStorageContainer
    $storageAccountKey=(Get-AzureRmStorageAccountKey `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
    
    #Create a storage content and upload the file
    $context = New-AzureStorageContext `
        -StorageAccountName $storageAccountName `
        -StorageAccountKey $storageAccountKey
            
	$PigQuery = "Register wasbs:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"

	$jobDefinition = New-AzureRmHDInsightPigJobDefinition -Query $PigQuery

	$job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds
        
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureRmHDInsightJob `
        -Job $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    # Uncomment the following to see stderr output
    # Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -DefaultContainer $container `
        -DefaultStorageAccountName $storageAccountName `
        -DefaultStorageAccountKey $storageAccountKey `
        -HttpCredential $creds

L'output del processo **Pig** sarà simile al seguente:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Risoluzione dei problemi

###Errori durante l'esecuzione di processi

Quando si esegue il processo hive, è possibile riscontrare un errore simile al seguente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
    
Questo problema potrebbe essere causato dalle terminazioni di riga nel file streaming.py. Molti editor di Windows usano per impostazione predefinita CRLF come terminazione di riga, mentre le applicazioni Linux prevedono in genere LF.

Se si usa un editor che non è in grado di creare terminazioni di riga LF o non si conoscono le terminazioni di riga usate, usare le seguenti istruzioni PowerShell per rimuovere i caratteri CR prima di caricare il file in HDInsight:

    $original_file ='c:\path\to\streaming.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)

###Script di PowerShell

Entrambi gli script di PowerShell di esempio usati per eseguire gli esempi contengono una riga impostata come commento che visualizzerà l'output degli errori relativi al processo. Se l'output previsto per il processo non viene visualizzato, rimuovere i simboli di commento dalla riga seguente e verificare se le informazioni di errore indicano un problema.

	# Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Le informazioni di errore (STDERR) e il risultato del processo (STDOUT) vengono inoltre registrati nel contenitore BLOB predefinito per i cluster nei percorsi seguenti.

Processo|File nel contenitore BLOB da verificare
---|---
Hive|/HivePython/stderr<p>/HivePython/stdout
Pig|/PigPython/stderr<p>/PigPython/stdout

##<a name="next"></a>Passaggi successivi

Se è necessario caricare moduli Python non forniti per impostazione predefinita, vedere l'articolo su [come distribuire un modulo in Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) per un esempio su come eseguire questa operazione.

Per altre modalità d'uso di Pig e Hive e per informazioni su come usare MapReduce, vedere le risorse seguenti.

* [Usare Hive con HDInsight](hdinsight-use-hive.md)

* [Usare Pig con HDInsight](hdinsight-use-pig.md)

* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->