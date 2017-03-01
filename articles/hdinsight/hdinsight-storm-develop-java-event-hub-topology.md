---
title: Elaborare eventi di Hub eventi con Storm in HDInsight usando Java| Documentazione Microsoft
description: Informazioni su come elaborare i dati dell&quot;hub eventi con una topologia Storm Java creata con Maven.
services: hdinsight,notification hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 453fa7b0-c8a6-413e-8747-3ac3b71bed86
ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/17/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 110f3aa9ce4848c9350ea2e560205aa762decf7a
ms.openlocfilehash: 85f56e223210f14615f7e4e1c87e35111b238aac
ms.lasthandoff: 02/21/2017


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)

Informazioni su come usare l'hub eventi di Azure con Storm in HDInsight. Questo esempio usa componenti basati su Java per leggere e scrivere dati nell'hub eventi di Azure.

L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per analizzare questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm usando il relativo bolt.

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Storm in HDInsight versione 3.5. Per altre informazioni, vedere [Introduzione a Storm in un cluster HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).
    
    > [!IMPORTANT]
    > Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere [HDInsight deprecato in Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md).

* [Oracle Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o equivalente, ad esempio [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): Maven è un sistema di compilazione per progetti Java.

* Un editor di testo o un IDE (Integrated Development Environment).
  
  > [!NOTE]
  > È possibile che l'editor di testo o l'ambiente IDE offra funzionalità specifiche per l'uso di Maven non descritte in questo documento. Per informazioni sulle funzionalità dell'ambiente di modifica, vedere la documentazione del prodotto usato.
  
  * Un client SSH. Per altre informazioni, vedere uno dei documenti seguenti:
    
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix, OS X e Bash in Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md).

    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows con PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md).

* Client SCP. Il comando `scp` viene fornito con tutti i sistemi Linux, Unix e OS X (con Bash su Windows 10). Per le versioni di Windows che non includono il comando `scp`, è consigliabile PSCP. Questa utilità è disponibile nella [pagina di download PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="understanding-the-example"></a>Informazioni sull'esempio

L'esempio [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) include due topologie:

**com.microsoft.example.EventHubWriter** scrive dati casuali in un hub eventi di Azure. I dati vengono generati da uno spout e sono costituiti da un ID dispositivo casuale e da un valore del dispositivo. Simula quindi un dispositivo hardware che genera un ID stringa e un valore numerico.

**com.microsoft.example.EventHubReader** legge i dati dall'hub eventi e li memorizza nello spazio di archiviazione predefinito dei cluster nella directory /devicedata.

I dati vengono formattati come documento JSON prima di essere scritti nell'hub eventi e quando vengono letti dal lettore, vengono analizzati da JSON e inseriti in tuple. Il formato JSON è il seguente:

    { "deviceId": "unique identifier", "deviceValue": some value }

### <a name="project-configuration"></a>Configurazione del progetto

Il file **POM.xml** contiene informazioni di configurazione per questo progetto Maven. Ecco gli aspetti interessanti:

#### <a name="the-eventhubs-storm-spout-dependency"></a>Dipendenza di spout Storm in EventHubs

```xml
<dependency>
    <groupId>com.microsoft</groupId>
    <artifactId>eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
```

Questo XML definisce una dipendenza per il pacchetto eventhubs che contiene sia uno spout per la lettura dall'hub eventi sia un bolt per la scrittura nell'hub eventi.

> [!NOTE]
> Questo pacchetto viene installato più avanti in questo documento.

#### <a name="the-hdfsbolt-and-wasb-components"></a>Componenti HdfsBolt e WASB

HdfsBolt viene usato in genere per archiviare i dati in Hadoop Distributed File System (HDFS). I cluster HDInsight usano tuttavia Azure Storage (WASB) come archivio dati predefinito, quindi è necessario caricare diversi componenti per consentire a HdfsBolt di riconoscere il file system WASB.

```xml
<!--HdfsBolt stuff -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-hdfs</artifactId>
    <exclusions>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
    </exclusion>
    <exclusion>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
    </exclusion>
    </exclusions>
    <version>${storm.version}</version>
</dependency>
<!--So HdfsBolt knows how to talk to WASB -->
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-client</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-hdfs</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-azure</artifactId>
    <version>${hadoop.version}</version>
</dependency>
<dependency>
    <groupId>org.apache.hadoop</groupId>
    <artifactId>hadoop-common</artifactId>
    <version>${hadoop.version}</version>
    <exclusions>
    <exclusion>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
    </exclusion>
    </exclusions>
</dependency>
```

> [!NOTE]
> Quando si utilizza una versione precedente di HDInsight, ad esempio la versione 3.2, è necessario registrare manualmente questi componenti. Per un esempio, vedere il ramo [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repository di esempio.

#### <a name="the-maven-compiler-plugin"></a>Maven-compiler-plugin

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <version>2.3.2</version>
    <configuration>
    <source>1.8</source>
    <target>1.8</target>
    </configuration>
</plugin>
```

Consente di configurare il progetto per generare l'output per Java 8, che viene usato da HDInsight 3.5.

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
    <!-- Keep us from getting errors when trying to use WASB from the storm-hdfs bolt -->
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

Consente di configurare la soluzione per impacchettare l'output in un file uber-jar. Il file jar contiene sia il codice del progetto sia le dipendenze necessarie. Viene usato anche per:

* Rinominare i file di licenza per le dipendenze.
* Escludere sicurezza/firme.
* Verificare che più implementazioni della stessa interfaccia vengano unite in un'unica voce.

Queste impostazioni di configurazione impediscono il verificarsi di errori in fase di runtime.

#### <a name="the-exec-maven-plugin"></a>Exec-maven-plugin

```xml
<plugin>
    <groupId>org.codehaus.mojo</groupId>
    <artifactId>exec-maven-plugin</artifactId>
    <version>1.2.1</version>
    <executions>
    <execution>
    <goals>
        <goal>exec</goal>
    </goals>
    </execution>
    </executions>
    <configuration>
    <executable>java</executable>
    <includeProjectDependencies>true</includeProjectDependencies>
    <includePluginDependencies>false</includePluginDependencies>
    <classpathScope>compile</classpathScope>
    <mainClass>${storm.topology}</mainClass>
    </configuration>
</plugin>
```

Questa configurazione semplifica l'esecuzione in locale della topologia nell'ambiente di sviluppo in uso. È possibile eseguire la topologia in locale usando la sintassi seguente:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

ad esempio `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>Sezione resources

```xml
<resources>
    <resource>
    <directory>${basedir}/conf</directory>
    <filtering>false</filtering>
    <includes>
        <include>EventHubs.properties</include>
    </includes>
    </resource>
</resources>
```

Questa configurazione definisce le risorse non Java che sono incluse nel progetto. Il file **EventHubs.properties**, ad esempio, contiene le informazioni usate per connettersi a un hub eventi di Azure.

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** : deve contenere i percorsi seguenti:
  
  * **JAVA_HOME** o il percorso equivalente
  * **JAVA_HOME\bin** o il percorso equivalente
  * Directory in cui è installato Maven

## <a name="download-and-register-the-eventhub-components"></a>Scaricare e registrare i componenti EventHub

1. Scaricare il `storm-eventhubs-1.0.2-jar-with-dependencies.jar` da [https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar](https://000aarperiscus.blob.core.windows.net/certs/storm-eventhubs-1.0.2-jar-with-dependencies.jar). Questo file contiene un componente spout per la lettura da EventHubs e un componente bolt per la scrittura in EventHubs.

2. Usare il comando seguente per registrare i componenti nel repository maven locale:
    
        mvn install:install-file -Dfile=storm-eventhubs-1.0.2-jar-with-dependencies.jar -DgroupId=com.microsoft -DartifactId=eventhubs -Dversion=1.0.2 -Dpackaging=jar
    
    Modificare il parametro `-Dfile=` in modo che punti al percorso del file scaricato.

    Questo comando installa il file nel repository Maven locale, dove può essere rilevato in fase di compilazione da Maven.

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

2. Usare il comando seguente per compilare il progetto e creare il pacchetto:
   
        mvn package
   
    Questo comando scarica le dipendenze necessarie, compila il progetto e quindi crea il pacchetto. L'output viene archiviato nella directory **/target** come **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="deploy-the-topologies"></a>Distribuire le topologie

Il file JAR creato da questo progetto contiene due topologie, **com.microsoft.example.EventHubWriter** e **com.microsoft.example.EventHubReader**. La topologia EventHubWriter dovrà essere avviata per prima, perché scrive gli eventi nell'hub eventi che vengono quindi letti da EventHubReader.

1. Usare SCP per copiare il pacchetto JAR nel cluster HDInsight. Sostituire USERNAME con l'utente SSH del cluster. Sostituire CLUSTERNAME con il nome del cluster HDInsight:
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    Se è stata usata una password per l'account SSH, viene richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Ad esempio `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.
   
   > [!NOTE]
   > Se il client è una workstation Windows, il comando SCP potrebbe non essere installato. È consigliabile usare PSCP che può essere scaricato nella [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
   
    Questo comando copia il file nella home directory dell'utente SSH nel cluster.

2. Una volta completato il caricamento del file, usare SSH per connettersi al cluster HDInsight: Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Se è stata usata una password per l'account SSH, viene richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata viene caricata da `~/.ssh/id_rsa`:
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   
    Se si usa PuTTY, immettere `CLUSTERNAME-ssh.azurehdinsight.net` nel campo **Host Name (o IP address)** e quindi fare clic su **Open** per connettersi. Viene richiesto di immettere il nome dell'account SSH.
   
   > [!NOTE]
   > Se è stata usata una password per l'account SSH, viene richiesto di immetterla. Se con l'account è stata usata una chiave SSH, potrebbe essere necessario eseguire questi passaggi per selezionare la chiave:
   > 
   > 1. In **Category** espandere **Connection**, **SSH** e selezionare **Auth**.
   > 2. Fare clic su **Browse** e selezionare il file PPK che contiene la chiave privata.
   > 3. Fare clic su **Open** per connettersi.

3. Per avviare le topologie, usare il comando seguente:
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    Questi comandi avviano le topologie usando i nomi descrittivi di "reader" e "writer".

4. Attendere un minuto perché le topologie generino i dati. Usare il comando seguente per verificare che i dati vengano scritti nell'archiviazione HDInsight:
   
        hadoop fs -ls /devicedata
   
    Questo comando restituisce un elenco di file simile al testo seguente:
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > Le dimensioni di alcuni file potrebbero essere pari a 0, perché sono stati creati da EventHubReader, ma non contengono ancora dati archiviati.
   
    Per visualizzare il contenuto di questi file, è possibile usare il comando seguente:
   
        hadoop fs -text /devicedata/*.txt
   
    Questo comando restituisce dati simili al testo seguente:
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
   
    La prima colonna contiene il valore ID del dispositivo e la seconda colonna il valore del dispositivo.

5. Per arrestare le topologie, usare i comandi seguenti:
   
        storm kill reader
        storm kill writer

## <a name="delete-your-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se non è possibile visualizzare i file nella directory /devicedata, usare l'interfaccia utente Storm per individuare eventuali errori restituiti dalle topologie.

Per altre informazioni sull'uso dell'Interfaccia utente di Storm, vedere gli argomenti seguenti:

* Se si usa Storm **basato su Linux** nel cluster HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight basato su Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Se si usa Storm **basato su Windows** nel cluster HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight basato su Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>Passaggi successivi

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)


