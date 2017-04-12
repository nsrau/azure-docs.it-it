---
title: Usare Python con Hive e Pig in HDInsight | Documentazione Microsoft
description: Informazioni su come usare le funzioni definite dall&quot;utente di Python da Hive e Pig in HDInsight, lo stack di tecnologie Hadoop in Azure.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c44d6606-28cd-429b-b535-235e8f34a664
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 02/27/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 1fc13142d3e4f54e0945032a404eb497746ee5a0
ms.lasthandoff: 04/12/2017

---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Usare le funzioni definite dall'utente di Python (UDF) con Hive e Pig in HDInsight

Hive e Pig sono soluzioni ottimali per usare i dati in HDInsight, ma in alcuni casi è necessario un linguaggio più generico. Sia Hive che Pig consentono di creare funzioni definite dall'utente (UDF) usando diversi linguaggi di programmazione. In questo articolo verrà illustrato come usare un'UDF Python da Hive e Pig.

## <a name="requirements"></a>Requisiti

* Un cluster HDInsight

  > [!IMPORTANT]
  > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

* Un editor di testo

## <a name="python"></a>Python in HDInsight

Python2.7 viene installato per impostazione predefinita nei cluster HDInsight 3.0 e versioni successive. Hive può essere usato con questa versione di Python per l'elaborazione dei flussi, in cui i dati vengono passati tra Hive e Python con STDOUT/STDIN.

HDInsight include anche Jython, un'implementazione di Python scritta in Java. Dal momento che Pig riconosce Jython senza dover ricorrere ai flussi, è preferibile scegliere Jython quando si usa Pig. È possibile usare Python normale (C Python) anche con Pig.

## <a name="hivepython"></a>Hive e Python

Python può essere usato come funzione definita dall'utente da Hive tramite l'istruzione **TRANSFORM** di HiveQL. Ad esempio, il codice HiveQL seguente richiama uno script di Python archiviato nel file **streaming.py** .

**HDInsight basato su Linux**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight basato su Windows**

```hiveql
add file wasbs:///streaming.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe streaming.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Nei cluster HDInsight basati su Windows, la clausola **USING** deve specificare il percorso completo di python.exe.

Ecco cosa fa l'esempio:

1. L'istruzione **add file** all'inizio del file aggiunge il file **streaming.py** alla cache distribuita ed è quindi accessibile a tutti i nodi del cluster.
2. L'istruzione **SELECT TRANSFORM ... USANDO** seleziona i dati di **hivesampletable**. Passa inoltre i valori clientid, devicemake e devicemodel nello script **streaming.py**.
3. La clausola **AS** descrive i campi restituiti da **streaming.py**

<a name="streamingpy"></a> Questo è il file **streaming.py** usato nell'esempio di HiveQL.

```python
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
```

Lo script esegue le azioni seguenti:

1. Leggere una riga di dati da STDIN.
2. Il carattere di nuova riga finale viene rimosso con `string.strip(line, "\n ")`.
3. Durante l'elaborazione di flussi tutti i valori sono contenuti in un'unica riga sono separati da un carattere di tabulazione. Si può quindi usare `string.split(line, "\t")` per dividere l'input in corrispondenza di ogni tabulazione, in modo da restituire solo i campi.
4. Al termine dell'elaborazione, l'output deve essere scritto in STDOUT in un'unica riga, con i campi separati da tabulazioni. A tale scopo viene usato `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Il ciclo `while` si ripete finché non viene letta alcuna `line`.

Lo script di output è una concatenazione di valori di input per `devicemake` e `devicemodel` e un hash del valore concatenato.

Per informazioni su come eseguire questo esempio nel cluster HDInsight, vedere [Esecuzione degli esempi](#running) .

## <a name="pigpython"></a>Pig e Python

Si può usare uno script di Python come funzione definita dall'utente da Pig tramite l'istruzione **GENERATE** . È possibile eseguire lo script usando Jython o C Python.

La differenza principale tra questi è che Jython viene eseguito su JVM e può essere chiamato in modo nativo da Pig. C Python è un processo esterno, pertanto i dati di Pig su JVM vengono inviati allo script in esecuzione in un processo di Python. Quindi l'output dello script di Python viene inviato in Pig.

Per determinare se Pig usa Jython o C Python per eseguire lo script, usare **register** quando si fa riferimento allo script Python da Pig Latin. Negli esempi seguenti, gli script vengono registrati con Pig come **myfuncs**:

* **Per usare Jython**: `register '/path/to/pig_python.py' using jython as myfuncs;`
* **Per usare C Python**: `register '/path/to/pig_python.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Quando si usa Jython, il percorso al file pig_jython può essere un percorso locale o un percorso WASB://. Tuttavia, quando si usa C Python, è necessario fare riferimento un file nel file system locale del nodo che si usa per inviare il processo Pig.

Una volta trascorsa la registrazione, Pig Latin per questo esempio è lo stesso per entrambi:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Ecco cosa fa l'esempio:

1. La prima riga carica il file di dati di esempio **sample.log** in **LOG**. Definisce inoltre ogni record come **chararray**.
2. La riga successiva esclude tutti i valori Null, archiviando il risultato dell'operazione in **LOG**.
3. Esegue quindi l'iterazione sui record in **LOG** e usa **GENERATE** per richiamare il metodo **create_structure** contenuto nello script Python/Jython caricato come **myfuncs**.  **LINE** viene usato per passare il record corrente alla funzione.
4. Viene infine eseguito il dump degli output in STDOUT con il comando **DUMP** . Questo consente di visualizzare i risultati al termine dell'operazione.

Il file di script Python è simile per Python C e Jython; l'unica differenza è che è necessario importare da **pig\_util** quando si usa C Python. Lo script **pig\_python.py** è il seguente:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema

@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

> [!NOTE]
> Non è necessario installare 'pig_util', perché è disponibile automaticamente per lo script.

Si ricordi che in precedenza, l'input di **LINE** è stato definito come chararray perché non conteneva uno schema coerente. Con lo script Python i dati vengono trasformati in uno schema coerente per l'output.

1. L'istruzione **@outputSchema** definisce il formato dei dati che verranno restituiti a Pig. In questo caso si tratta di un **contenitore di dati**, ovvero un tipo di dati Pig. Il contenitore include i seguenti campi, che sono tutti chararray (stringhe):

   * date - data di creazione della voce del log
   * time - ora di creazione della voce del log
   * classname - nome della classe per cui è stata creata la voce
   * level - livello del log
   * detail - dettagli relativi alla voce di log

2. Successivamente **def create_structure(input)** definisce la funzione a cui Pig passerà le voci.

3. I dati di esempio in **sample.log**sono per lo più conformi allo schema date, time, classname, level e detail che si intende restituire. Contengono però anche alcune righe che iniziano con la stringa '*java.lang.Exception*' e che è necessario modificare per adattarle allo schema. L'istruzione **if** verifica la presenta di queste righe, quindi forza i dati di input a spostare la stringa '*java.lang.Exception*' alla fine, portando i dati in linea con lo schema di output previsto.

4. Viene quindi usato il comando **split** per dividere i dati in corrispondenza dei primi quattro spazi. L'output viene assegnato in **date**, **time**, **classname**, **level** e **detail**.

5. I valori vengono infine restituiti a Pig.

Quando i dati vengono restituiti a Pig, lo schema sarà coerente, come definito nell'istruzione **@outputSchema**.

## <a name="running"></a>Esecuzione degli esempi
Se si usa un cluster HDInsight basato su Linux, eseguire la procedura con **SSH**. Se si usa un cluster HDInsight basato su Windows e un client Windows, eseguire la procedura con **PowerShell** .

### <a name="ssh"></a>SSH

Per altre informazioni sull'uso di SSH, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

1. Usando gli esempi [streaming.py](#streamingpy) e [pig_python.py](#jythonpy) di Python, creare copie locali dei file nel computer di sviluppo.

2. Usare `scp` per copiare i file nel cluster HDInsight. Ad esempio, il comando seguente copia i file in un cluster denominato **mycluster**.

        scp streaming.py pig_python.py myuser@mycluster-ssh.azurehdinsight.net:

3. Usare SSH per connettersi al cluster. Il comando seguente stabilisce ad esempio la connessione a un cluster denominato **mycluster** come utente **myuser**.

        ssh myuser@mycluster-ssh.azurehdinsight.net
4. Dalla sessione SSH, aggiungere i file python caricati in precedenza nell'archivio WASB per il cluster.

        hdfs dfs -put streaming.py /streaming.py
        hdfs dfs -put pig_python.py /pig_python.py

Dopo il caricamento dei file, usare la procedura seguente per eseguire i processi Hive e Pig.

#### <a name="hive"></a>Hive

1. Usare il comando `hive` per avviare la shell di Hive. Una volta caricata la shell, verrà visualizzato un prompt `hive>` .
2. Al prompt `hive>` immettere il codice seguente.

   ```hive
   add file wasbs:///streaming.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python streaming.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```
3. Dopo l'immissione dell'ultima riga il processo dovrebbe essere avviato. Al termine del processo, restituisce un output simile al seguente esempio:

        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100041    RIM 9650    d476f3687700442549a83fac4560c51c
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
        100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig"></a>Pig

1. Usare il comando `pig` per avviare la shell. Una volta caricata la shell, verrà visualizzato un prompt `grunt>` .

2. Al prompt `grunt>` immettere le istruzioni seguenti:

   ```pig
   Register wasbs:///pig_python.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Dopo l'immissione della riga seguente, il processo dovrebbe essere avviato. Al termine del processo, restituisce un output simile al seguente.

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Usare `quit` per chiudere la shell Grunt e quindi il prompt seguente per modificare il file pig_python.py nel file system locale:

    nano pig_python.py

5. Una volta nell'editor, rimuovere i simboli di commenti dalla riga seguente rimuovendo il carattere `#` dall'inizio della riga:

        #from pig_util import outputSchema

    Una volta apportata la modifica, usare Ctrl + X per uscire dall'editor. Selezionare Y e quindi INVIO per salvare le modifiche.

6. Usare il comando `pig` per avviare di nuovo la shell. Una volta al prompt `grunt>` usare la riga seguente per eseguire il script di Python con l'interprete C Python.

   ```pig
   Register 'pig_python.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Una volta completato questo processo, si dovrebbe vedere lo stesso risultato di quando è stato eseguito lo script in precedenza con Jython.

### <a name="powershell"></a>PowerShell

Nella procedura seguente viene usato Azure PowerShell. Per altre informazioni sull'uso di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Usando gli esempi [streaming.py](#streamingpy) e [pig_python.py](#jythonpy) di Python, creare copie locali dei file nel computer di sviluppo.
2. Usare lo script di PowerShell seguente per caricare i file **streaming.py** e **pig\_python.py** nel server. Sostituire il nome del cluster HDInsight di Azure e il percorso per i file **streaming.py** e **pig\_python.py** nelle prime tre righe dello script.

   ```powershell
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get cluster info
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
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
   ```

    Questo script recupera le informazioni relative al cluster HDInsight, quindi estrae l'account e la chiave dell'account di archiviazione predefinito e carica i file nella radice del contenitore.

   > [!NOTE]
   > Altri metodi per caricare gli script sono descritti nel documento [Caricare dati per processi Hadoop in HDInsight](hdinsight-upload-data.md) .

Dopo aver caricato i file, usare i seguenti script di PowerShell per avviare i processi. Al termine del processo, l'output dovrebbe essere scritto nella console di PowerShell.

#### <a name="hive"></a>Hive

Questo script esegue lo script **streaming.py**. Prima dell'esecuzione, verranno richieste le informazioni sull'account HTTPs/Admin per il cluster HDInsight.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

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
#   -HttpCredential $creds `
#   -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

L'output del processo **Hive** sarà simile al seguente esempio:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

Nel seguente script si usa lo script **pig_python.py** tramite l'interprete Jython. Prima dell'esecuzione, verranno richieste le informazioni su HTTPs/Admin per il cluster HDInsight.

> [!NOTE]
> Durante l'invio remoto di un processo con PowerShell, non è possibile usare C Python come interprete.

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

$PigQuery = "Register wasbs:///pig_python.py using jython as myfuncs;" +
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
#    -Clustername $clusterName `
#    -JobId $job.JobId `
#    -HttpCredential $creds `
#    -DisplayOutputType StandardError
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

L'output del processo **Pig** sarà simile al seguente:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="errors-when-running-jobs"></a>Errori durante l'esecuzione di processi

Quando si esegue il processo hive, è possibile riscontrare un errore simile al seguente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Questo problema potrebbe essere causato dalle terminazioni di riga nel file streaming.py. Molti editor di Windows usano per impostazione predefinita CRLF come terminazione di riga, mentre le applicazioni Linux prevedono in genere LF.

È possibile usare le istruzioni di PowerShell seguenti per rimuovere i caratteri CR prima di caricare il file in HDInsight:

```powershell
$original_file ='c:\path\to\streaming.py'
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

### <a name="powershell-scripts"></a>Script di PowerShell

Entrambi gli script di esempio di PowerShell usati per eseguire gli esempi contengono una riga impostata come commento che mostra l'output degli errori relativi al processo. Se l'output previsto per il processo non viene visualizzato, rimuovere i simboli di commento dalla riga seguente e verificare se le informazioni di errore indicano un problema.

```powershell
# Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Le informazioni sull'errore (STDERR) e il risultato del processo (STDOUT) vengono inoltre registrati nell'archivio di HDInsight.

| Processo | File nel contenitore BLOB da verificare |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Passaggi successivi

Se è necessario caricare moduli Python non forniti per impostazione predefinita, vedere [Come distribuire un modulo in Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Per altre modalità d'uso di Pig e Hive e per informazioni su come usare MapReduce, vedere i documenti seguenti:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)

