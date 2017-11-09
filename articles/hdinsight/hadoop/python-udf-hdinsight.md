---
title: 'Funzioni definite dall''utente di Python con Apache Hive e Pig: Azure HDInsight | Microsoft Docs'
description: Informazioni su come usare le funzioni definite dall'utente di Python da Hive e Pig in HDInsight, lo stack di tecnologie Hadoop in Azure.
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
ms.date: 10/06/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 822293da48f14dc3fe29e7e95e7a30faaadbfea4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="use-python-user-defined-functions-udf-with-hive-and-pig-in-hdinsight"></a>Usare le funzioni definite dall'utente di Python (UDF) con Hive e Pig in HDInsight

Informazioni su come usare le funzioni definite dall'utente di Python con Apache Hive e Pig in Hadoop in Azure HDInsight.

## <a name="python"></a>Python in HDInsight

Python 2.7 viene installato per impostazione predefinita in HDInsight 3.0 e versioni successive. Apache Hive può essere usato con questa versione di Python per l'elaborazione di flussi. L'elaborazione di flussi usa STDOUT e STDIN per passare i dati tra Hive e la funzione definita dall'utente.

HDInsight include anche Jython, un'implementazione di Python scritta in Java. Jython viene eseguito direttamente in Java Virtual Machine e non usa lo streaming. Jython è l'interprete Python consigliato quando si usa Python con Pig.

> [!WARNING]
> La procedura in questo documento parte dai presupposti seguenti: 
>
> * L'utente deve creare gli script Python in un ambiente di sviluppo locale.
> * Caricare gli script in HDInsight usando il comando `scp` da una sessione Bash locale o lo script di PowerShell presente.
>
> Se si desidera usare l'anteprima [Azure Cloud Shell (bash)](https://docs.microsoft.com/azure/cloud-shell/overview) con HDInsight, è necessario:
>
> * Creare gli script all'interno dell'ambiente Cloud Shell.
> * Usare `scp` per caricare i file da Cloud Shell in HDInsight.
> * Usare `ssh` da Cloud Shell per connettersi a HDInsight ed eseguire gli esempi.

## <a name="hivepython"></a>UDF di Hive

Python può essere usato come funzione definita dall'utente da Hive tramite l'istruzione `TRANSFORM` di HiveQL. Ad esempio, HiveQL seguente richiama il file `hiveudf.py` archiviato nell'account di archiviazione di Azure predefinito per il cluster.

**HDInsight basato su Linux**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

**HDInsight basato su Windows**

```hiveql
add file wasb:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'D:\Python27\python.exe hiveudf.py' AS
    (clientid string, phoneLable string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

> [!NOTE]
> Nei cluster HDInsight basati su Windows la clausola `USING` deve specificare il percorso completo di python.exe.

Ecco cosa fa l'esempio:

1. L'istruzione `add file` all'inizio del file aggiunge il file `hiveudf.py` alla cache distribuita ed è quindi accessibile a tutti i nodi del cluster.
2. L'istruzione `SELECT TRANSFORM ... USING` seleziona i dati da `hivesampletable`. Passa anche i valori clientid, devicemake e devicemodel nello script `hiveudf.py`.
3. La clausola `AS` descrive i campi restituiti da `hiveudf.py`.

<a name="streamingpy"></a>

### <a name="create-the-hiveudfpy-file"></a>Creare il file hiveudf.py


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

1. Leggere una riga di dati da STDIN.
2. Il carattere di nuova riga finale viene rimosso con `string.strip(line, "\n ")`.
3. Durante l'elaborazione di flussi tutti i valori sono contenuti in un'unica riga sono separati da un carattere di tabulazione. Si può quindi usare `string.split(line, "\t")` per dividere l'input in corrispondenza di ogni tabulazione, in modo da restituire solo i campi.
4. Al termine dell'elaborazione, l'output deve essere scritto in STDOUT in un'unica riga, con i campi separati da tabulazioni. Ad esempio, `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Il ciclo `while` si ripete finché non viene letta alcuna `line`.

Lo script di output è una concatenazione di valori di input per `devicemake` e `devicemodel` e un hash del valore concatenato.

Per informazioni su come eseguire questo esempio nel cluster HDInsight, vedere [Esecuzione degli esempi](#running) .

## <a name="pigpython"></a>UDF di Pig

Si può usare uno script di Python come funzione definita dall'utente da Pig tramite l'istruzione `GENERATE`. È possibile eseguire lo script usando Jython o C Python.

* Jython viene eseguito su JVM e può essere chiamato in modo nativo da Pig.
* C Python è un processo esterno, pertanto i dati di Pig su JVM vengono inviati allo script in esecuzione in un processo di Python. Quindi l'output dello script di Python viene inviato in Pig.

Per specificare l'interprete Python, usare `register` quando si fa riferimento allo script di Python. Negli esempi seguenti gli script vengono registrati con Pig come `myfuncs`:

* **Per usare Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Per usare C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]
> Quando si usa Jython, il percorso al file pig_jython può essere un percorso locale o un percorso WASB://. Tuttavia, quando si usa C Python, è necessario fare riferimento un file nel file system locale del nodo che si usa per inviare il processo Pig.

Una volta trascorsa la registrazione, Pig Latin per questo esempio è lo stesso per entrambi:

```pig
LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Ecco cosa fa l'esempio:

1. La prima riga carica il file di dati di esempio `sample.log` in `LOGS`. Definisce anche ogni record come `chararray`.
2. La riga successiva esclude tutti i valori Null, archiviando il risultato dell'operazione in `LOG`.
3. Esegue quindi l'iterazione sui record in `LOG` e usa `GENERATE` per richiamare il metodo `create_structure` contenuto nello script di Python/Jython caricato come `myfuncs`. `LINE` viene usato per passare il record corrente alla funzione.
4. Viene infine eseguito il dump degli output in STDOUT con il comando `DUMP`. Questo comando visualizza i risultati al termine dell'operazione.

### <a name="create-the-pigudfpy-file"></a>Creare il file pigudf.py

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

Nell'esempio Pig Latin l'input di `LINE` è stato definito come chararray perché non contiene uno schema coerente. Con lo script Python i dati vengono trasformati in uno schema coerente per l'output.

1. L'istruzione `@outputSchema` definisce il formato dei dati che verranno restituiti a Pig. In questo caso si tratta di un **contenitore di dati**, ovvero un tipo di dati Pig. Il contenitore include i seguenti campi, che sono tutti chararray (stringhe):

   * date - data di creazione della voce del log
   * time - ora di creazione della voce del log
   * classname - nome della classe per cui è stata creata la voce
   * level - livello del log
   * detail - dettagli relativi alla voce di log

2. Successivamente `def create_structure(input)` definisce la funzione a cui Pig passerà le voci.

3. I dati di esempio in `sample.log` sono per lo più conformi allo schema date, time, classname, level e detail che si intende restituire. Contengono tuttavia alcune righe che iniziano con `*java.lang.Exception*`. Queste righe devono essere modificate in modo che corrispondano allo schema. L'istruzione `if` verifica la presenza di queste righe, quindi forza i dati di input a spostare la stringa `*java.lang.Exception*` alla fine, portando i dati in linea con lo schema di output previsto.

4. Viene quindi usato il comando `split` per dividere i dati in corrispondenza dei primi quattro spazi. L'output viene assegnato in `date`, `time`, `classname`, `level` e `detail`.

5. I valori vengono infine restituiti a Pig.

Quando i dati vengono restituiti a Pig, lo schema sarà coerente, come definito nell'istruzione `@outputSchema`.

## <a name="running"></a>Caricare ed eseguire gli esempi

> [!IMPORTANT]
> La procedura per **SSH** può essere eseguita solo con un cluster HDInsight basato su Linux. La procedura per **PowerShell** può essere eseguita con un cluster HDInsight basato su Linux o su Windows, ma è necessario un client Windows.

### <a name="ssh"></a>SSH

Per altre informazioni sull'uso di SSH, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Usare `scp` per copiare i file nel cluster HDInsight. Ad esempio, il comando seguente copia i file in un cluster denominato **mycluster**.

    ```bash
    scp hiveudf.py pigudf.py myuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Usare SSH per connettersi al cluster.

    ```bash
    ssh myuser@mycluster-ssh.azurehdinsight.net
    ```

    Per altre informazioni, vedere il documento [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Dalla sessione SSH, aggiungere i file python caricati in precedenza nell'archivio WASB per il cluster.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    hdfs dfs -put pigudf.py /pigudf.py
    ```

Dopo il caricamento dei file, usare la procedura seguente per eseguire i processi Hive e Pig.

#### <a name="use-the-hive-udf"></a>Usare l'UDF di Hive

1. Usare il comando seguente per connettersi a Hive:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Questo comando avvia il client Beeline.

2. Immettere la query seguente al prompt `0: jdbc:hive2://headnodehost:10001/>`:

   ```hive
   add file wasb:///hiveudf.py;
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

4. Per uscire da Beeline, usare il comando seguente:

    ```hive
    !q
    ```

#### <a name="use-the-pig-udf"></a>Usare l'UDF di Pig

1. Usare il comando seguente per connettersi a Pig:

    ```bash
    pig
    ```

2. Al prompt `grunt>` immettere le istruzioni seguenti:

   ```pig
   Register wasb:///pigudf.py using jython as myfuncs;
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
   LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Una volta completato questo processo, si dovrebbe vedere lo stesso risultato di quando è stato eseguito lo script in precedenza con Jython.

### <a name="powershell-upload-the-files"></a>PowerShell: Caricare i file

È possibile usare PowerShell per caricare i file nel server di HDInsight. Usare lo script seguente per caricare i file di Python:

> [!IMPORTANT] 
> I passaggi descritti in questa sezione usano Azure PowerShell. Per altre informazioni sull'uso di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=5-41)]

> [!IMPORTANT]
> Modifica il valore `C:\path\to` per il percorso dei file in un ambiente di sviluppo.

Questo script recupera le informazioni relative al cluster HDInsight, quindi estrae l'account e la chiave dell'account di archiviazione predefinito e carica i file nella radice del contenitore.

> [!NOTE]
> Per altre informazioni sul caricamento dei file, vedere il documento [Caricare dati per processi Hadoop in HDInsight](../hdinsight-upload-data.md).

#### <a name="powershell-use-the-hive-udf"></a>PowerShell: usare l'UDF di Hive

PowerShell può essere usato anche per eseguire in remoto le query Hive. Usare lo script di PowerShell seguente per eseguire una query di Hive che usa lo script **hiveudf.py**:

> [!IMPORTANT]
> Prima dell'esecuzione, lo script richiede le informazioni sull'account HTTPS/Amministratore per il cluster HDInsight.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=45-94)]

L'output del processo **Hive** sarà simile al seguente esempio:

    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9

#### <a name="pig-jython"></a>Pig (Jython)

PowerShell può essere usato anche per eseguire processi di Pig Latin. Per eseguire un processo di Pig Latin che usa lo script **pigudf.py**, usare lo script di PowerShell seguente:

> [!NOTE]
> Durante l'invio remoto di un processo con PowerShell, non è possibile usare C Python come interprete.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=98-144)]

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

Se è necessario caricare moduli Python non forniti per impostazione predefinita, vedere [Come distribuire un modulo in Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx).

Per altre modalità d'uso di Pig e Hive e per informazioni su come usare MapReduce, vedere i documenti seguenti:

* [Usare Hive con HDInsight](hdinsight-use-hive.md)
* [Usare Pig con HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con HDInsight](hdinsight-use-mapreduce.md)
