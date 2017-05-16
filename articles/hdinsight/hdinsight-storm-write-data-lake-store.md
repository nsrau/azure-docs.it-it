---
title: Usare Archivio Azure Data Lake con Apache Storm in HDInsight
description: Informazioni su come scrivere dati nell&quot;Archivio Azure Data Lake da una topologia Apache Storm in HDInsight. Questo documento e l&quot;esempio associato illustrano come usare il componente HdfsBolt per scrivere nell&quot;Archivio Data Lake.
services: hdinsight
documentationcenter: na
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 1df98653-a6c8-4662-a8c6-5d288fc4f3a6
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/21/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 30edf20d7fc742da9b42d3ea9baafcce31141259
ms.contentlocale: it-it
ms.lasthandoff: 04/22/2017


---
# <a name="use-azure-data-lake-store-with-apache-storm-with-hdinsight-java"></a>Usare Azure Data Lake Store con Apache Storm in HDInsight (Java)

Archivio Azure Data Lake è un servizio di archiviazione cloud compatibile con HDFS che garantisce una velocità effettiva elevata, disponibilità, durata e affidabilità per i dati. In questo documento viene illustrato come usare una topologia Storm basata su Java per scrivere i dati in Azure Data Lake Store. La procedura descritta in questo documento usa il componente [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html), offerto nell'ambito di Apache Storm.

> [!IMPORTANT]
> La topologia di esempio usata in questo documento si basa su componenti inclusi nei cluster Storm in HDInsight. Se usata con altri cluster Apache Storm, potrebbe essere necessario modificarla per il funzionamento con Archivio Azure Data Lake.

## <a name="how-to-work-with-azure-data-lake-store"></a>Usare Azure Data Lake Store

Per HDInsight, Data Lake Store è un file system compatibile con HDFS e di conseguenza è possibile usare il bolt Storm-HDFS per scrivervi. Quando si lavora con Azure Data Lake da HDInsight, è possibile usare una combinazione di file di `adl://`.

* Se Data Lake Store è la risorsa di archiviazione principale per il cluster, usare `adl:///`. Questa è la radice dell'archiviazione cluster in Azure Data Lake. Potrebbe tradursi in un percorso di /clusters/CLUSTERNAME nell'account Data Lake Store.
* Se Data Lake Store è la risorsa di archiviazione aggiuntiva per il cluster, usare `adl://DATALAKEACCOUNT.azuredatalakestore.net/`. Questo URI specifica l'account Data Lake Store in cui vengono scritti i dati. I dati vengono scritti a partire dalla radice di Data Lake Store.

    > [!NOTE]
    > È possibile usare questo formato URI anche per salvare i dati nell'account Data Lake Store contenente l'archiviazione primaria per il cluster. Ciò consente di salvare i dati all'esterno del percorso della directory che contiene HDInsight.

Il codice Java seguente illustra come è possibile usare il bolt Storm-HDFS per scrivere dati in una directory denominata `/stormdata` su un account Data Lake Store denominato `MYDATALAKE`.

```java
// 1. Create sync and rotation policies to control when data is synched
//    (written) to the file system and when to roll over into a new file.
SyncPolicy syncPolicy = new CountSyncPolicy(1000);
FileRotationPolicy rotationPolicy = new FileSizeRotationPolicy(0.5f, Units.KB);
// 2. Set the format. In this case, comma delimited
RecordFormat recordFormat = new DelimitedRecordFormat().withFieldDelimiter(",");
// 3. Set the directory name. In this case, '/stormdata/'
FileNameFormat fileNameFormat = new DefaultFileNameFormat().withPath("/stormdata/");
// 4. Create the bolt using the previously created settings,
//    and also tell it the base URL to your Data Lake Store.
// NOTE! Replace 'MYDATALAKE' below with the name of your data lake store.
HdfsBolt adlsBolt = new HdfsBolt()
    .withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")
        .withRecordFormat(recordFormat)
        .withFileNameFormat(fileNameFormat)
        .withRotationPolicy(rotationPolicy)
        .withSyncPolicy(syncPolicy);
// 4. Give it a name and wire it up to the bolt it accepts data
//    from. NOTE: The name used here is also used as part of the
//    file name for the files written to Data Lake Store.
builder.setBolt("ADLStoreBolt", adlsBolt, 1)
    .globalGrouping("finalcount");
```

Nello YAML seguente viene illustrato come usare il bolt Storm HDFS del framework Flux:

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - KB

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

  - id: "rotationAction"
    className: "org.apache.storm.hdfs.common.rotation.MoveFileAction"
    configMethods:
      - name: "toDestination"
        args: ["${hdfs.dest.dir}"]

# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1
```

> [!NOTE]
> Gli esempi in questo documento usano il framework Flux.

## <a name="prerequisites"></a>Prerequisiti

* [Java JDK 1.8](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o versione successiva. HDInsight 3.5 richiede Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* Un cluster Storm nella versione 3.5 di HDInsight. Per creare un nuovo cluster Storm in HDInsight, seguire la procedura descritta nel documento [Usare HDInsight con Archivio Data Lake tramite Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) .

### <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-8-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.8`.
* **PATH** : deve contenere i percorsi seguenti:
  
  * **JAVA\_HOME** (o percorso equivalente)
  * **JAVA\_HOME\bin** (o percorso equivalente)
  * Directory in cui è installato Maven

## <a name="topology-implementation"></a>Implementare la topologia

L'esempio usato in questo documento è scritto in Java e fa uso dei componenti seguenti:

* **TickSpout**: genera i dati usati da altri componenti nella topologia.
* **PartialCount**: conta gli eventi generati da TickSpout.
* **FinalCount**: aggrega i dati conteggiati da PartialCount.
* **ADLStoreBolt**: scrive i dati in Azure Data Lake Store usando il componente [HdfsBolt](http://storm.apache.org/releases/1.0.2/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html).

Il progetto contenente questa topologia è disponibile per il download da [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

## <a name="build-and-package-the-topology"></a>Compilare e creare il pacchetto della topologia

1. Scaricare il progetto di esempio da [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) nell'ambiente di sviluppo.

2. Aprire il file `StormToDataLake\src\main\java\com\microsoft\example\StormToDataLakeStore.java` in un editor e trovare la riga contenente `.withFsUrl("adl://MYDATALAKE.azuredatalakestore.net/")`. Sostituire **MYDATALAKE** con il nome dell'istanza di Azure Data Lake Store usata durante la creazione del server HDInsight.

3. Da un prompt dei comandi, un terminale o una sessione della shell, passare alla directory radice del progetto scaricato ed eseguire questi comandi per compilare e creare il pacchetto della topologia.
   
        mvn compile
        mvn package
   
    Al termine della compilazione e della creazione del pacchetto sarà presente una nuova directory denominata `target`, che contiene un file denominato `StormToDataLakeStore-1.0-SNAPSHOT.jar`, contenente a sua volta la topologia compilata.

## <a name="deploy-and-run-the-topology"></a>Distribuire ed eseguire la topologia

1. Usare il comando seguente per copiare la topologia nel cluster HDInsight. Sostituire **USER** con il nome utente SSH usato durante la creazione del cluster. Sostituire **CLUSTERNAME** con il nome del cluster.
   
        scp target\StormToDataLakeStore-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToDataLakeStore-1.0-SNAPSHOT.jar
   
    Quando richiesto, immettere la password usata durante la creazione dell'utente SSH per il cluster. Se è stata usata una chiave pubblica anziché una password, può essere necessario usare il parametro `-i` per specificare il percorso della chiave privata corrispondente.
   
   > [!NOTE]
   > Se si sta usando un client Windows per lo sviluppo, potrebbe non essere disponibile un comando `scp`. In tal caso, è possibile usare `pscp`, disponibile nella pagina [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

2. Al termine del caricamento, usare il comando seguente per connettersi al cluster HDInsight tramite SSH. Sostituire **USER** con il nome utente SSH usato durante la creazione del cluster. Sostituire **CLUSTERNAME** con il nome del cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Quando richiesto, immettere la password usata durante la creazione dell'utente SSH per il cluster. Se è stata usata una chiave pubblica anziché una password, può essere necessario usare il parametro `-i` per specificare il percorso della chiave privata corrispondente.
   
   Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Dopo la connessione, usare il comando seguente per creare un file denominato `dev.properties`:

        nano dev.properties

4. Usare il testo seguente come contenuto del file `dev.properties`:

        hdfs.write.dir: /stormdata
        hdfs.url: adl:///
    
    Per salvare il file, usare __CTRL + X__, poi __Y__e infine premere __Invio__. I valori in questo file impostano l'URL di Data Lake Store e il nome della directory in cui vengono scritti i dati.

3. Per avviare la topologia, usare il comando seguente:
   
        storm jar StormToDataLakeStore-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /datalakewriter.yaml --filter dev.properties

    Questo comando avvia la topologia usando il framework Flux. La topologia viene definita mediante il file `datalakewriter.yaml` incluso nel file con estensione jar. Il file `dev.properties` viene passato come filtro e la topologia legge i dati contenuti nel file.

## <a name="view-output-data"></a>Visualizzare i dati di output

Per visualizzare i dati, usare il comando seguente:

    hdfs dfs -ls /stormdata/

Ciò consente di visualizzare un elenco di file creati dalla topologia.

Se Data Lake Store non è l'archivio predefinito per il cluster, usare il comando seguente per visualizzare i dati:

    hdfs dfs -ls adl://MYDATALAKE.azuredatalakestore.net/stormdata/

Nel comando precedente sostituire __MYDATALAKE__ con l'account Data Lake Store.

Nell'elenco seguente è riportato un esempio dei dati restituiti dai comandi precedenti:

    Found 30 items
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-13-1488568412603.txt
    -rw-r-----+  1 larryfr larryfr       5120 2017-03-03 19:13 /stormdata/hdfs-bolt-3-14-1488568415055.txt

## <a name="stop-the-topology"></a>Arrestare la topologia

Le topologie Storm vengono eseguite fino a quando non vengono arrestate o non viene eliminato il cluster. Per arrestare le topologie, usare le informazioni riportate di seguito:

**Per HDInsight basato su Linux**:

Da una sessione SSH al cluster, usare il comando seguente:

    storm kill datalakewriter

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare Storm per scrivere nell'Archivio Azure Data Lake, è possibile vedere altri [esempi di Storm per HDInsight](hdinsight-storm-example-topology.md).


