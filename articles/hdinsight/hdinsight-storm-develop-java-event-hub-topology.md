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
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eaa86b706a538543816b59d8cd09ee54df43b26d


---
# <a name="process-events-from-azure-event-hubs-with-storm-on-hdinsight-java"></a>Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)
L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per l'analisi di questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm usando il relativo bolt.

In questa esercitazione si apprenderà come usare spout e bolt dell'hub eventi per leggere e scrivere dati in una topologia Storm basata su Java.

## <a name="prerequisites"></a>Prerequisiti
* Cluster Apache Storm in HDInsight. Per la creazione di un cluster, usare uno degli articoli introduttivi seguenti:
  
  * Un [cluster Storm in HDInsight basato su Linux](hdinsight-apache-storm-tutorial-get-started-linux.md): scegliere questo tipo di cluster se si vuole usare SSH per accedere al cluster da client Linux, Unix, OS X o Windows.
  * Un [cluster Storm in HDInsight basato su Windows](hdinsight-apache-storm-tutorial-get-started.md): scegliere questo tipo di cluster se si vuole usare PowerShell per accedere al cluster da un client Windows.
    
    > [!NOTE]
    > I passaggi in questo documento si basano sull'uso di un cluster Storm in HDInsight versione 3.3 o 3.4. Questi cluster forniscono Storm 0.10.0 e Hadoop 2.7, che riducono il numero di passaggi necessari per il funzionamento dell'esempio.
    > 
    > Per una versione di questo esempio chei funzioni con Storm 0.9.3 in HDInsight 3.2, vedere il ramo [Storm v0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repository di esempio.
    > 
    > 
* Un [Hub eventi di Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Oracle Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o equivalente, ad esempio [OpenJDK](http://openjdk.java.net/).
* [Maven](https://maven.apache.org/download.cgi): Maven è un sistema per la compilazione di progetti per Java.
* Editor di testo o ambiente di sviluppo integrato (IDE) Java.
  
  > [!NOTE]
  > È possibile che l'editor di testo o l'ambiente IDE offra funzionalità specifiche per l'uso di Maven non descritte in questo documento. Per informazioni sulle funzionalità dell'ambiente di modifica, vedere la documentazione del prodotto usato.
  > 
  > 
  
  * Un client SSH. Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:
    
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
* Client SCP. Viene fornito con tutti i sistemi Linux, Unix e OS X. Per i client Windows è consigliabile PSCP, disponibile nella [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="understanding-the-example"></a>Informazioni sull'esempio
L'esempio [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) include due topologie:

**com.microsoft.example.EventHubWriter** scrive dati casuali in un hub eventi di Azure. I dati vengono generati da uno spout e sono costituiti da un ID dispositivo casuale e da un valore del dispositivo. Simula quindi un dispositivo hardware che genera un ID stringa e un valore numerico.

**com.microsoft.example.EventHubReader** legge i dati dall'hub eventi, scritti da EventHubWriter, e li archivia in HDFS, in questo caso WASB perché è stato scritti e testato con Azure HDInsight, nella directory /devicedata.

I dati vengono formattati come documento JSON prima di essere scritti nell'hub eventi e quando vengono letti dal lettore, vengono analizzati da JSON e inseriti in tuple. Il formato JSON è il seguente:

    { "deviceId": "unique identifier", "deviceValue": some value }

Il motivo per cui viene usato un documento JSON per archiviare i dati in un hub eventi consiste nel fornire all'utente informazioni sul formato, invece di fare affidamento sui meccanismi di formattazione interni di spout e bolt dell'hub eventi.

### <a name="project-configuration"></a>Configurazione del progetto
Il file **POM.xml** contiene informazioni di configurazione per questo progetto Maven. Ecco gli aspetti interessanti:

#### <a name="the-eventhubs-storm-spout-dependency"></a>Dipendenza di spout Storm in EventHubs
    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-eventhubs</artifactId>
      <version>0.10.0</version>
    </dependency>

Aggiunge una dipendenza per il pacchetto storm-eventhubs che contiene sia uno spout per la lettura da Hub eventi che un bolt per la scrittura in Hub eventi.

> [!NOTE]
> Questo pacchetto è disponibile solo per Storm 0.10.0 e versioni successive. Quando si usa Storm 0.9.3, è necessario installare manualmente il pacchetto spout fornito da Microsoft. Per un esempio di utilizzo di Storm 0.9.3, vedere il ramo [Storm 0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repository di esempio.
> 
> 

#### <a name="the-hdfsbolt-and-wasb-components"></a>Componenti HdfsBolt e WASB
HdfsBolt viene usato in genere per archiviare i dati in Hadoop Distributed File System (HDFS). I cluster HDInsight usano tuttavia Azure Storage (WASB) come archivio dati predefinito, quindi è necessario caricare diversi componenti per consentire a HdfsBolt di riconoscere il file system WASB.

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
        <version>0.10.0</version>
        </dependency>
    <!--So HdfsBolt knows how to talk to WASB -->
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-client</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-hdfs</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-azure</artifactId>
        <version>2.7.1</version>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.1</version>
        <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        </exclusions>
    </dependency>

> [!NOTE]
> Quando si utilizza una versione precedente di HDInsight, ad esempio la versione 3.2, è necessario registrare manualmente questi componenti. Per un esempio, insieme ai bit personalizzati necessari per i cluster HDInsight meno recenti, vedere il ramo [Storm 0.9.3](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub/tree/Storm_v0.9.3) del repository di esempio.
> 
> 

#### <a name="the-maven-compiler-plugin"></a>Maven-compiler-plugin
    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>2.3.2</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

Indica a Maven che il progetto deve essere compilato con un livello di compatibilità per Java 7 usato dai cluster HDInsight.

#### <a name="the-maven-shade-plugin"></a>Maven-shade-plugin
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

Si usa per creare il pacchetto della soluzione in un file uberjar che contiene sia il codice del progetto che le dipendenze necessarie. Viene usato anche per:

* Rinominare i file di licenza per le dipendenze. Se non si esegue questa operazione, può verificarsi un errore in fase di runtime nei cluster HDInsight basati su Windows.
* Escludere sicurezza e firme. Se non si esegue questa operazione, può verificarsi un errore in fase di runtime nel cluster HDInsight.
* Verificare che più implementazioni della stessa interfaccia vengano unite in un'unica voce. Se questa non viene eseguita, si riceveranno messaggi di errore relativi al fatto che il bolt Storm-HDFS non ha capito come comunicare con il file system WASB.

#### <a name="the-exec-maven-plugin"></a>Exec-maven-plugin
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

Consente di eseguire la topologia in locale nel computer di sviluppo con il comando seguente:

    mvn compile exec:java -Dstorm.topology=<CLASSNAME>

Ad esempio `mvn compile exec:java -Dstorm.topology=com.microsoft.example.EventHubWriter`.

#### <a name="the-resources-section"></a>Sezione resources
    <resources>
      <resource>
        <directory>${basedir}/conf</directory>
        <filtering>false</filtering>
        <includes>
          <include>EventHubs.properties</include>
          <include>core-site.xml</include>
        </includes>
      </resource>
    </resources>

Definisce le risorse necessarie per il progetto:

* **EventHubs.properties**: contiene le informazioni usate per connettersi a un hub eventi di Azure.
* **core-site.xml**: contiene informazioni su Archiviazione di Azure usate dal cluster HDInsight.

È necessario popolarle entrambe con le informazioni relative all'hub eventi e al cluster HDInsight.

## <a name="configure-environment-variables"></a>Configurare le variabili di ambiente
Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** : deve contenere i percorsi seguenti:
  
  * **JAVA_HOME** o il percorso equivalente
  * **JAVA_HOME\bin** o il percorso equivalente
  * Directory in cui è installato Maven

## <a name="configure-event-hub"></a>Configurare l'hub eventi
L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, seguire questa procedura.

1. Nel [Portale di Azure classico](https://manage.windowsazure.com) selezionare **NUOVO** > **Bus di servizio** > **Hub eventi** > **Creazione personalizzata**.
2. Nella schermata **Aggiungi hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Fare clic sulla **freccia** per continuare.
   
    ![procedura guidata - pagina 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)
   
   > [!NOTE]
   > È consigliabile selezionare lo stesso **Percorso** di Storm nel server HDInsight per ridurre la latenza e i costi.
   > 
   > 
3. Nella schermata **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.
   
    ![procedura guidata - pagina 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)
4. Dopo aver creato l'hub eventi, selezionare lo spazio dei nomi, selezionare **Hub eventi**, quindi selezionare l'hub eventi creato in precedenza.
5. Selezionare **Configura**e quindi creare due nuovi criteri di accesso usando le informazioni seguenti.
   
    <table>
    <tr><th>Name</th><th>Autorizzazioni</th></tr>
    <tr><td>Writer</td><td>Invio</td></tr>
    <tr><td>Reader</td><td>Attesa</td></tr>
    </table>
   
    Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Verranno creati i criteri di accesso condivisi che saranno usati per inviare (writer) e rimanere in ascolto (reader) in questo hub eventi.
   
    ![criteri](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)
6. Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** nella parte inferiore della pagina per recuperare la chiave per i criteri **writer** e **reader**. Salvare queste informazioni perché verranno usate in seguito.

## <a name="download-and-build-the-project"></a>Scaricare e compilare il progetto
1. Scaricare il progetto da GitHub: [hdinsight-java-storm-eventhub](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub). È possibile scaricare il pacchetto come archivio ZIP o usare [git](https://git-scm.com/) per clonare il progetto in locale.
2. Usare il comando seguente per compilare il progetto e creare il pacchetto:
   
        mvn package
   
    Vengono scaricate le dipendenze necessarie, viene compilato il progetto e quindi viene creato il pacchetto: L'output sarà archiviato nella directory **/target** come **EventHubExample-1.0-SNAPSHOT.jar**.

## <a name="deploy-the-topologies"></a>Distribuire le topologie
Il file JAR creato da questo progetto contiene due topologie, **com.microsoft.example.EventHubWriter** e **com.microsoft.example.EventHubReader**. La topologia EventHubWriter dovrà essere avviata per prima, perché scrive gli eventi nell'hub eventi che vengono quindi letti da EventHubReader.

### <a name="if-using-a-linux-based-cluster"></a>Se si usa un cluster basato su Linux
1. Usare SCP per copiare il pacchetto JAR nel cluster HDInsight. Sostituire USERNAME con l'utente SSH del cluster. Sostituire CLUSTERNAME con il nome del cluster HDInsight:
   
        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.
   
    Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Ad esempio `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.
   
   > [!NOTE]
   > Se il client è una workstation Windows, il comando SCP potrebbe non essere installato. È consigliabile usare PSCP che può essere scaricato nella [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
   > 
   > 
   
    Questo comando copia il file nella home directory dell'utente SSH nel cluster.
2. Una volta completato il caricamento del file, usare SSH per connettersi al cluster HDInsight: Sostituire **USERNAME** con il nome di accesso a SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:
   
        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
   
   > [!NOTE]
   > Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata verrà caricata da `~/.ssh/id_rsa`:
   > 
   > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`
   > 
   > 
   
    Se si usa PuTTY, immettere `CLUSTERNAME-ssh.azurehdinsight.net` nel campo **Host Name (o IP address)** e quindi fare clic su **Open** per connettersi. Verrà richiesto di immettere il nome dell'account SSH.
   
   > [!NOTE]
   > Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se con l'account è stata usata una chiave SSH, potrebbe essere necessario eseguire questi passaggi per selezionare la chiave:
   > 
   > 1. In **Category** espandere **Connection**, **SSH** e selezionare **Auth**.
   > 2. Fare clic su **Browse** e selezionare il file PPK che contiene la chiave privata.
   > 3. Fare clic su **Open** per connettersi.
   > 
   > 
3. Per avviare le topologie, usare il comando seguente:
   
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader
   
    Le topologie verranno avviate e saranno assegnati i nomi descrittivi "reader" e "writer".
4. Attendere un minuto o due per consentire alle topologie di scrivere e leggere gli eventi dall'hub eventi, quindi usare il comando seguente per verificare che EventHubReader memorizzi i dati nell'archiviazione di HDInsight:
   
        hadoop fs -ls /devicedata
   
    Dovrebbe essere restituito un elenco di file simile al seguente:
   
        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt
   
   > [!NOTE]
   > Le dimensioni di alcuni file potrebbero essere pari a 0, perché sono stati creati da EventHubReader, ma non contengono ancora dati archiviati.
   > 
   > 
   
    Per visualizzare il contenuto di questi file, è possibile usare il comando seguente:
   
        hadoop fs -text /devicedata/*.txt
   
    Verranno restituiti dati simili ai seguenti:
   
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

### <a name="if-using-a-windows-based-cluster"></a>Se si usa un cluster basato su Windows
1. Nel browser Web, aprire https://NOMECLUSTER.azurehdinsight.net. Quando richiesto, immettere le credenziali di amministratore per il cluster di HDInsight. Verrà visualizzato il Dashboard di Storm.
2. Usare l'elenco a discesa **File JAR** per trovare e selezionare il file EventHubExample-1.0-SNAPSHOT.jar nell'ambiente di compilazione.
3. Per **Nome classe** immettere `com.mirosoft.example.EventHubWriter`.
4. Per **Parametri aggiuntivi** immettere `writer`. Infine fare clic su **Invia** per caricare il file JAR e avviare la topologia EventHubWriter.
5. Una volta avviata la topologia, usare il form per avviare EventHubReader:
   
   * **File JAR**: selezionare il file EventHubExample-1.0-SNAPSHOT.jar caricato in precedenza.
   * **Class Name**: immettere `com.microsoft.example.EventHubReader`
   * **Additional Parameters**: immettere `reader`
     
     Fare clic su Invia per avviare la topologia EventHubReader.
6. Attendere alcuni minuti per consentire alle topologie di generare gli eventi e archiviarli in Archiviazione di Azure, quindi fare clic sulla scheda della **console di query di Hadoop** in alto nella pagina **Dashboard di Storm**.
7. In **Console query** selezionare **Editor Hive** e sostituire la stringa predefinita `select * from hivesampletable` con la seguente:
   
        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasbs:///devicedata/';
        select * from devicedata limit 10;
   
    Fare clic su **Seleziona** per eseguire la query. Verranno restituite 10 righe dai dati scritti in Archiviazione di Azure (WASB) da EventHubReader. Dopo il completamento della query, dovrebbero essere visualizzati dati simili ai seguenti:
   
        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574
8. Selezionare **Dashboard di Storm** in alto nella pagina, quindi selezionare **Interfaccia utente di Storm**. In **Interfaccia utente di Storm** selezionare il collegamento per la topologia **reader**r e quindi usare il pulsante **Termina** per arrestare la topologia. Ripetere il processo per la topologia **writer** .

### <a name="checkpointing"></a>Checkpoint
EventHubSpout controlla periodicamente il proprio stato nel nodo Zookeeper, operazione che salva l'offset corrente per i messaggi letti dalla coda. Questo consente al componente di avviare la ricezione di messaggi in corrispondenza dell'offset salvato negli scenari seguenti:

* L'istanza del componente ha esito negativo e viene riavviata.
* Il cluster viene aumentato o ridotto mediante l'aggiunta o la rimozione di nodi.
* La topologia viene arrestata e riavviata **con lo stesso nome**.

#### <a name="on-windows-based-hdinsight-clusters"></a>Nei cluster HDInsight basati su Windows
È possibile esportare e importare i checkpoint persistenti in WASB (il servizio Archiviazione di Azure usato dal cluster HDInsight). Gli script per eseguire questa operazione si trovano in Storm, nel cluster HDInsight, in **c:\apps\dist\storm-0.9.3.2.2.1.0-2340\zkdatatool-1.0\bin**.

> [!NOTE]
> Il numero di versione indicato nel percorso può essere diverso, poiché la versione di Storm installata nel cluster può cambiare.
> 
> 

Gli script presenti in questa directory sono:

* **stormmeta_import.cmd**: importa tutti i metadati Storm dal contenitore di archiviazione predefinito del cluster in Zookeeper.
* **stormmeta_export.cmd**: esporta tutti i metadati Storm da Zookeeper al contenitore di archiviazione predefinito del cluster.
* **stormmeta_delete.cmd**: elimina tutti i metadati Storm da Zookeeper.

L'esportazione di un'importazione consente di rendere persistenti i dati del punto di controllo quando è necessario eliminare il cluster ma si desidera riprendere l'elaborazione dall'offset corrente nell'hub quando si riporta un nuovo cluster online.

> [!NOTE]
> Poiché i dati vengono mantenuti nel contenitore di archiviazione predefinito, il nuovo cluster **deve** usare gli stessi account e contenitore di archiviazione del cluster precedente.
> 
> 

## <a name="delete-your-cluster"></a>Eliminare il cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshooting"></a>Risoluzione dei problemi
Se non vengono visualizzati file archiviati nel percorso /devicedata, tramite il comando `hadoop fs -ls /devicedata` o il comando Hive nella Console query, usare l'Interfaccia utente di Storm per vedere gli eventuali errori restituiti dalle topologie.

Per altre informazioni sull'uso dell'Interfaccia utente di Storm, vedere gli argomenti seguenti:

* Se si usa Storm **basato su Linux** nel cluster HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight basato su Linux](hdinsight-storm-deploy-monitor-topology-linux.md)
* Se si usa Storm **basato su Windows** nel cluster HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight basato su Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

## <a name="next-steps"></a>Passaggi successivi
* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)




<!--HONumber=Nov16_HO3-->


