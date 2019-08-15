---
title: 'Esercitazione: Usare Apache Storm per eseguire operazioni di scrittura in Archiviazione di Azure o Data Lake Storage - Azure HDInsight'
description: 'Esercitazione: Informazioni su come usare Apache Storm per eseguire operazioni di scrittura in un archivio compatibile con HDFS per Azure HDInsight.'
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 17cb1091d34c8c0800d0b4dd1f9044fee0ef313f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946445"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Esercitazione: Eseguire operazioni di scrittura in Apache Hadoop HDFS da Apache Storm in HDInsight

Questa esercitazione illustra come usare Apache Storm per scrivere dati nell'archivio compatibile con HDFS usato da Apache Storm in HDInsight. HDInsight può usare sia Archiviazione di Azure che Azure Data Lake Storage come archivio compatibile con HDFS. Storm offre un componente [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) che scrive i dati in HDFS. Questo documento contiene informazioni su come eseguire operazioni di scrittura da HdfsBolt in entrambi gli archivi.

La topologia di esempio usata in questo documento si basa su componenti inclusi con Storm in HDInsight. Può essere necessario apportare delle modifiche per usare Azure Data Lake Storage con altri cluster Apache Storm.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare il cluster con un'azione script
> * Compilare e creare il pacchetto della topologia
> * Distribuire ed eseguire la topologia
> * Visualizzare i dati di output
> * Arrestare la topologia

## <a name="prerequisites"></a>Prerequisiti

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks)

* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.

* Un client SSH. Per altre informazioni, vedere [Connettersi a HDInsight (Apache Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Lo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) per l'archiviazione primaria dei cluster. Corrisponde a `wasb://` per Archiviazione di Azure, a `abfs://` per Azure Data Lake Storage Gen2 e a `adl://` per Azure Data Lake Storage Gen1. Se il trasferimento sicuro è abilitato per Archiviazione di Azure o Data Lake Storage Gen2, l'URI è rispettivamente `wasbs://` o `abfss://`. Vedere anche [trasferimento sicuro](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Configurazione di esempio

Il seguente YAML è un estratto del file `resources/writetohdfs.yaml` incluso nell'esempio. Questo file definisce la topologia di Storm usando il framework [Flux](https://storm.apache.org/releases/current/flux.html) per Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

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

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


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
```

Questo YAML definisce i seguenti elementi:

* `syncPolicy`: specifica quando i file vengono sincronizzati/allineati con il file system. In questo esempio, ogni 1000 tuple.
* `fileNameFormat`: specifica il modello di percorso e nome del file da usare per la scrittura di file. In questo esempio, il percorso viene fornito in fase di runtime usando un filtro e l'estensione è `.txt`.
* `recordFormat`: specifica il formato interno dei file scritti. In questo esempio, i campi sono delimitati dal carattere `|`.
* `rotationPolicy`: specifica quando ruotare i file. In questo esempio, non viene eseguita alcuna rotazione.
* `hdfs-bolt`: usa i componenti precedenti come parametri di configurazione per la classe `HdfsBolt`.

Per altre informazioni sul framework Flux, vedere [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html).

## <a name="configure-the-cluster"></a>Configurare il cluster

Per impostazione predefinita, Storm in HDInsight non include i componenti usati da `HdfsBolt` per comunicare con Archiviazione di Azure o Data Lake Storage nel classpath di Storm. Usare la seguente azione script per aggiungere questi componenti alla directory `extlib` per Storm nel cluster:

| Proprietà | Valore |
|---|---|
|Tipo di script |- Personalizzato|
|URI script Bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Tipo/i di nodo |Nimbus, Supervisor|
|Parametri |Nessuna|

Per informazioni sull'uso di questo script con il cluster, vedere il documento [Customize HDInsight clusters using script actions](./../hdinsight-hadoop-customize-cluster-linux.md) (Personalizzare i cluster HDInsight con le azioni script).

## <a name="build-and-package-the-topology"></a>Compilare e creare il pacchetto della topologia

1. Scaricare il progetto di esempio da [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) nell'ambiente di sviluppo in uso.

2. Da un prompt dei comandi, un terminale o una sessione shell, modificare le directory impostandole sulla directory principale del progetto scaricato. Per compilare e creare la topologia, usare il seguente comando:

    ```cmd
    mvn compile package
    ```

    Al termine della compilazione e creazione, ci sarà una nuova directory denominata `target`, che contiene un file denominato `StormToHdfs-1.0-SNAPSHOT.jar`. Questo file contiene la topologia compilata.

## <a name="deploy-and-run-the-topology"></a>Distribuire ed eseguire la topologia

1. Usare il comando seguente per copiare la topologia nel cluster HDInsight. Sostituire `CLUSTERNAME` con il nome del cluster.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Al termine del caricamento, usare il comando seguente per connettersi al cluster HDInsight tramite SSH. Sostituire `CLUSTERNAME` con il nome del cluster.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Dopo la connessione, usare il comando seguente per creare un file denominato `dev.properties`:

    ```bash
    nano dev.properties
    ```

1. Usare il testo seguente come contenuto del file `dev.properties`. Eseguire una revisione a seconda della necessità in base allo [schema URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Per salvare il file, usare __CTRL + X__, poi __Y__e infine premere __Invio__. I valori in questo file impostano l'URL di archiviazione e il nome della directory in cui vengono scritti i dati.

1. Per avviare la topologia, usare il comando seguente:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    Questo comando avvia la topologia usando il framework Flux, inviandolo al nodo Nimbus del cluster. La topologia viene definita mediante il file `writetohdfs.yaml` incluso nel file con estensione jar. Il file `dev.properties` viene passato come filtro e la topologia legge i dati contenuti nel file.

## <a name="view-output-data"></a>Visualizzare i dati di output

Per visualizzare i dati, usare il comando seguente:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Viene visualizzato un elenco dei file creati dalla topologia. Nell'elenco seguente è riportato un esempio dei dati restituiti dai comandi precedenti:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Arrestare la topologia

Le topologie Storm vengono eseguite fino all'arresto o all'eliminazione del cluster. Per arrestare la topologia, usare il seguente comando:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa esercitazione, eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Apache Storm per scrivere dati nell'archivio compatibile con HDFS usato da Apache Storm in HDInsight.

> [!div class="nextstepaction"]
> Vedere altri [esempi su Apache Storm per HDInsight](apache-storm-example-topology.md)