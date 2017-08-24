---
title: Elaborare eventi di Hub eventi con Storm in HDInsight usando Java| Documentazione Microsoft
description: Informazioni su come elaborare i dati dell'hub eventi con una topologia Storm Java creata con Maven.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/13/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2e8ebbdab2be7bed224a67facec798820615bb22
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)

Informazioni su come usare l'hub eventi di Azure con Storm in HDInsight. Questo esempio usa componenti basati su Java per leggere e scrivere dati nell'hub eventi di Azure.

L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per analizzare questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm usando il relativo bolt.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Storm in HDInsight versione 3.6. Per altre informazioni, vedere [Introduzione a Storm in un cluster HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

    > [!IMPORTANT]
    > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Oracle Java Developer Kit (JDK) versione 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o equivalente, ad esempio [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): Maven è un sistema di compilazione per progetti Java.

* Un editor di testo o un IDE (Integrated Development Environment).

    > [!NOTE]
    > È possibile che l'editor di testo o l'ambiente IDE offra funzionalità specifiche per l'uso di Maven non descritte in questo documento. Per informazioni sulle funzionalità dell'ambiente di modifica, vedere la documentazione del prodotto usato.

    * Un client SSH. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Comandi `ssh` e `scp`. Questi vengono usati per copiare i file nel cluster HDInsight. In Windows, è possibile ottenerli con Bash in Windows 10.

## <a name="understanding-the-example"></a>Informazioni sull'esempio

L'esempio [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) include due topologie:

La topologia `resources/writer.yaml` scrive dati casuali in un hub di eventi di Azure. I dati vengono generati dal componente `DeviceSpout` e sono costituiti da un ID dispositivo casuale e da un valore del dispositivo. Simula quindi un dispositivo hardware che genera un ID stringa e un valore numerico.

La topologia `resources/reader.yaml` legge i dati provenienti dall'Hub di eventi, ovvero i dati scritti da EventHubWriter, analizza i dati JSON e quindi registra i dati `deviceId` e `deviceValue`.

I dati vengono formattati come documento JSON prima di essere scritti nell'hub eventi e quando vengono letti dal lettore, vengono analizzati da JSON e inseriti in tuple. Il formato JSON è il seguente:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configurazione del progetto

Il file `POM.xml` contiene informazioni di configurazione per questo progetto Maven. Ecco gli aspetti interessanti:

#### <a name="event-hub-components"></a>Componenti di Hub eventi

Il componente che legge e scrive in Hub di eventi di Azure si trova nel [repository HDInsight](https://github.com/hdinsight/mvn-rep). Le sezioni seguenti nel file `POM.xml` caricano i componenti da questo repository

```xml
<repositories>
    <repository>
        <id>hdinsight-examples</id>
        <url>http://raw.github.com/hdinsight/mvn-repo/master</url>
    </repository>
</repositories>
```

#### <a name="the-eventhubs-storm-spout-dependency"></a>Dipendenza di spout Storm in EventHubs

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>${storm.eventhub.version}</version>
</dependency>
```

Questo XML definisce una dipendenza per il pacchetto eventhubs che contiene sia uno spout per la lettura dall'hub eventi sia un bolt per la scrittura nell'hub eventi.

```xml
</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Questo file XML consente di configurare il progetto per generare l'output per Java 8, che viene usato da HDInsight 3.5 o una versione successiva.

#### <a name="the-maven-shade-plugin"></a>Maven-shade-plugin

```xml
<!-- build an uber jar -->
<plugin>
<groupId>org.apache.maven.plugins</groupId>
<artifactId>maven-shade-plugin</artifactId>
<version>2.3</version>
<configuration>
    <transformers>
    <!-- Keep us from getting a can't overwrite file error -->
    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer"/>
    <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer"/>
    </transformers>
    <!-- Keep us from getting a bad signature error -->
    <filters>
    <filter>
        <artifact>*:*</artifact>
        <excludes>
            <exclude>META-INF/*.SF</exclude>
            <exclude>META-INF/*.DSA</exclude>
            <exclude>META-INF/*.RSA</exclude>
        </excludes>
    </filter>
    </filters>
</configuration>
<executions>
    <execution>
    <phase>package</phase>
    <goals>
        <goal>shade</goal>
    </goals>
    </execution>
</executions>
</plugin>
```

Questo file XML consente di configurare la soluzione per impacchettare l'output in un file uber jar. Il file jar contiene sia il codice del progetto sia le dipendenze necessarie. Viene usato anche per:

* Rinominare i file di licenza per le dipendenze.
* Escludere sicurezza/firme.
* Verificare che più implementazioni della stessa interfaccia vengano unite in un'unica voce.

Queste impostazioni di configurazione impediscono il verificarsi di errori in fase di runtime.

#### <a name="topology-definitions"></a>Definizioni di topologia

Questo esempio usa il framework [Flux](https://storm.apache.org/releases/1.1.0/flux.html), il quale usa YAML per definire le topologie. Il vantaggio principale è che non è necessaria una codifica complessa per la topologia nel codice Java. Poiché la definizione è YAML, è possibile modificarla prima di inviare la topologia, senza dover ricompilare tutti gli elementi.

__writer.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubwriter"

components:
  # Configure the Event Hub spout
  - id: "eventhubbolt-config"
    className: "org.apache.storm.eventhubs.bolt.EventHubBoltConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.write.policy.name}"
      - "${eventhub.write.policy.key}"
      - "${eventhub.namespace}"
      - "servicebus.windows.net"
      - "${eventhub.name}"

spouts:
  - id: "device-emulator-spout"
    className: "com.microsoft.example.DeviceSpout"
    parallelism: ${eventhub.partitions}

bolts:
  - id: "eventhub-bolt"
    className: "org.apache.storm.eventhubs.bolt.EventHubBolt"
    constructorArgs:
      - ref: "eventhubbolt-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

# How data flows through the components
streams:
  - name: "spout -> eventhub" # just a string used for logging
    from: "device-emulator-spout"
    to: "eventhub-bolt"
    grouping:
        type: SHUFFLE

  - name: "spout -> logger"
    from: "device-emulator-spout"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

__reader.yaml__:

```yaml
---
# Topology that reads from Event Hubs
name: "eventhubreader"

components:
  # Configure the Event Hub spout
  - id: "eventhubspout-config"
    className: "org.apache.storm.eventhubs.spout.EventHubSpoutConfig"
    constructorArgs:
      # These are populated from the .properties file when the topology is started
      - "${eventhub.read.policy.name}"
      - "${eventhub.read.policy.key}"
      - "${eventhub.namespace}"
      - "${eventhub.name}"
      - ${eventhub.partitions}

spouts:
  - id: "eventhub-spout"
    className: "org.apache.storm.eventhubs.spout.EventHubSpout"
    constructorArgs:
      - ref: "eventhubspout-config" # config declared in components section
    # parallelism hint. This should be the same as the number of partitions for your Event Hub, so we read it from the dev.properties file passed at run time.
    parallelism: ${eventhub.partitions}

bolts:
  # Log information
  - id: "log-bolt"
    className: "org.apache.storm.flux.wrappers.bolts.LogInfoBolt"
    parallelism: 1

  # Parses from JSON into tuples
  - id: "parser-bolt"
    className: "com.microsoft.example.ParserBolt"
    parallelism: ${eventhub.partitions}

# How data flows through the components
streams:
  - name: "spout -> parser" # just a string used for logging
    from: "eventhub-spout"
    to: "parser-bolt"
    grouping:
        type: SHUFFLE

  - name: "parser -> log-bolt"
    from: "parser-bolt"
    to: "log-bolt"
    grouping:
        type: SHUFFLE
```

#### <a name="tell-the-topology-about-event-hub"></a>Informare la topologia sull'hub eventi

In fase di esecuzione il file `dev.properties` viene usato per passare la configurazione di Hub di eventi nella topologia. I contenuti predefiniti del file sono analoghi a quelli dell'esempio seguente:

```yaml
eventhub.write.policy.name: writer
eventhub.write.policy.key: your_key_here
eventhub.read.policy.name: reader
eventhub.read.policy.key: your_key_here
eventhub.namespace: your_namespace_here
eventhub.name: storm
eventhub.partitions: 2
```

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** : deve contenere i percorsi seguenti:

  * **JAVA_HOME** o il percorso equivalente
  * **JAVA_HOME\bin** o il percorso equivalente
  * Directory in cui è installato Maven

## <a name="configure-event-hub"></a>Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un hub eventi, seguire questa procedura.

1. Nel [Portale di Azure classico](https://manage.windowsazure.com) selezionare **NUOVO** > **Bus di servizio** > **Hub eventi** > **Creazione personalizzata**.

2. Nella schermata **Crea un nuovo hub eventi** immettere un **Nome hub eventi**. Selezionare l'**Area** in cui creare l'hub e quindi creare uno spazio dei nomi o selezionarne uno esistente. Infine fare clic sulla **freccia** per continuare.

    ![procedura guidata - pagina 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

   > [!NOTE]
   > Selezionare lo stesso **Percorso** di Storm nel server HDInsight per ridurre la latenza e i costi.

3. Nella schermata **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.

    ![procedura guidata - pagina 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

4. Dopo aver creato l'hub eventi, selezionare lo spazio dei nomi, selezionare **Hub eventi**, quindi selezionare l'hub eventi creato in precedenza.
5. Selezionare **Configura** e quindi creare due nuovi criteri di accesso usando le informazioni seguenti:

    <table>
    <tr><th>Nome</th><th>Autorizzazioni</th></tr>
    <tr><td>Writer</td><td>Invio</td></tr>
    <tr><td>Reader</td><td>Attesa</td></tr>
    </table>

    Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Questi criteri di accesso condiviso vengono usati per leggere e scrivere nell'hub eventi.

    ![criteri](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

6. Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** nella parte inferiore della pagina per recuperare la chiave per i criteri **writer** e **reader**. Salvare queste chiavi.

## <a name="download-and-build-the-project"></a>Scaricare e compilare il progetto

1. Scaricare il progetto da GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). È possibile scaricare il pacchetto come archivio ZIP o usare [git](https://git-scm.com/) per clonare il progetto in locale.

2. Modificare il file `dev.properties` con la configurazione per l'hub eventi.

3. Usare il comando seguente per compilare il progetto e creare il pacchetto:

        mvn package

    Questo comando scarica le dipendenze necessarie, compila il progetto e quindi crea il pacchetto. L'output viene archiviato nella directory **/target** come **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="test-locally"></a>Test in locale

Poiché queste topologie possono solo leggere e scrivere gli hub eventi, è possibile eseguirne il test in locale se si dispone di un [ambiente di sviluppo Storm](http://storm.apache.org/releases/current/Setting-up-development-environment.html). Seguire la procedura seguente per eseguire in locale nell'ambiente di sviluppo:

1. Eseguire il writer:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /writer.yaml --filter dev.properties

2. Eseguire il reader:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /reader.yaml --filter dev.properties

> [!TIP]
> * `--local`: consente di eseguire la topologia in modalità locale, non distribuita.
> * `-R /writer.yaml`: consente di caricare la definizione di topologia da `resources` sotto forma di file con estensione JAR. Se la topologia è un file nel file system locale, specificare il percorso come ultimo parametro.
> * `--filter dev.properties`: consente di usare il contenuto di `dev.properties` per inserire i valori nelle definizioni di topologia. Ad esempio: `${eventhub.read.policy.name}`.

L'output viene registrato nella console durante l'esecuzione in locale. Per arrestare la topologia, usare __CTRL+C__.

## <a name="deploy-the-topologies"></a>Distribuire le topologie

1. Usare SCP per copiare il pacchetto JAR nel cluster HDInsight. Sostituire USERNAME con l'utente SSH del cluster. Sostituire CLUSTERNAME con il nome del cluster HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Se è stata usata una password per l'account SSH, viene richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Ad esempio: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`

    Questo comando copia il file nella home directory dell'utente SSH nel cluster.

2. Una volta completato il caricamento del file, usare SSH per connettersi al cluster HDInsight: Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [!NOTE]
    > Se è stata usata una password per l'account SSH, viene richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata viene caricata da `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

3. Per avviare le topologie, usare il comando seguente:

        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
        storm jar EventHubExample-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties

    > [!TIP]
    > * `--remote`: consente di inviare la topologia al servizio Nimbus, che lo avvia sui nodi di lavoro nel cluster.

4. Per visualizzare i dati registrati, passare a https://CLUSTERNAME.azurehdinsight.net/stormui, dove __CLUSTERNAME__ è il nome del cluster HDInsight. Selezionare le topologie ed eseguire il drill-down per i componenti. Selezionare la voce __port__ per un'istanza di un componente e visualizzare le informazioni registrate.

5. Per arrestare le topologie, usare i comandi seguenti:

        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Passaggi successivi

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

