---
title: Apache Storm con componenti Python - Azure HDInsight
description: Informazioni su come creare una topologia Apache Storm che usa componenti Python.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm python
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: hrasheed
ms.openlocfilehash: 3b808a12c7a669a272bf6a1bbb253ed6b4625288
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67078203"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Sviluppare topologie Apache Storm con Python in HDInsight

Informazioni su come creare una topologia [Apache Storm](https://storm.apache.org/) che usa componenti Python. Apache Storm supporta più linguaggi, consentendo di combinare in un'unica topologia componenti in più lingue. Il framework [Flux](https://storm.apache.org/releases/current/flux.html), introdotto con Storm 0.10.0, consente di creare facilmente soluzioni che usino i componenti Python.

> [!IMPORTANT]  
> Le informazioni contenute in questo documento sono state testate usando Storm in HDInsight 3.6. 

Il codice per questo progetto è disponibile presso il sito [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount).

## <a name="prerequisites"></a>Prerequisiti

* Python 2.7 o versione successiva

* Java JDK 1.8 o versione successiva

* [Apache Maven 3](https://maven.apache.org/download.cgi)

* (Facoltativo) Un ambiente locale di sviluppo Storm. L'ambiente locale di Storm è necessario solo se si vuole eseguire la topologia in locale. Per altre informazioni, vedere [Setting up a development environment](https://storm.apache.org/releases/1.1.2/Setting-up-development-environment.html) (Impostazione di un ambiente di sviluppo).

## <a name="storm-multi-language-support"></a>Supporto per più linguaggi in Storm

Apache Storm è stato progettato per funzionare con componenti scritti con qualsiasi linguaggio di programmazione. I componenti devono poter lavorare con la [definizione Thrift per Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Per Python viene fornito un modulo, come parte del progetto Apache Storm, che consente di interfacciarsi facilmente con Storm. Questo modulo è disponibile presso il sito [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Storm è un processo Java che viene eseguito su Java Virtual Machine (JVM). I componenti scritti in altri linguaggi vengono eseguiti come sottoprocessi. Storm comunica con tali sottoprocessi tramite messaggi JSON inviati tramite stdin/stdout. Altri dettagli sulla comunicazione tra i componenti sono disponibili nella documentazione relativa al [protocollo Multi-lang](https://storm.apache.org/documentation/Multilang-protocol.html) .

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

Come accennato in precedenza, un file `storm.py` implementa la definizione Thrift per Storm. Il framework Flux include automaticamente `storm.py` quando viene compilato il progetto, quindi non è necessario preoccuparsi di includerlo manualmente.

## <a name="build-the-project"></a>Compilare il progetto

Dalla radice del progetto, usare il comando seguente:

```bash
mvn clean compile package
```

Tale comando crea un file `target/WordCount-1.0-SNAPSHOT.jar` che contiene la topologia compilata.

## <a name="run-the-topology-locally"></a>Eseguire la topologia in locale

Per eseguire la topologia in locale, usare il comando seguente:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> Tale comando richiede un ambiente locale di sviluppo Storm. Per altre informazioni, vedere [Setting up a development environment](https://storm.apache.org/releases/current/Setting-up-development-environment.html) (Impostazione di un ambiente di sviluppo)

Una volta avviata, la topologia emette informazioni alla console locale simili al testo seguente:


    24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
    24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
    24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
    24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
    24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
    24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}


Per arrestare la topologia, usare __CTRL+C__.

## <a name="run-the-storm-topology-on-hdinsight"></a>Eseguire la topologia Storm in HDInsight

1. Usare il comando seguente per copiare il file `WordCount-1.0-SNAPSHOT.jar` nel cluster Storm in HDInsight:

    ```bash
    scp target\WordCount-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net
    ```

    Sostituire `sshuser` con l'utente SSH del cluster. Sostituire `mycluster` con il nome del cluster. È possibile che venga richiesto di immettere la password per l'utente SSH.

    Per altre informazioni sull'uso di SSH e SCP, vedere [Connettersi a HDInsight (Hadoop) con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Una volta completato il caricamento del file, connettersi al cluster tramite SSH:

    ```bash
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Nella sessione SSH usare il comando seguente per avviare la topologia nel cluster:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

3. Per visualizzare la topologia nel cluster, è possibile usare l'interfaccia utente di Storm. L'interfaccia utente Storm si trova all'indirizzo https://mycluster.azurehdinsight.net/stormui. Sostituire `mycluster` con il nome del cluster.

> [!NOTE]  
> Una volta avviata, l'esecuzione di una topologia Storm procede fino a quando non viene arrestata. Per arrestare la topologia, è possibile usare uno dei metodi seguenti:
>
> * Il comando `storm kill TOPOLOGYNAME` dalla riga di comando
> * Il pulsante **Termina** nell'interfaccia utente di Storm.


## <a name="next-steps"></a>Passaggi successivi

Vedere i documenti seguenti per altri modi di usare Python con HDInsight:

* [Come usare funzioni definite dall'utente Python in Apache Pig e Apache Hive](../hadoop/python-udf-hdinsight.md)
