---
title: Apache Storm con i componenti Python-Azure HDInsight
description: Informazioni su come creare una topologia di Apache Storm che usa componenti Python in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/16/2019
ms.openlocfilehash: 20e4827b1a86bff338646ef71f0dd732255c09c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77460025"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Sviluppare topologie Apache Storm con Python in HDInsight

Informazioni su come creare una topologia [Apache Storm](https://storm.apache.org/) che usa i componenti Python. Apache Storm supporta più linguaggi, consentendo di combinare in un'unica topologia componenti in più lingue. Il Framework [Flux](https://storm.apache.org/releases/current/flux.html) (introdotto con Storm 0.10.0) consente di creare facilmente soluzioni che usano i componenti Python.

> [!IMPORTANT]  
> Le informazioni contenute in questo documento sono state testate usando Storm in HDInsight 3.6.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Storm in HDInsight. Consultare [Creare cluster di Apache Hadoop usando il portale di Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selezionare **Storm** per **Tipo di cluster**.

* Un ambiente di sviluppo Storm locale (facoltativo). L'ambiente locale di Storm è necessario solo se si vuole eseguire la topologia in locale. Per altre informazioni, vedere [Setting up a development environment](https://storm.apache.org/releases/current/Setting-up-development-environment.html) (Impostazione di un ambiente di sviluppo).

* [Python 2,7 o versione successiva](https://www.python.org/downloads/).

* [Java Developer Kit (JDK) versione 8](https://aka.ms/azure-jdks).

* [Apache Maven](https://maven.apache.org/download.cgi) correttamente [installato](https://maven.apache.org/install.html) in base alle indicazioni di Apache.  Maven è un sistema di compilazione per progetti Java.

## <a name="storm-multi-language-support"></a>Supporto per più linguaggi in Storm

Apache Storm è stato progettato per funzionare con componenti scritti con qualsiasi linguaggio di programmazione. I componenti devono poter lavorare con la definizione Thrift per Storm. Per Python viene fornito un modulo, come parte del progetto Apache Storm, che consente di interfacciarsi facilmente con Storm. Questo modulo è reperibile in [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm è un processo Java che viene eseguito su Java Virtual Machine (JVM). I componenti scritti in altri linguaggi vengono eseguiti come sottoprocessi. Storm comunica con tali sottoprocessi tramite messaggi JSON inviati tramite stdin/stdout. Altri dettagli sulla comunicazione tra i componenti sono disponibili nella documentazione relativa al [protocollo Multi-lang](https://storm.apache.org/releases/current/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python con il framework Flux

Il framework Flux consente di definire topologie Storm in modo separato rispetto ai componenti. Il Flux framework usa YAML per definire la topologia Storm. Il testo seguente è un esempio di come fare riferimento a un componente Python nel documento YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

La classe `FluxShellSpout` viene usata per avviare lo script `sentencespout.py` che implementa lo spout.

Flux prevede che gli script Python siano nella directory `/resources` all'interno del file con estensione jar che contiene la topologia. Pertanto in questo esempio gli script Python vengono archiviati nella directory `/multilang/resources`. `pom.xml` include il file usando il codice XML seguente:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Come indicato in precedenza, è disponibile `storm.py` un file che implementa la definizione di risparmio per Storm. Il framework Flux include automaticamente `storm.py` quando viene compilato il progetto, quindi non è necessario preoccuparsi di includerlo manualmente.

## <a name="build-the-project"></a>Compilare il progetto

1. Scaricare il progetto da [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

1. Aprire un prompt dei comandi e passare alla radice del progetto `hdinsight-python-storm-wordcount-master`:. Immettere il comando seguente:

    ```cmd
    mvn clean compile package
    ```

    Tale comando crea un file `target/WordCount-1.0-SNAPSHOT.jar` che contiene la topologia compilata.

## <a name="run-the-storm-topology-on-hdinsight"></a>Eseguire la topologia Storm in HDInsight

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per copiare `WordCount-1.0-SNAPSHOT.jar` il file nel cluster Storm in HDInsight. Modificare il comando seguente sostituendo CLUSTERname con il nome del cluster e quindi immettere il comando:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Una volta completato il caricamento del file, connettersi al cluster tramite SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Nella sessione SSH usare il comando seguente per avviare la topologia nel cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Una volta avviata, l'esecuzione di una topologia Storm procede fino a quando non viene arrestata.

1. Usare l'interfaccia utente di Storm per visualizzare la topologia nel cluster. L'interfaccia utente Storm si trova all'indirizzo `https://CLUSTERNAME.azurehdinsight.net/stormui`. Sostituire `CLUSTERNAME` con il nome del cluster.

1. Arrestare la topologia Storm. Usare il comando seguente per arrestare la topologia nel cluster:

    ```bash
    storm kill wordcount
    ```

    In alternativa, è possibile usare l'interfaccia utente di Storm. In **azioni topologia** per la topologia selezionare **Kill**.

## <a name="run-the-topology-locally"></a>Eseguire la topologia in locale

Per eseguire la topologia in locale, usare il comando seguente:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Tale comando richiede un ambiente locale di sviluppo Storm. Per altre informazioni, vedere [Setting up a development environment](https://storm.apache.org/releases/current/Setting-up-development-environment.html) (Impostazione di un ambiente di sviluppo).

Una volta avviata, la topologia emette informazioni alla console locale simili al testo seguente:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Per arrestare la topologia, usare __CTRL+C__.

## <a name="next-steps"></a>Passaggi successivi

Per altri modi di usare Python con HDInsight, vedere i documenti seguenti: [come usare le funzioni definite dall'utente (UDF) di Python in Apache Pig e Apache hive](../hadoop/python-udf-hdinsight.md).
