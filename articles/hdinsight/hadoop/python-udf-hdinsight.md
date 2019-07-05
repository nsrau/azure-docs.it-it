---
title: Funzioni definite dall'utente di Python con Apache Hive e Apache Pig - Azure HDInsight
description: Informazioni su come usare le funzioni definite dall'utente di Python da Apache Hive e Apache Pig in HDInsight, lo stack di tecnologie Apache Hadoop in Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 03/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: d74c40264e8ed535d250e938487885a848ba6b47
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484189"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Usare le funzioni definite dall'utente di Python con Apache Hive e Apache Pig in HDInsight

Informazioni su come usare le funzioni definite dall'utente di Python con Apache Hive e Apache Pig in Apache Hadoop in Azure HDInsight.

## <a name="python"></a>Python in HDInsight

Python 2.7 viene installato per impostazione predefinita in HDInsight 3.0 e versioni successive. Apache Hive può essere usato con questa versione di Python per l'elaborazione di flussi. L'elaborazione di flussi usa STDOUT e STDIN per passare i dati tra Hive e la funzione definita dall'utente.

HDInsight include anche Jython, un'implementazione di Python scritta in Java. Jython viene eseguito direttamente in Java Virtual Machine e non usa lo streaming. Jython è l'interprete Python consigliato quando si usa Python con Pig.

## <a name="prerequisites"></a>Prerequisiti

* **Un cluster Hadoop in HDInsight**. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](apache-hadoop-linux-tutorial-get-started.md).
* **Un client SSH**. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Il [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria cluster. Il risultato sarà wasb: / / per archiviazione di Azure, abfs: / / per Azure Data Lake archiviazione Gen2 o adl: / / per Azure Data Lake archiviazione Gen1. Se il trasferimento protetto è abilitato per l'archiviazione di Azure o Data Lake Storage Gen2, l'URI sarà wasbs: / / o abfss: / /, vedere rispettivamente anche [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).
* **Possibili modifiche alla configurazione di archiviazione.**  Visualizzare [configurazione dell'archivio](#storage-configuration) se si usa tipologia di account di archiviazione `BlobStorage`.
* facoltativo.  Se si intende usare PowerShell, è necessario il [modulo di AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) installato.

> [!NOTE]  
> L'account di archiviazione usato in questo articolo è stato archiviazione di Azure con [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md) abilitato e pertanto `wasbs` viene usato in tutto l'articolo.

## <a name="storage-configuration"></a>Configurazione dell'archiviazione
È richiesta alcuna azione se l'account di archiviazione usato è di tipo `Storage (general purpose v1)` o `StorageV2 (general purpose v2)`.  Il processo in questo articolo genererà output almeno `/tezstaging`.  Una configurazione di hadoop predefinita conterrà `/tezstaging` nella `fs.azure.page.blob.dir` variabile di configurazione in `core-site.xml` per il servizio `HDFS`.  Questa configurazione causerà l'output per la directory da BLOB di pagine, che non sono supportati per il tipo di account di archiviazione `BlobStorage`.  Per utilizzare `BlobStorage` di questo articolo, rimuovere `/tezstaging` dal `fs.azure.page.blob.dir` variabile di configurazione.  La configurazione è possibile accedere dal [Ambari UI](../hdinsight-hadoop-manage-ambari.md).  In caso contrario, si riceverà il messaggio di errore: `Page blob is not supported for this account type.`

> [!WARNING]  
> La procedura in questo documento parte dai presupposti seguenti:  
>
> * L'utente deve creare gli script Python in un ambiente di sviluppo locale.
> * Caricare gli script in HDInsight tramite il `scp` comando o lo script di PowerShell fornito.
>
> Se si desidera utilizzare il [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) per lavorare con HDInsight, quindi è necessario:
>
> * Creare gli script all'interno dell'ambiente Cloud Shell.
> * Usare `scp` per caricare i file da Cloud Shell in HDInsight.
> * Usare `ssh` da Cloud Shell per connettersi a HDInsight ed eseguire gli esempi.

## <a name="hivepython"></a>UDF Apache Hive

Python può essere usato come funzione definita dall'utente da Hive tramite l'istruzione `TRANSFORM` di HiveQL. Ad esempio, HiveQL seguente richiama il file `hiveudf.py` archiviato nell'account di archiviazione di Azure predefinito per il cluster.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Ecco cosa fa l'esempio:

1. L'istruzione `add file` all'inizio del file aggiunge il file `hiveudf.py` alla cache distribuita ed è quindi accessibile a tutti i nodi del cluster.
2. L'istruzione `SELECT TRANSFORM ... USING` seleziona i dati da `hivesampletable`. Passa anche i valori clientid, devicemake e devicemodel nello script `hiveudf.py`.
3. La clausola `AS` descrive i campi restituiti da `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-file"></a>Crea file

Nell'ambiente di sviluppo creare un file di testo denominato `hiveudf.py`. Usare il codice seguente come contenuto del file:

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

1. Legge una riga di dati da STDIN.
2. Il carattere di nuova riga finale viene rimosso con `string.strip(line, "\n ")`.
3. Durante l'elaborazione di flussi tutti i valori sono contenuti in un'unica riga sono separati da un carattere di tabulazione. Si può quindi usare `string.split(line, "\t")` per dividere l'input in corrispondenza di ogni tabulazione, in modo da restituire solo i campi.
4. Al termine dell'elaborazione, l'output deve essere scritto in STDOUT in un'unica riga, con i campi separati da tabulazioni. Ad esempio: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Il ciclo `while` si ripete finché non viene letta alcuna `line`.

Lo script di output è una concatenazione di valori di input per `devicemake` e `devicemodel` e un hash del valore concatenato.

### <a name="upload-file-shell"></a>Caricare il file (shell)
I comandi seguenti, sostituire `sshuser` con il nome utente effettivo se diverso.  Sostituire `mycluster` con il nome effettivo del cluster.  Verificare nella directory di lavoro in cui si trova il file.

1. Usare `scp` per copiare i file nel cluster HDInsight. Modificare e immettere il comando seguente:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Usare SSH per connettersi al cluster.  Modificare e immettere il comando seguente:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Dalla sessione SSH, aggiungere i file python caricati in precedenza nella risorsa di archiviazione per il cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Usare Hive definita dall'utente (shell)

1. Per connettersi a Hive, usare il comando seguente dalla sessione SSH aperta:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Questo comando avvia il client Beeline.

2. Immettere la query seguente al prompt `0: jdbc:hive2://headnodehost:10001/>`:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
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

4. Per uscire da Beeline, immettere il comando seguente:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Caricare il file (PowerShell)

PowerShell può essere usato anche per eseguire in remoto le query Hive. Verificare che nella directory di lavoro in cui è `hiveudf.py` si trova.  Usare lo script di PowerShell seguente per eseguire una query Hive che usa il `hiveudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Per altre informazioni sul caricamento dei file, vedere il documento [Caricare dati per processi Apache Hadoop in HDInsight](../hdinsight-upload-data.md).


#### <a name="use-hive-udf"></a>Usare Hive definita dall'utente


```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
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


## <a name="pigpython"></a>UDF Apache Pig

Si può usare uno script di Python come funzione definita dall'utente da Pig tramite l'istruzione `GENERATE`. È possibile eseguire lo script usando Jython o C Python.

* Jython viene eseguito su JVM e può essere chiamato in modo nativo da Pig.
* C Python è un processo esterno, pertanto i dati di Pig su JVM vengono inviati allo script in esecuzione in un processo di Python. Quindi l'output dello script di Python viene inviato in Pig.

Per specificare l'interprete Python, usare `register` quando si fa riferimento allo script di Python. Negli esempi seguenti gli script vengono registrati con Pig come `myfuncs`:

* **Per usare Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Per usare C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Quando si usa Jython, il percorso al file pig_jython può essere un percorso locale o un WASBS: / / percorso. Tuttavia, quando si usa C Python, è necessario fare riferimento un file nel file system locale del nodo che si usa per inviare il processo Pig.

Una volta trascorsa la registrazione, Pig Latin per questo esempio è lo stesso per entrambi:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Ecco cosa fa l'esempio:

1. La prima riga carica il file di dati di esempio `sample.log` in `LOGS`. Definisce anche ogni record come `chararray`.
2. La riga successiva esclude tutti i valori Null, archiviando il risultato dell'operazione in `LOG`.
3. Esegue quindi l'iterazione sui record in `LOG` e usa `GENERATE` per richiamare il metodo `create_structure` contenuto nello script di Python/Jython caricato come `myfuncs`. `LINE` viene usato per passare il record corrente alla funzione.
4. Viene infine eseguito il dump degli output in STDOUT con il comando `DUMP`. Questo comando visualizza i risultati al termine dell'operazione.

### <a name="create-file"></a>Crea file

Nell'ambiente di sviluppo creare un file di testo denominato `pigudf.py`. Usare il codice seguente come contenuto del file:

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

Nell'esempio Pig Latin l'input di `LINE` è stato definito come chararray perché non esiste uno schema coerente. Con lo script Python i dati vengono trasformati in uno schema coerente per l'output.

1. L'istruzione `@outputSchema` definisce il formato dei dati che verranno restituiti a Pig. In questo caso si tratta di un **contenitore di dati**, ovvero un tipo di dati Pig. Il contenitore include i seguenti campi, che sono tutti chararray (stringhe):

   * date - data di creazione della voce del log
   * time - ora di creazione della voce del log
   * classname - nome della classe per cui è stata creata la voce
   * level - livello del log
   * detail - dettagli relativi alla voce di log

2. Successivamente `def create_structure(input)` definisce la funzione a cui Pig passerà le voci.

3. I dati di esempio in `sample.log` sono per lo più conformi allo schema date, time, classname, level e detail. Contengono tuttavia alcune righe che iniziano con `*java.lang.Exception*`. Queste righe devono essere modificate in modo che corrispondano allo schema. L'istruzione `if` verifica la presenza di queste righe, quindi forza i dati di input a spostare la stringa `*java.lang.Exception*` alla fine, portando i dati in linea con lo schema di output previsto.

4. Viene quindi usato il comando `split` per dividere i dati in corrispondenza dei primi quattro spazi. L'output viene assegnato in `date`, `time`, `classname`, `level` e `detail`.

5. I valori vengono infine restituiti a Pig.

Quando i dati vengono restituiti a Pig, lo schema sarà coerente, come definito nell'istruzione `@outputSchema`.



### <a name="upload-file-shell"></a>Caricare il file (shell)

I comandi seguenti, sostituire `sshuser` con il nome utente effettivo se diverso.  Sostituire `mycluster` con il nome effettivo del cluster.  Verificare nella directory di lavoro in cui si trova il file.

1. Usare `scp` per copiare i file nel cluster HDInsight. Modificare e immettere il comando seguente:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Usare SSH per connettersi al cluster.  Modificare e immettere il comando seguente:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Dalla sessione SSH, aggiungere i file python caricati in precedenza nella risorsa di archiviazione per il cluster.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```


### <a name="use-pig-udf-shell"></a>Usare l'UDF di Pig (shell)

1. Per connettersi a pig, usare il comando seguente dalla sessione SSH aperta:

    ```bash
    pig
    ```

2. Al prompt `grunt>` immettere le istruzioni seguenti:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Dopo l'immissione della riga seguente, il processo dovrebbe essere avviato. Al termine del processo, restituisce un output simile ai dati seguenti:

        ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
        ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
        ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
        ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
        ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

4. Usare `quit` per chiudere la shell Grunt e quindi il prompt seguente per modificare il file pigudf.py nel file system locale:

    ```bash
    nano pigudf.py
    ```

5. Una volta nell'editor, rimuovere i simboli di commenti dalla riga seguente rimuovendo il carattere `#` dall'inizio della riga:

    ```bash
    #from pig_util import outputSchema
    ```

    In questo modo lo script Python viene modificato in modo da non funzionare più con Jython ma con C Python. Una volta apportata la modifica, usare **Ctrl+X** per uscire dall'editor. Selezionare **Y** e quindi **INVIO** per salvare le modifiche.

6. Usare il comando `pig` per avviare di nuovo la shell. Una volta al prompt `grunt>` usare la riga seguente per eseguire il script di Python con l'interprete C Python.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Una volta completato questo processo, si dovrebbe vedere lo stesso risultato di quando è stato eseguito lo script in precedenza con Jython.


### <a name="upload-file-powershell"></a>Caricare il file (PowerShell)

PowerShell può essere usato anche per eseguire in remoto le query Hive. Verificare che nella directory di lavoro in cui è `pigudf.py` si trova.  Usare lo script di PowerShell seguente per eseguire una query Hive che usa il `pigudf.py` script:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Usare Pig di funzione definita dall'utente (PowerShell)

> [!NOTE]  
> Durante l'invio remoto di un processo con PowerShell, non è possibile usare C Python come interprete.

PowerShell può essere usato anche per eseguire processi di Pig Latin. Per eseguire un processo Pig Latin che usa il `pigudf.py` script, usare lo script di PowerShell seguente:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

L'output del processo **Pig** sarà simile ai dati seguenti:

    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="errors-when-running-jobs"></a>Errori durante l'esecuzione di processi

Quando si esegue il processo hive, è possibile riscontrare un errore simile al testo seguente:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Questo problema potrebbe essere causato dalle terminazioni di riga nel file di Python. Molti editor di Windows usano per impostazione predefinita CRLF come terminazione di riga, mentre le applicazioni Linux prevedono in genere LF.

È possibile usare le istruzioni di PowerShell seguenti per rimuovere i caratteri CR prima di caricare il file in HDInsight:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>Script di PowerShell

Entrambi gli script di esempio di PowerShell usati per eseguire gli esempi contengono una riga impostata come commento che mostra l'output degli errori relativi al processo. Se l'output previsto per il processo non viene visualizzato, rimuovere i simboli di commento dalla riga seguente e verificare se le informazioni di errore indicano un problema.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Le informazioni sull'errore (STDERR) e il risultato del processo (STDOUT) vengono anche registrati nell'archivio di HDInsight.

| Processo | File nel contenitore BLOB da verificare |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next"></a>Passaggi successivi

Se è necessario caricare moduli Python non forniti per impostazione predefinita, vedere [Come distribuire un modulo in Azure HDInsight](https://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Per altre modalità d'uso di Pig e Hive e per informazioni su come usare MapReduce, vedere i documenti seguenti:

* [Usare Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)
