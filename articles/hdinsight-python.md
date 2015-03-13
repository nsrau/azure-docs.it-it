<properties 
	pageTitle="Usare Python con Hive e Pig in Azure HDInsight" 
	description="Informazioni su come usare le funzioni definite dall'utente di Python da Hive e Pig in Azure HDInsight." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#Usare Python con Hive e Pig in HDInsight

Hive e Pig sono soluzioni ottimali per usare i dati in HDInsight, ma in alcuni casi è necessario un linguaggio più generico. Sia Hive che Pig consentono di creare funzioni definite dall'utente usando una vasta gamma di linguaggi di programmazione. In questo articolo verrà illustrato come usare una funzione definita dall'utente Python da Hive e Pig.

> [AZURE.NOTE] La procedura descritta in questo articolo è valida per il cluster HDInsight versioni 2.1, 3.0 e 3.1.

##Sommario

* [Python in HDInsight](#python)
*   [Hive e Python](#hivepython)
*   [Pig e Python](#pigpython)
* [Esecuzione degli esempi](#running)
* [Risoluzione dei problemi](#troubleshooting)
* [Passaggi successivi](#next)

##<a name="python"></a>Python in HDInsight

Python2.7 viene installato per impostazione predefinita nei cluster HDInsight 3.0. L'installazione è disponibile nella cartella D:\Python. Hive può essere usato con questa versione di Python per l'elaborazione dei flussi, in cui i dati vengono passati tra Hive e Python con STDOUT/STDIN.

HDInsight include anche Jython, un'implementazione di Python scritta in Java. Dal momento che Pig riconosce Jython senza dover ricorrere ai flussi, è preferibile scegliere Jython quando si usa Pig.

###<a name="hivepython"></a>Hive e Python

Python può essere usato come funzione definita dall'utente da Hive tramite l'istruzione **TRANSFORM** di HiveQL. Ad esempio, il codice HiveQL seguente richiama uno script di Python archiviato nel file **streaming.py**.

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

Ecco cosa fa l'esempio:

1. L'istruzione **add file** all'inizio del file aggiunge il file **streaming.py** alla cache distribuita ed è quindi accessibile a tutti i nodi del cluster.

2. L'istruzione  **SELECT TRANSFORM ... USING 'D:\Python27\python.exe streaming.py'** seleziona i dati di **hivesampletable** e passa clientid, devicemake e devicemodel allo script **streaming.py**.

	> [AZURE.NOTE] La clausola **USING** specifica il percorso completo di python.exe, in quanto non è incluso nel percorso.

3. La clausola **AS** descrive i campi restituiti da **streaming.py**.


<a name="streamingpy"></a>
Ecco il file **streaming.py** usato nell'esempio di HiveQL.

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

1. Leggere i dati da STDIN. In questo esempio a tale scopo viene usato `sys.stdin.readline()`.

2. Per rimuovere il carattere di nuova riga finale, viene usato `string.strip(line, "\n ")`, dal momento che si è interessati solo ai dati di testo e non all'indicatore di fine riga.

2. Durante l'elaborazione di flussi tutti i valori sono contenuti in un'unica riga sono separati da un carattere di tabulazione. Si può quindi usare `string.split(line, "\t")` per dividere l'input in corrispondenza di ogni tabulazione, in modo da restituire solo i campi.

3. Al termine dell'elaborazione, l'output deve essere scritto in STDOUT in un'unica riga, con i campi separati da tabulazioni. A tale scopo viene usato `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.

4. Tutte queste operazioni vengono eseguite in un ciclo `while`, che verrà ripetuto finché non vengono più letti elementi `line`. A tale punto, verrà eseguito `break` per terminare il ciclo e di conseguenza lo script.

Oltre a quanto descritto, lo script concatena i valori di input per `devicemake` e `devicemodel` e calcola un hash del valore concatenato. In breve il funzionamento di un qualsiasi script di Python richiamato da Hive è il seguente: viene riprodotto a ciclo continuo, legge l'input fino alla fine, interrompe ogni riga di input in corrispondenza delle tabulazioni, viene elaborato, scrive una singola riga di output delimitato da tabulazioni.

Vedere [Esecuzione degli esempi](#running) per informazioni su come eseguire questo esempio nel cluster HDInsight.

###<a name="pigpython"></a>Pig e Python

Si può usare uno script di Python come funzione definita dall'utente da Pig tramite l'istruzione **GENERATE**. Ad esempio, nell'esempio seguente viene usato uno script di Python archiviato nel file **jython.py**.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

Ecco cosa fa l'esempio:

1. Registra il file contenente lo script di Python (**jython.py**) usando **Jython** e espone a Pig come **myfuncs**. Jython è un'implementazione di Python in Java e viene eseguito nella stessa istanza di Java Virtual Machine di Pig. È quindi possibile considerare lo script di Python come una chiamata di funzione tradizionale rispetto all'approccio basato su flussi usato con Hive.

2. La riga successiva carica il file di dati di esempio **sample.log** in **LOGS**. Dal momento che lo schema del file di log non è coerente, definisce inoltre ogni record (in questo caso **LINE**) come **chararray**, che è essenzialmente una stringa.

3. La terza riga esclude tutti i valori Null, archiviando il risultato dell'operazione in **LOG**.

4. Esegue quindi l'iterazione sui record in **LOG** e usa **GENERATE** per richiamare il metodo **create_structure** contenuto nello script **jython.py** caricato come **myfuncs**.  **LINE** viene usato per passare il record corrente alla funzione.

5. Viene infine eseguito il dump degli output in STDOUT con il comando **DUMP**. In questo modo i risultati verranno subito visualizzati al termine dell'operazione; in uno script effettivo in genere viene usato **STORE** per archiviare i dati in un nuovo file.

<a name="jythonpy"></a>
Ecco il file **jython.py** usato nell'esempio di Pig:

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

In precedenza l'input di **LINE** è stato definito come chararray perché non conteneva uno schema coerente. Con lo script **jython.py** i dati vengono trasformati in uno schema coerente per l'output, come descritto di seguito:

1. L'istruzione **@outputSchema** definisce il formato dei dati che verranno restituiti a Pig. In questo caso si tratta di un **contenitore di dati**, ovvero un tipo di dati Pig. Il contenitore include i seguenti campi, che sono tutti chararray (stringhe):

	* date - data di creazione della voce del log
	* time - ora di creazione della voce del log
	* classname - nome della classe per cui è stata creata la voce
	* level - livello del log
	* detail - dettagli relativi alla voce di log

2. In seguito, **def create_structure(input)** definisce la funzione a cui Pig passerà le voci.

3. I dati di esempio in **sample.log** sono per lo più conformi allo schema date, time, classname, level e detail che si intende restituire. Contengono però anche alcune righe che iniziano con la stringa '*java.lang.Exception*' e che è necessario modificare per adattarle allo schema. L'istruzione **if** verifica la presenta di queste righe, quindi forza i dati di input a spostare la stringa '*java.lang.Exception*' alla fine, portando i dati in linea con lo schema di output previsto.

4. Viene quindi usato il comando **split** per dividere i dati in corrispondenza dei primi quattro spazi. Si otterranno in tal modo cinque valori, che vengono assegnati a **date**, **time**, **classname**, **level** e **detail**.

5. I valori vengono infine restituiti a Pig.

Quando i dati vengono restituiti a Pig, lo schema sarà coerente, come quello definito nell'istruzione **@outputSchema**.

Vedere [Esecuzione degli esempi](#running) per informazioni su come eseguire questo esempio nel cluster HDInsight.

##<a name="running"></a>Esecuzione degli esempi

In queste procedure viene usato Windows Azure PowerShell. Se questo non è già installato e configurato nel computer di sviluppo, prima di iniziare vedere [Come installare e configurare Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).


1. Usando gli esempi di Python [streaming.py](#streamingpy) e [jython.py](#jythonpy), creare copie locali dei file nel computer di sviluppo.

2. Usare  lo script di PowerShell seguente per caricare i file **streaming.py** e **jython.py** nel server. Sostituire il nome del cluster Azure HDInsight e il percorso dei file **streaming.py** e **jython.py** nelle prime tre righe dello script.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	Questo script recupera le informazioni relative al cluster HDInsight, quindi estrae l'account e la chiave dell'account di archiviazione predefinito e carica i file nella radice del contenitore.

	> [AZURE.NOTE] Altri metodi per caricare gli script sono descritti nel documento [Caricare dati per processi Hadoop in HDInsight](/it-it/documentation/articles/hdinsight-upload-data/).

###Uso del Dashboard di Hive (solo esempio di Hive)

1. Dopo aver caricato il file, aprire un browser e passare a https://NomeCluster.azurehdinsight.net/. Quando vengono richieste le credenziali, immettere il nome utente e la password dell'amministratore del cluster.

	> [AZURE.NOTE] È anche possibile usare il collegamento **Gestione del cluster** nella parte inferiore del **Dashboard** di HDInsight nel portale di gestione di Azure per avviare il Dasboard di Hive.

2. Nell'**Editor Hive** sostituire la riga `select * from hivesampletable` con il seguente codice HiveQL.

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Fare clic sul pulsante **Invia** per inviare il processo. A seconda della versione del cluster HDInsight potrebbe essere visualizzata la pagina Dettagli processo. In caso contrario, selezionare **Visualizza dettagli** nell'area **Sessione processo** nella parte inferiore della pagina.

4. La pagina **Dettagli processo** verrà aggiornata fino al completamento del processo. Una volta completato il processo, verranno visualizzate le relative informazioni e l'output.

###Uso di PowerShell (esempi di Hive e Pig)

Dopo aver caricato i file, usare i seguenti script di PowerShell per avviare i processi. Al termine del processo, l'output dovrebbe essere scritto nella console di PowerShell.

**Per eseguire il processo Hive**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

L'output del processo **Hive** sarà simile al seguente:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**Per eseguire il processo Pig**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

L'output del processo **Pig** sarà simile al seguente:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Risoluzione dei problemi

Entrambi gli script di PowerShell di esempio usati per eseguire gli esempi contengono una riga impostata come commento che visualizzerà l'output degli errori relativi al processo. Se l'output previsto per il processo non viene visualizzato, rimuovere i simboli di commento dalla riga seguente e verificare se le informazioni di errore indicano un problema.

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

Le informazioni di errore (STDERR) e il risultato del processo (STDOUT) vengono inoltre registrati nel contenitore BLOB predefinito per i cluster nei percorsi seguenti.

<table>
<tr>
<td>Processo</td><td>File nel contenitore BLOB da verificare</td>
</tr>
<tr>
<td>Hive</td><td>/HivePython/stderr<br />/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr<br />/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>Passaggi successivi

Se è necessario caricare moduli Python non forniti per impostazione predefinita, vedere l'articolo su [come distribuire un modulo in Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) per un esempio su come eseguire questa operazione.

Per eseguire processi in HDInsight in modalità remota senza usare PowerShell, vedere l'articolo su [come usare Azure HDInsight da Linux](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx) per un esempio su come usare Python per eseguire processi tramite l'API REST WebHCat.
<!--HONumber=42-->
