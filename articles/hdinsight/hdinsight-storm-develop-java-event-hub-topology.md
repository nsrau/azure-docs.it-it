<properties
   pageTitle="Elaborare eventi dell'hub eventi con Storm in HDInsight | Azure"
   description="Informazioni su come elaborare i dati dell'hub eventi con una topologia Storm Java creata con Maven."
   services="hdinsight,notification hubs"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/12/2015"
   ms.author="larryfr"/>

# Elaborare eventi dell'hub eventi di Azure con Storm in HDInsight (Java)

L'hub eventi di Azure consente di elaborare grandi quantità di dati da siti Web, app e dispositivi. Lo spout dell'hub eventi semplifica l'uso di Apache Storm in HDInsight per l'analisi di questi dati in tempo reale. È anche possibile scrivere dati nell'hub eventi da Storm usando il relativo bolt.

In questa esercitazione si apprenderà come usare spout e bolt dell'hub eventi per leggere e scrivere dati in una topologia Storm basata su Java.

## Prerequisiti

* Cluster Apache Storm in HDInsight. Per la creazione di un cluster, usare uno degli articoli introduttivi seguenti:

    - [Cluster basato su Linux](hdinsight-apache-storm-tutorial-get-started-linux.md): scegliere questo tipo di cluster se si vuole usare SSH per accedere al cluster da client Linux, Unix, OS X o Windows.

    - [Cluster basato su Windows](hdinsight-apache-storm-tutorial-get-started.md): scegliere questo tipo di cluster se si vuole usare PowerShell per accedere al cluster da un client Windows.

    > [AZURE.NOTE]La sola differenza tra i due tipi di cluster riguarda l'uso di SSH o di un Web Form per l'invio della topologia al cluster.

* [Hub eventi di Azure](../event-hubs/service-bus-event-hubs-csharp-ephcs-getstarted.md)

* [Oracle Java Developer Kit (JDK) versione 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) o equivalente, ad esempio [OpenJDK](http://openjdk.java.net/).

* [Maven](https://maven.apache.org/download.cgi): Maven è un sistema per la compilazione di progetti per Java.

* Editor di testo o ambiente di sviluppo integrato (IDE) Java.

	> [AZURE.NOTE]È possibile che l'editor di testo o l'ambiente IDE offra funzionalità specifiche per l'uso di Maven non descritte in questo documento. Per informazioni sulle funzionalità dell'ambiente di modifica, vedere la documentazione del prodotto usato.

 * Un client SSH. Per altre informazioni sull'uso di SSH con HDInsight, vedere gli articoli seguenti:

    - [Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Usare SSH con Hadoop basato su Linux in HDInsight da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* Client SCP. Viene fornito con tutti i sistemi Linux, Unix e OS X. Per i client Windows è consigliabile PSCP, disponibile nella [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##Informazioni sull'esempio

L'esempio [hdinsight-java-storm-eventhub](https://github.com/Blackmist/hdinsight-java-storm-eventhub) include due topologie:

__com.microsoft.example.EventHubWriter__ scrive dati casuali in un hub eventi di Azure. I dati vengono generati da uno spout e sono costituiti da un ID dispositivo casuale e da un valore del dispositivo. Simula quindi un dispositivo hardware che genera un ID stringa e un valore numerico.

__com.microsoft.example.EventHubReader__ legge i dati dall'hub eventi, scritti da EventHubWriter, e li archivia in HDFS, in questo caso WASB perché è stato scritti e testato con Azure HDInsight, nella directory /devicedata.

I dati vengono formattati come documento JSON prima di essere scritti nell'hub eventi e quando vengono letti dal lettore, vengono analizzati da JSON e inseriti in tuple. Il formato JSON è il seguente:

    { "deviceId": "unique identifier", "deviceValue": some value }

Il motivo per cui viene usato un documento JSON per archiviare i dati in un hub eventi consiste nel fornire all'utente informazioni sul formato, invece di fare affidamento sui meccanismi di formattazione interni di spout e bolt dell'hub eventi.

###Configurazione del progetto

Il file **POM.xml** contiene informazioni di configurazione per questo progetto Maven. Ecco gli aspetti interessanti:

####Dipendenza di spout Storm in EventHubs

    <dependency>
      <groupId>com.microsoft.eventhubs</groupId>
      <artifactId>eventhubs-storm-spout</artifactId>
      <version>0.9.3</version>
    </dependency>

Aggiunge una dipendenza per il pacchetto eventhubs-storm-spout che contiene sia uno spout per la lettura che un bolt per la scrittura negli hub eventi.

> [AZURE.NOTE]Questo pacchetto non è disponibile in Maven e verrà installato manualmente nel repository Maven in un passaggio successivo.

####Componenti HdfsBolt e WASB

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
        <version>0.9.3</version>
      </dependency>
      <!--
     This is a temporary workaround to make HdfsBolt work with WASB through hadoop-azure project.
     For now, we have to build hadoop-client, hadoop-hdfs and hadoop-azure from Hadoop trunk
     (which defaults to 3.0.0-SNAPSHOT version). And push those jars and dependencies to local
     mvn repo (take a look at push_lib_mvn.ps1).

     Once Hadoop 2.7 is released, we can just switch to that version.
     Note that hadoop-azure is added to Hadoop on Hadoop 2.7.
     -->
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-client</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-hdfs</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-azure</artifactId>
       <version>3.0.0-SNAPSHOT</version>
     </dependency>
     <dependency>
       <groupId>org.apache.hadoop</groupId>
       <artifactId>hadoop-common</artifactId>
       <version>3.0.0-SNAPSHOT</version>
       <exclusions>
         <exclusion>
           <groupId>org.slf4j</groupId>
           <artifactId>slf4j-log4j12</artifactId>
         </exclusion>
       </exclusions>
     </dependency>
     <dependency>
       <groupId>com.microsoft.windowsazure.storage</groupId>
       <artifactId>microsoft-windowsazure-storage-sdk</artifactId>
       <version>0.6.0</version>
     </dependency>

> [AZURE.NOTE]I pacchetti per abilitare WASB non sono disponibili nel repository Maven e saranno installati manualmente in un passggio successivo.

####Maven-compiler-plugin

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

####Maven-shade-plugin

      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
        <version>2.3</version>
        <configuration>
          <!-- Keep us from getting a can't overwrite file error -->
          <transformers>
            <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
            </transformer>
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

####Exec-maven-plugin

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

####Sezione resources

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

- **EventHubs.properties**: contiene le informazioni usate per connettersi a un hub eventi di Azure.
- **core-site.xml**: contiene informazioni su Archiviazione di Azure usate dal cluster HDInsight.

È necessario popolarle entrambe con le informazioni relative all'hub eventi e al cluster HDInsight.

##Configurare le variabili di ambiente

Le variabili di ambiente seguenti possono essere impostate quando si installa Java e l'JDK nella workstation di sviluppo. È tuttavia necessario verificare che esistano e che contengano i valori corretti per il sistema in uso.

* **JAVA\_HOME**: deve puntare alla directory in cui è installato Java Runtime Environment (JRE). In una distribuzione Unix o Linux, ad esempio, deve avere un valore simile a `/usr/lib/jvm/java-7-oracle`. In Windows, avrebbe un valore simile a `c:\Program Files (x86)\Java\jre1.7`.

* **PATH**: deve contenere i percorsi seguenti:

	* **JAVA\_HOME** o il percorso equivalente

	* **JAVA\_HOME\\bin** o il percorso equivalente

	* Directory in cui è installato Maven

## Configurare l'hub eventi

L'hub eventi è l'origine dati per questo esempio. Per creare un nuovo hub eventi, seguire questa procedura.

1. Nel [Portale di Azure](https://manage.windowsazure.com) selezionare **NUOVO** > **Bus di servizio** > **Hub eventi** > **Creazione personalizzata**.

2. Nella schermata **Aggiungi hub eventi** immettere un nome in **Nome hub eventi**, selezionare l'area in cui creare l'hub in **Area geografica** e creare un nuovo spazio dei nomi o selezionarne uno esistente. Fare clic sulla **freccia** per continuare.

	![procedura guidata - pagina 1](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz1.png)

	> [AZURE.NOTE]È consigliabile selezionare lo stesso **Percorso** di Storm nel server HDInsight per ridurre la latenza e i costi.

2. Nella schermata **Configura hub eventi** immettere i valori per **Conteggio partizioni** e **Conservazione messaggi**. Per questo esempio usare un numero di partizioni pari a 10 e un valore di conservazione dei messaggi pari a 1. Prendere nota del numero di partizioni, perché questo valore sarà necessario in seguito.

	![procedura guidata - pagina 2](./media/hdinsight-storm-develop-csharp-event-hub-topology/wiz2.png)

3. Dopo aver creato l'hub eventi, selezionare lo spazio dei nomi, selezionare **Hub eventi**, quindi selezionare l'hub eventi creato in precedenza.

4. Selezionare **Configura** e quindi creare due nuovi criteri di accesso usando le informazioni seguenti.

	<table>
<tr><th>Name</th><th>Autorizzazioni</th></tr>
<tr><td>Writer</td><td>Invio</td></tr>
<tr><td>Reader</td><td>Attesa</td></tr>
</table>Dopo avere creato le autorizzazioni, selezionare l'icona **Salva** nella parte inferiore della pagina. Verranno creati i criteri di accesso condivisi che saranno usati per inviare (writer) e rimanere in ascolto (reader) in questo hub eventi.

	![criteri](./media/hdinsight-storm-develop-csharp-event-hub-topology/policy.png)

5. Dopo avere salvato i criteri, usare **Generatore di chiavi di accesso condivise** nella parte inferiore della pagina per recuperare la chiave per i criteri **writer** e **reader**. Salvare queste informazioni perché verranno usate in seguito.

## Scaricare e compilare il progetto

1. Scaricare il progetto da GitHub: [hdinsight-java-storm-eventhub](https://github.com/Blackmist/hdinsight-java-storm-eventhub). È possibile scaricare il pacchetto come archivio ZIP o usare [git](https://git-scm.com/) per clonare il progetto in locale.

2. Usare i comandi seguenti per installare i pacchetti inclusi nel progetto nel repository Maven locale. Vengono abilitati gli spout e i bolt dell'hub eventi, oltre alla possibilità di usare HdfsBolt per la scrittura in Archiviazione di Azure (WASB).

		mvn -q install:install-file -Dfile=lib/eventhubs/eventhubs-storm-spout-0.9.3-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9.3 -Dpackaging=jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-azure-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-client-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-hdfs-3.0.0-SNAPSHOT.jar

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.jar -DpomFile=lib/hadoop/hadoop-common-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-dist-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-project-3.0.0-SNAPSHOT.pom

		mvn -q org.apache.maven.plugins:maven-install-plugin:2.5.2:install-file -Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom -DpomFile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom

	> [AZURE.NOTE]Se si usa Powershell, potrebbe essere necessario racchiudere i parametri `-D` tra parentesi. Ad esempio: `"-Dfile=lib/hadoop/hadoop-main-3.0.0-SNAPSHOT.pom"`.

	Questi file provengono in origine da https://github.com/hdinsight/hdinsight-storm-examples, dove è possibile trovare le versioni più recenti.

3. Usare il comando seguente per compilare il progetto e creare il pacchetto:

        mvn package

    Vengono scaricate le dipendenze necessarie, viene compilato il progetto e quindi viene creato il pacchetto: L'output sarà archiviato nella directory __/target__ come __EventHubExample-1.0-SNAPSHOT.jar__.

## Distribuire le topologie

Il file JAR creato da questo progetto contiene due topologie, __com.microsoft.example.EventHubWriter__ e __com.microsoft.example.EventHubReader__. La topologia EventHubWriter dovrà essere avviata per prima, perché scrive gli eventi nell'hub eventi che vengono quindi letti da EventHubReader.

###Se si usa un cluster basato su Linux

1. Usare SCP per copiare il pacchetto JAR nel cluster HDInsight. Sostituire USERNAME con l'utente SSH del cluster. Sostituire CLUSTERNAME con il nome del cluster HDInsight:

        scp ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.

    Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Ad esempio: `scp -i ~/.ssh/id_rsa ./target/EventHubExample-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:.`.

    > [AZURE.NOTE]Se il client è una workstation Windows, il comando SCP potrebbe non essere installato. È consigliabile usare PSCP che può essere scaricato nella [pagina di download di PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

    Questo comando copia il file nella home directory dell'utente SSH nel cluster.

1. Una volta completato il caricamento del file, usare SSH per connettersi al cluster HDInsight: Sostituire **USERNAME** con il nome dell'account di accesso SSH. Sostituire **CLUSTERNAME** con il nome del cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    > [AZURE.NOTE]Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se è stata usata una chiave SSH con l'account, potrebbe essere necessario usare il parametro `-i` per specificare il percorso del file di chiave. Nell'esempio seguente la chiave privata verrà caricata da `~/.ssh/id_rsa`:
    >
    > `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`

    Se si usa PuTTY, immettere `CLUSTERNAME-ssh.azurehdinsight.net` nel campo __Host Name (o IP address)__ e quindi fare clic su __Open__ per connettersi. Verrà richiesto di immettere il nome dell'account SSH.

    > [AZURE.NOTE]Se è stata usata una password per l'account SSH, verrà richiesto di immetterla. Se con l'account è stata usata una chiave SSH, potrebbe essere necessario eseguire questi passaggi per selezionare la chiave:
    >
    > 1. In **Category** espandere **Connection**, **SSH** e selezionare **Auth**.
    > 2. Fare clic su **Browse** e selezionare il file PPK che contiene la chiave privata.
    > 3. Fare clic su __Open__ per connettersi.

2. Per avviare le topologie, usare il comando seguente:

        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubWriter writer
        storm jar EventHubExample-1.0-SNAPSHOT.jar com.microsoft.example.EventHubReader reader

    Le topologie verranno avviate e saranno assegnati i nomi descrittivi "reader" e "writer".

3. Attendere un minuto o due per consentire alle topologie di scrivere e leggere gli eventi dall'hub eventi, quindi usare il comando seguente per verificare che EventHubReader memorizzi i dati nell'archiviazione di HDInsight:

        hadoop fs -ls /devicedata

    Dovrebbe essere restituito un elenco di file simile al seguente:

        -rw-r--r--   1 storm supergroup      10283 2015-08-11 19:35 /devicedata/wasbbolt-14-0-1439321744110.txt
        -rw-r--r--   1 storm supergroup      10277 2015-08-11 19:35 /devicedata/wasbbolt-14-1-1439321748237.txt
        -rw-r--r--   1 storm supergroup      10280 2015-08-11 19:36 /devicedata/wasbbolt-14-10-1439321760398.txt
        -rw-r--r--   1 storm supergroup      10267 2015-08-11 19:36 /devicedata/wasbbolt-14-11-1439321761090.txt
        -rw-r--r--   1 storm supergroup      10259 2015-08-11 19:36 /devicedata/wasbbolt-14-12-1439321762679.txt

    > [AZURE.NOTE]Le dimensioni di alcuni file potrebbero essere pari a 0, perché sono stati creati da EventHubReader, ma non contengono ancora dati archiviati.

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

4. Per arrestare le topologie, usare i comandi seguenti:

        storm kill reader
        storm kill writer

###Se si usa un cluster basato su Windows

1. Aprire il browser all'indirizzo https://CLUSTERNAME.azurehdinsight.net. Quando richiesto, immettere le credenziali di amministratore per il cluster di HDInsight. Verrà visualizzato il Dashboard di Storm.

2. Usare l'elenco a discesa __File JAR__ per trovare e selezionare il file EventHubExample-1.0-SNAPSHOT.jar nell'ambiente di compilazione.

3. Per __Nome classe__ immettere `com.mirosoft.example.EventHubWriter`.

4. Per __Parametri aggiuntivi__ immettere `writer`. Infine fare clic su __Invia__ per caricare il file JAR e avviare la topologia EventHubWriter.

5. Una volta avviata la topologia, usare il form per avviare EventHubReader:

    * __File JAR__: selezionare il file EventHubExample-1.0-SNAPSHOT.jar caricato in precedenza.
    * __Nome classe__: immettere `com.microsoft.example.EventHubReader`.
    * __Parametri aggiuntivi__ immettere `reader`.

    Fare clic su Invia per avviare la topologia EventHubReader.

6. Attendere alcuni minuti per consentire alle topologie di generare gli eventi e archiviarli in Archiviazione di Azure, quindi fare clic sulla scheda __Console query__ in alto nella pagina __Dashboard di Storm__.

7. In __Console query__ selezionare __Editor Hive__ e sostituire la stringa predefinita `select * from hivesampletable` con la seguente:

        create external table devicedata (deviceid string, devicevalue int) row format delimited fields terminated by ',' stored as textfile location 'wasb:///devicedata/';
        select * from devicedata limit 10;

    Fare clic su __Seleziona__ per eseguire la query. Verranno restituite 10 righe dai dati scritti in Archiviazione di Azure (WASB) da EventHubReader. Dopo il completamento della query, dovrebbero essere visualizzati dati simili ai seguenti:

        3409e622-c85d-4d64-8622-af45e30bf774,848981614
        c3305f7e-6948-4cce-89b0-d9fbc2330c36,-1638780537
        788b9796-e2ab-49c4-91e3-bc5b6af1f07e,-1662107246
        6403df8a-6495-402f-bca0-3244be67f225,275738503
        d7c7f96c-581a-45b1-b66c-e32de6d47fce,543829859
        9a692795-e6aa-4946-98c1-2de381b37593,1857409996
        3c8d199b-0003-4a79-8d03-24e13bde7086,-1271260574

8. Selezionare __Dashboard di Storm__ in alto nella pagina, quindi selezionare __Interfaccia utente di Storm__. In __Interfaccia utente di Storm__ selezionare il collegamento per la topologia __reader__ e quindi usare il pulsante __Termina__ per arrestare la topologia. Ripetere il processo per la topologia __writer__.



### Checkpoint

EventHubSpout controlla periodicamente il proprio stato nel nodo Zookeeper, operazione che salva l'offset corrente per i messaggi letti dalla coda. Questo consente al componente di avviare la ricezione di messaggi in corrispondenza dell'offset salvato negli scenari seguenti:

* L'istanza del componente ha esito negativo e viene riavviata.

* Il cluster viene aumentato o ridotto mediante l'aggiunta o la rimozione di nodi.

* La topologia viene arrestata e riavviata **con lo stesso nome**.

####Nei cluster HDInsight basati su Windows

È possibile esportare e importare i checkpoint persistenti in WASB (il servizio Archiviazione di Azure usato dal cluster HDInsight). Gli script per eseguire questa operazione si trovano in Storm, nel cluster HDInsight, in **c:\\apps\\dist\\storm-0.9.3.2.2.1.0-2340\\zkdatatool-1.0\\bin**.

>[AZURE.NOTE]Il numero di versione indicato nel percorso può essere diverso, poiché la versione di Storm installata nel cluster può cambiare.

Gli script presenti in questa directory sono:

* **stormmeta\_import.cmd**: importa tutti i metadati Storm dal contenitore di archiviazione predefinito del cluster in Zookeeper.

* **stormmeta\_export.cmd**: esporta tutti i metadati Storm da Zookeeper al contenitore di archiviazione predefinito del cluster.

* **stormmeta\_delete.cmd**: elimina tutti i metadati Storm da Zookeeper.

L'esportazione di un'importazione consente di rendere persistenti i dati del punto di controllo quando è necessario eliminare il cluster ma si desidera riprendere l'elaborazione dall'offset corrente nell'hub quando si riporta un nuovo cluster online.

> [AZURE.NOTE]Poiché i dati vengono mantenuti nel contenitore di archiviazione predefinito, il nuovo cluster **deve** usare gli stessi account e contenitore di archiviazione del cluster precedente.

##Risoluzione dei problemi

Se non vengono visualizzati file archiviati nel percorso /devicedata, tramite il comando `hadoop fs -ls /devicedata` o il comando Hive nella Console query, usare l'Interfaccia utente di Storm per vedere gli eventuali errori restituiti dalle topologie.

Per altre informazioni sull'uso dell'Interfaccia utente di Storm, vedere gli argomenti seguenti:

* Se si usa Storm __basato su Linux__ nel cluster HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight basato su Linux](hdinsight-storm-deploy-monitor-topology-linux.md)

* Se si usa Storm __basato su Windows__ nel cluster HDInsight, vedere [Distribuire e gestire topologie Apache Storm in HDInsight basato su Windows](hdinsight-storm-deploy-monitor-topology-linux.md)

##Passaggi successivi

* [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=Oct15_HO1-->