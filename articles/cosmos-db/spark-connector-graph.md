---

title: 'Azure Cosmos DB: eseguire analisi dei grafi con Spark e Apache TinkerPop Gremlin | Microsoft Docs'
description: Questo articolo fornisce le istruzioni necessarie per configurare ed eseguire l'analisi dei grafi e il calcolo parallelo in Azure Cosmos DB con Spark e TinkerPop SparkGraphComputer.
services: cosmosdb
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: article
ms.date: 06/05/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: 5bbeb9d4516c2b1be4f5e076a7f63c35e4176b36
ms.openlocfilehash: 4f35ed6399df777edd90d1944f22dce91e76952f
ms.contentlocale: it-it
ms.lasthandoff: 06/13/2017


---
# <a name="azure-cosmos-db-perform-graph-analytics-by-using-spark-and-apache-tinkerpop-gremlin"></a>Azure Cosmos DB: eseguire analisi dei grafi con Spark e Apache TinkerPop Gremlin

[Azure Cosmos DB](introduction.md) è il servizio di database multimodello distribuito a livello globale di Microsoft. È possibile creare ed eseguire query su database di documenti, coppie chiave-valore e grafi sfruttando i vantaggi offerti dalle funzionalità di scalabilità orizzontale e distribuzione globale alla base di Azure Cosmos DB. Azure Cosmos DB supporta carichi di lavoro di grafi OLTP (Online Transaction Processing) basati sul linguaggio [Apache TinkerPop Gremlin](graph-introduction.md).

[Spark](http://spark.apache.org/) è un progetto di Apache Software Foundation incentrato sull'elaborazione di dati OLAP (Online Analytical Processing) generici. Spark offre un modello di calcolo distribuito in memoria/su disco simile al modello MapReduce di Hadoop. È possibile distribuire Apache Spark nel cloud con [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Combinando Azure Cosmos DB e Spark, è possibile eseguire carichi di lavoro sia OLTP che OLAP con Gremlin. Questa guida introduttiva illustra come eseguire query Gremlin su Azure Cosmos DB in un cluster Azure HDInsight Spark.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter eseguire questo esempio, è necessario soddisfare i prerequisiti seguenti:
* Cluster Azure HDInsight Spark 2.0
* JDK 1.8+ (eseguire `apt-get install default-jdk` se JDK non è disponibile)
* Maven (eseguire `apt-get install maven` se Maven non è disponibile)
* Una sottoscrizione di Azure ([!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)])

Per informazioni su come configurare un cluster Azure HDInsight Spark, vedere [Provisioning di cluster HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Creare un account di database Azure Cosmos DB

Per prima cosa, si crea un account di database con l'API Graph seguendo questa procedura:

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Aggiungere una raccolta

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="get-apache-tinkerpop"></a>Ottenere Apache TinkerPop

Ottenere Apache TinkerPop seguendo questa procedura:

1. Accedere in remoto al nodo master del cluster HDInsight `ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`.

2. Clonare il codice sorgente di TinkerPop3, compilarlo in locale ed eseguirne l'installazione nella cache di Maven.

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

3. Installare il plug-in Spark-Gremlin 

    a. L'installazione del plug-in viene gestita da Grape. Inserire le informazioni relative ai repository per Grape in modo da consentire il download del plug-in e delle relative dipendenze. 

      Creare il file di configurazione di Grape, se non è presente in `~/.groovy/grapeConfig.xml`. Usare le seguenti impostazioni:

    ```xml
    <ivysettings>
    <settings defaultResolver="downloadGrapes"/>
    <resolvers>
        <chain name="downloadGrapes">
        <filesystem name="cachedGrapes">
            <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
            <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
        </filesystem>
        <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
        <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
        <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
        <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
        <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
        <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
        </chain>
    </resolvers>
    </ivysettings>
    ``` 

    b. Avviare la console Gremlin `bin/gremlin.sh`.
        
    c. Installare il plug-in Spark-Gremlin con la versione 3.3.0-SNAPSHOT compilata nei passaggi precedenti:

    ```bash
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
    ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
    gremlin> :q
    $ bin/gremlin.sh

            \,,,/
            (o o)
    -----oOOo-(3)-oOOo-----
    plugin activated: tinkerpop.server
    plugin activated: tinkerpop.utilities
    plugin activated: tinkerpop.tinkergraph
    gremlin> :plugin use tinkerpop.spark
    ==>tinkerpop.spark activated
    ```

4. Controllare se `Hadoop-Gremlin` è attivato con `:plugin list`. Disabilitare questo plug-in perché potrebbe interferire con il plug-in Spark-Gremlin `:plugin unuse tinkerpop.hadoop`.

## <a name="prepare-tinkerpop3-dependencies"></a>Preparare le dipendenze di TinkerPop3

Durante la compilazione di TinkerPop3 nel passaggio precedente sono state estratte nella directory di destinazione anche tutte le dipendenze in formato JAR per Spark e Hadoop. Usare i file JAR preinstallati con HDI ed effettuare il pull delle dipendenze aggiuntive solo se necessario.

1. Passare alla directory di destinazione della console Gremlin in `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

2. Spostare tutti i file JAR da `ext/` a `lib/`: `find ext/ -name '*.jar' -exec mv {} lib/ \;`.

3. Rimuovere tutte le librerie JAR in `lib/` che non sono incluse nell'elenco seguente:

    ```bash
    # TinkerPop3
    gremlin-console-3.3.0-SNAPSHOT.jar
    gremlin-core-3.3.0-SNAPSHOT.jar       
    gremlin-groovy-3.3.0-SNAPSHOT.jar     
    gremlin-shaded-3.3.0-SNAPSHOT.jar     
    hadoop-gremlin-3.3.0-SNAPSHOT.jar     
    spark-gremlin-3.3.0-SNAPSHOT.jar      
    tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

    # Gremlin depedencies
    asm-3.2.jar                                
    avro-1.7.4.jar                             
    caffeine-2.3.1.jar                         
    cglib-2.2.1-v20090111.jar                  
    gbench-0.4.3-groovy-2.4.jar                
    gprof-0.3.1-groovy-2.4.jar                 
    groovy-2.4.9-indy.jar                      
    groovy-2.4.9.jar                           
    groovy-console-2.4.9.jar                   
    groovy-groovysh-2.4.9-indy.jar             
    groovy-json-2.4.9-indy.jar                 
    groovy-jsr223-2.4.9-indy.jar               
    groovy-sql-2.4.9-indy.jar                  
    groovy-swing-2.4.9.jar                     
    groovy-templates-2.4.9.jar                 
    groovy-xml-2.4.9.jar                       
    hadoop-yarn-server-nodemanager-2.7.2.jar   
    hppc-0.7.1.jar                             
    javatuples-1.2.jar                         
    jaxb-impl-2.2.3-1.jar                      
    jbcrypt-0.4.jar                            
    jcabi-log-0.14.jar                         
    jcabi-manifests-1.1.jar                    
    jersey-core-1.9.jar                        
    jersey-guice-1.9.jar                       
    jersey-json-1.9.jar                        
    jettison-1.1.jar                           
    scalatest_2.11-2.2.6.jar                   
    servlet-api-2.5.jar                        
    snakeyaml-1.15.jar                         
    unused-1.0.0.jar                           
    xml-apis-1.3.04.jar                        
    ```

## <a name="get-the-azure-cosmos-db-spark-connector"></a>Ottenere il connettore Spark per Azure Cosmos DB

1. Ottenere il connettore Spark per Azure Cosmos DB `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` e Cosmos DB Java SDK `azure-documentdb-1.10.0.jar` dalla relativa pagina in [Github](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark-0.0.3_2.0.2_2.11).

2. In alternativa, è possibile eseguirne la compilazione in locale. Dato che l'ultima versione di Spark-Gremlin è stata compilata con Spark 1.6.1 e non è compatibile con la versione Spark 2.0.2 attualmente usata nel connettore Spark per Azure Cosmos DB, è possibile compilare il codice di TinkerPop3 più recente e installare i file JAR manualmente. Eseguire le operazioni seguenti:

    a. Clonare il connettore Spark per Azure Cosmos DB.

    b. Compilare TinkerPop3 (operazione già eseguita nei passaggi precedenti) e installare tutti i file JAR di TinkerPop 3.3.0-SNAPSHOT in locale.

    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    c. Aggiornare `tinkerpop.version` `azure-documentdb-spark/pom.xml` a `3.3.0-SNAPSHOT`.
    
    d. Eseguire la compilazione con Maven. I file JAR necessari vengono inseriti in `target` e `target/alternateLocation`.

    ```bash
    git clone https://github.com/Azure/azure-cosmosdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copiare i file JAR elencati in precedenza in una directory locale in ~/azure-documentdb-spark:

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuire le dipendenze nei nodi di lavoro Spark 

1. Dato che la trasformazione dei dati dei grafi dipende da TinkerPop3, è necessario distribuire le relative dipendenze in tutti i nodi di lavoro Spark.

2. Copiare le dipendenze Gremlin elencate in precedenza, il file JAR del connettore Spark per CosmosDB e CosmosDB Java SDK nei nodi di lavoro seguendo questa procedura:

    a. Copiare tutti i file JAR in `~/azure-documentdb-spark`.

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    b. Ottenere l'elenco di tutti i nodi di lavoro Spark, disponibile nel dashboard Ambari, nell'elenco `Spark2 Clients` della sezione `Spark2`.

    c. Copiare la directory in ogni nodo.

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Configurare le variabili di ambiente

1. Trovare la versione HDP del cluster Spark, corrispondente al nome di directory in `/usr/hdp/` (ad esempio, 2.5.4.2-7).

2. Configurare hdp.version per tutti i nodi. Nel dashboard Ambari passare a **YARN section** > **Configs** > **Advanced** (Sezione YARN > Configurazioni > Avanzate) e seguire questa procedura: 
 
    a. In `Custom yarn-site` aggiungere una nuova proprietà `hdp.version` con il valore della versione di HDP nel nodo master. 
     
    b. Salvare le configurazioni. Verranno visualizzati avvisi che è possibile ignorare. 
     
    c. Riavviare i servizi Oozie e YARN come indicato dalle icone di notifica.

3. Impostare le variabili di ambiente seguenti nel nodo master, sostituendo i valori in base alle esigenze:

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Preparare la configurazione per i grafi

1. Creare un file di configurazione con i parametri di connessione di Azure Cosmos DB e le impostazioni di Spark e inserirlo in `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`.

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Aggiornare `spark.driver.extraClassPath` e `spark.executor.extraClassPath` in modo da includere la directory dei file JAR distribuiti nel passaggio precedente, in questo caso `/home/sshuser/azure-documentdb-spark/*`.

3. Specificare i dettagli seguenti per Azure Cosmos DB:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-the-tinkerpop-graph-and-save-it-to-azure-cosmos-db"></a>Caricare il grafo TinkerPop e salvarlo in Azure Cosmos DB
Per illustrare come è possibile salvare in modo permanente un grafo in Azure Cosmos DB, si usa come esempio il grafo modern predefinito di TinkerPop. Il grafo è stato archiviato in formato Kryo ed è disponibile nel repository TinkerPop.

1. Dato che si esegue Gremlin in modalità YARN, è necessario rendere disponibili i dati del grafo nel file system Hadoop. Usare i comandi seguenti per creare una directory e copiare al suo interno il file del grafo locale. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Aggiornare temporaneamente il file `gremlin-spark.properties` in modo da usare `GryoInputFormat` per la lettura del grafo. Indicare anche `inputLocation` come directory creata, come illustrato di seguito:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

3. Avviare la console Gremlin e creare questi passaggi di calcolo per salvare in modo permanente i dati nella raccolta Azure Cosmos DB configurata:  

    a. Creare il grafo: `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`.

    b. Usare SparkGraphComputer per la scrittura `graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`.

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

4. In Esplora dati è possibile verificare che i dati siano stati salvati in modo permanente in Azure Cosmos DB.

## <a name="load-the-graph-from-azure-cosmos-db-and-run-gremlin-queries"></a>Caricare il grafo da Azure Cosmos DB ed eseguire query Gremlin.

1. Per caricare il grafo, modificare `gremlin-spark.properties` per impostare `graphReader` su `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Caricare il grafo, attraversare i dati ed eseguire query Gremlin seguendo questa procedura:

    a. Avviare la console Gremlin `bin/gremlin.sh`.

    b. Creare il grafo con la configurazione `graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`.

    c. Creare un attraversamento del grafo con SparkGraphComputer `g = graph.traversal().withComputer(SparkGraphComputer)`.

    d. Eseguire le query Gremlin seguenti sul grafo:

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
    gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
    ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
    gremlin> g.V().count()
    ==>6
    gremlin> g.E().count()
    ==>6
    gremlin> g.V(1).out().values('name')
    ==>josh
    ==>vadas
    ==>lop
    gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
    ==>josh
    ==>peter
    ==>vadas
    ==>marko
    gremlin> g.V().hasLabel('person').
            choose(values('name')).
                option('marko', values('age')).
                option('josh', values('name')).
                option('vadas', valueMap()).
                option('peter', label())
    ==>josh
    ==>person
    ==>[name:[vadas],age:[27]]
    ==>29
    ```

> [!NOTE]
> Per visualizzare una registrazione più dettagliata, impostare un livello di log più dettagliato in `conf/log4j-console.properties`.
>

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come usare i grafi combinando Azure Cosmos DB e Spark.

> [!div class="nextstepaction"]

