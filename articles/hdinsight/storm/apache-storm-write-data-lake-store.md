---
title: Scrittura in un archivio con Apache Storm/Data Lake Store - Azure HDInsight | Microsoft Docs
description: Informazioni su come usare Apache Storm per eseguire operazioni di scrittura in un archivio compatibile con HDFS per HDInsight. Archiviazione di Azure o Azure Data Lake Store offre un'archiviazione compatibile con HDFS per HDInsight. Questo documento e il relativo esempio dimostrano come usare il componente HdfsBolt per scrivere nell'archivio predefinito di uno Storm in un cluster HDInsight.
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
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 908a1130171cc091b4d95d4532cc3bb95edb44f9
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="write-to-hdfs-from-apache-storm-on-hdinsight"></a>Scrivere da Apache Storm a HDFS in HDInsight

Informazioni su come usare Storm per scrivere dati in un archivio compatibile con HDFS usato da Apache Storm in HDInsight. HDInsight può usare sia Archiviazione di Azure che Azure Data Lake Store come archivio compatibile con HDFS. Storm offre un componente [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) che scrive i dati in HDFS. Questo documento contiene informazioni su come eseguire operazioni di scrittura da HdfsBolt in entrambi gli archivi. 

> [!IMPORTANT]
> La topologia di esempio usata in questo documento si basa su componenti inclusi con Storm in HDInsight. Può essere necessario apportare delle modifiche per usare Azure Data Lake Store con altri cluster Apache Storm.

## <a name="get-the-code"></a>Ottenere il codice

Il progetto contenente questa topologia è disponibile per il download da [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store).

Per compilare questo progetto, è necessaria la seguente configurazione per l'ambiente di sviluppo:

* [Java JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) o versione successiva. Per HDInsight 3.5 o una versione successiva è necessario Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* `JAVA_HOME` - deve puntare alla directory dove è installato JDK.
* `PATH`: deve contenere i percorsi seguenti:
  
    * `JAVA_HOME` o il percorso equivalente.
    * `JAVA_HOME\bin` o il percorso equivalente.
    * Directory in cui è installato Maven.

## <a name="how-to-use-the-hdfsbolt-with-hdinsight"></a>Procedura per usare HdfsBolt con HDInsight

> [!IMPORTANT]
> Prima di usare HdfsBolt con Storm in HDInsight, è necessario usare l'azione script per copiare i file .jar richiesti in `extpath` per Storm. Per altre informazioni, vedere la sezione [Configurare il cluster](#configure).

HdfsBolt usa lo schema file specificato dall'utente per capire come scrivere in HDFS. Per HDInsight, usare uno dei seguenti schemi:

* `wasb://`: usato con l'account di archiviazione di Azure.
* `adl://`: usato con Azure Data Lake Store.

La seguente tabella riporta esempi di utilizzo dello schema di file in diversi scenari:

| Schema | Note |
| ----- | ----- |
| `wasb:///` | L'account di archiviazione predefinito è un contenitore BLOB nell'account di archiviazione di Azure. |
| `adl:///` | L'account di archiviazione predefinito è una directory in Azure Data Lake Store. Durante la creazione del cluster, si specifica la directory in Data Lake Store che rappresenta la radice dell'HDFS del cluster. Ad esempio, la directory `/clusters/myclustername/`. |
| `wasb://CONTAINER@ACCOUNT.blob.core.windows.net/` | Un account di archiviazione di Azure non predefinito (aggiuntivo) associato al cluster. |
| `adl://STORENAME/` | La radice di Data Lake Store usata dal cluster. Questo schema permette di accedere ai dati situati all'esterno della directory che contiene il file system del cluster. |

Per altre informazioni, vedere il riferimento [HdfsBolt](http://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) su Apache.org.

### <a name="example-configuration"></a>Configurazione di esempio

Il seguente YAML è un estratto del file `resources/writetohdfs.yaml` incluso nell'esempio. Questo file definisce la topologia di Storm usando il framework [Flux](https://storm.apache.org/releases/1.1.0/flux.html) per Apache Storm.

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

Per altre informazioni sul framework Flux, vedere [https://storm.apache.org/releases/1.1.0/flux.html](https://storm.apache.org/releases/1.1.0/flux.html).

## <a name="configure-the-cluster"></a>Configurare il cluster

Per impostazione predefinita, Storm in HDInsight non include i componenti che HdfsBolt usa per comunicare con Archiviazione di Azure o Data Lake Store nel classpath di Storm. Usare la seguente azione script per aggiungere questi componenti alla directory `extlib` per Storm nel cluster:

* URI script: `https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`
* Nodi da applicare a: Nimbus, Supervisor
* Parametri: nessuno

Per informazioni sull'uso di questo script con il cluster, vedere il documento [Customize HDInsight clusters using script actions](./../hdinsight-hadoop-customize-cluster-linux.md) (Personalizzare i cluster HDInsight con le azioni script).

## <a name="build-and-package-the-topology"></a>Compilare e creare il pacchetto della topologia

1. Scaricare il progetto di esempio da [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store ](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) nell'ambiente di sviluppo.

2. Da un prompt dei comandi, un terminale o una sessione shell, modificare le directory impostandole sulla directory principale del progetto scaricato. Per compilare e creare la topologia, usare il seguente comando:
   
        mvn compile package
   
    Al termine della compilazione e creazione, ci sarà una nuova directory denominata `target`, che contiene un file denominato `StormToHdfs-1.0-SNAPSHOT.jar`. Questo file contiene la topologia compilata.

## <a name="deploy-and-run-the-topology"></a>Distribuire ed eseguire la topologia

1. Usare il comando seguente per copiare la topologia nel cluster HDInsight. Sostituire **USER** con il nome utente SSH usato durante la creazione del cluster. Sostituire **CLUSTERNAME** con il nome del cluster.
   
        scp target\StormToHdfs-1.0-SNAPSHOT.jar USER@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
   
    Quando richiesto, immettere la password usata durante la creazione dell'utente SSH per il cluster. Se è stata usata una chiave pubblica anziché una password, può essere necessario usare il parametro `-i` per specificare il percorso della chiave privata corrispondente.
   
   > [!NOTE]
   > Per altre informazioni sull'uso di `scp` con HDInsight, vedere [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md) (Uso di SSH con HDInsight).

2. Al termine del caricamento, usare il comando seguente per connettersi al cluster HDInsight tramite SSH. Sostituire **USER** con il nome utente SSH usato durante la creazione del cluster. Sostituire **CLUSTERNAME** con il nome del cluster.
   
        ssh USER@CLUSTERNAME-ssh.azurehdinsight.net
   
    Quando richiesto, immettere la password usata durante la creazione dell'utente SSH per il cluster. Se è stata usata una chiave pubblica anziché una password, può essere necessario usare il parametro `-i` per specificare il percorso della chiave privata corrispondente.
   
   Per altre informazioni, vedere [Usare SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

3. Dopo la connessione, usare il comando seguente per creare un file denominato `dev.properties`:

        nano dev.properties

4. Usare il testo seguente come contenuto del file `dev.properties`:

        hdfs.write.dir: /stormdata/
        hdfs.url: wasb:///

    > [!IMPORTANT]
    > In questo esempio si presuppone che il cluster usi un account di archiviazione di Azure come archivio predefinito. Se il cluster usa Azure Data Lake Store, usare invece `hdfs.url: adl:///`.
    
    Per salvare il file, usare __CTRL + X__, poi __Y__e infine premere __Invio__. I valori in questo file impostano l'URL di Data Lake Store e il nome della directory in cui vengono scritti i dati.

3. Per avviare la topologia, usare il comando seguente:
   
        storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties

    Questo comando avvia la topologia usando il framework Flux, inviandolo al nodo Nimbus del cluster. La topologia viene definita mediante il file `writetohdfs.yaml` incluso nel file con estensione jar. Il file `dev.properties` viene passato come filtro e la topologia legge i dati contenuti nel file.

## <a name="view-output-data"></a>Visualizzare i dati di output

Per visualizzare i dati, usare il comando seguente:

    hdfs dfs -ls /stormdata/

Viene visualizzato un elenco dei file creati dalla topologia.

Nell'elenco seguente è riportato un esempio dei dati restituiti dai comandi precedenti:

    Found 30 items
    -rw-r-----+  1 sshuser sshuser       488000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-0-1488568403092.txt
    -rw-r-----+  1 sshuser sshuser       444000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-1-1488568404567.txt
    -rw-r-----+  1 sshuser sshuser       502000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-10-1488568408678.txt
    -rw-r-----+  1 sshuser sshuser       582000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-11-1488568411636.txt
    -rw-r-----+  1 sshuser sshuser       464000 2017-03-03 19:13 /stormdata/hdfs-bolt-3-12-1488568411884.txt

## <a name="stop-the-topology"></a>Arrestare la topologia

Le topologie Storm vengono eseguite fino all'arresto o all'eliminazione del cluster. Per arrestare la topologia, usare il seguente comando:

    storm kill hdfswriter

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso come usare Storm per scrivere in Archiviazione di Azure e Azure Data Lake Store, è possibile vedere altri [esempi di Storm per HDInsight](apache-storm-example-topology.md).

