---
title: Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito | Microsoft Docs
description: Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito
services: hdinsight
documentationcenter: 
author: xiaoyongzhu
manager: asadk
editor: cgronlun
tags: azure-portal
ms.assetid: 71dcd1ad-4cad-47ad-8a9d-dcb7fa3c2ff9
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2017
ms.author: xiaoyzhu
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 14b7808c9534bce3049422d6bce1e8914b2c2fbc
ms.contentlocale: it-it
ms.lasthandoff: 03/25/2017

---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Usare Caffe in Azure HDInsight Spark per l'apprendimento avanzato distribuito


## <a name="introduction"></a>Introduzione

Gli effetti dell'apprendimento avanzato si possono rintracciare in ogni campo, dalla sanità ai trasporti, fino al settore manifatturiero. Le aziende si affidano all'apprendimento avanzato per risolvere problemi quali la [classificazione di immagini](http://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), il [riconoscimento vocale](http://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), il riconoscimento di oggetti e la traduzione automatica. 

A questo scopo vengono usati [diversi framework molto diffusi](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), tra cui [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), MXNet, Theano e così via. Caffe è un framework per reti neurali non simbolico (imperativo) tra i più famosi ed è largamente usato in molti campi, tra cui la visione artificiale. [CaffeOnSpark](http://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) unisce Caffe e Apache Spark e permette di usare facilmente l'apprendimento avanzato in un cluster Hadoop esistente insieme a pipeline ETL Spark, riducendo così la latenza e la complessità del sistema per l'apprendimento end-to-end.

[HDInsight](https://azure.microsoft.com/en-us/services/hdinsight/) è l'unica soluzione Hadoop cloud completamente gestita che fornisce cluster di analisi open source ottimizzati per Spark, Hive, MapReduce, HBase, Storm, Kafka ed R Server, con un contratto di servizio che garantisce la disponibilità al 99,9%. Ognuna di queste tecnologie di Big Data e applicazioni ISV consente una semplice distribuzione come cluster gestito, con funzionalità di sicurezza e monitoraggio di livello aziendale.

Le informazioni richieste dagli utenti sull'uso dell'apprendimento avanzato in HDInsight, ovvero il prodotto Hadoop PaaS di Microsoft, saranno presto disponibili. Questo articolo offre invece un riepilogo, sotto forma di blog tecnico, sull'uso di Caffe in HDInsight Spark.

L'installazione del framework Caffe può risultare piuttosto difficile. Viene illustrato prima di tutto come installare [Caffe in Spark](https://github.com/yahoo/CaffeOnSpark) per un cluster HDInsight. Successivamente, si usa la demo MNIST incorporata per dimostrare come usare l'apprendimento avanzato distribuito con HDInsight Spark nelle CPU.

Per l'uso in HDInsight è necessario eseguire questi quattro passaggi principali:

1. Installare le dipendenze necessarie in tutti i nodi.
2. Compilare Caffe in Spark per HDInsight nel nodo head.
3. Distribuire le librerie necessarie in tutti i nodi del ruolo di lavoro.
4. Creare un modello di Caffe ed eseguirlo in modalità distribuita.

HDInsight è una soluzione PaaS che offre ottime funzionalità di piattaforma. Alcune attività risultano quindi molto semplici da eseguire. Una delle funzionalità più usate in questo articolo è l'[azione script](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux), che permette di eseguire i comandi della shell per personalizzare i nodi del cluster, ovvero il nodo head, il nodo del ruolo di lavoro e il nodo perimetrale.

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Passaggio 1: Installare le dipendenze necessarie in tutti i nodi

Per iniziare, occorre installare le dipendenze necessarie. Il sito di Caffe e il [sito di CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) offrono alcuni wiki molti utili per l'installazione delle dipendenze per Spark in modalità YARN, ovvero la modalità per HDInsight Spark, ma è necessario aggiungere altre dipendenze per la piattaforma HDInsight. L'azione script verrà usata come indicato di seguito ed eseguita in tutti i nodi head e i nodi del ruolo di lavoro. L'esecuzione dell'azione script richiede circa 20 minuti, perché le dipendenze dipendono anche da altri pacchetti. Inserirla in un percorso che sia accessibile al cluster HDInsight, ad esempio l'account di archiviazione BLOB predefinito o un percorso GitHub.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in http://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose we install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


L'azione script precedente prevede due passaggi. Il primo passaggio consiste nell'installare tutte le librerie necessarie. Sono incluse le librerie necessarie sia per la compilazione di Caffe, ad esempio gflags e glog, che per la sua esecuzione, ad esempio numpy. Per l'ottimizzazione della CPU si userà libatlas, ma è anche possibile seguire il wiki di CaffeOnSpark sull'installazione di altre librerie di ottimizzazione, come MKL o CUDA per GPU.

Il secondo passaggio consiste nello scaricare, compilare e installare protobuf 2.5.0 per Caffe durante la fase di esecuzione. Protobuf 2.5.0 [è necessario](https://github.com/yahoo/CaffeOnSpark/issues/87), tuttavia questa versione non è disponibile come pacchetto in Ubuntu 16. È quindi necessario compilarlo dal codice sorgente. In Internet è anche possibile reperire alcune risorse che illustrano come compilarlo, ad esempio [questa](http://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Per iniziare direttamente, è possibile eseguire l'azione script nel cluster per tutti i nodi del ruolo di lavoro e i nodi head, per HDInsight 3.5. Le azioni script per un cluster possono essere eseguite quando questo è in esecuzione oppure durante la fase di provisioning del cluster. Per informazioni dettagliate sulle azioni script, vedere la documentazione [qui](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#view-history-promote-and-demote-script-actions).

![Azioni script per l'installazione delle dipendenze](./media/hdinsight-deep-learning-caffe-spark/Script-Action-1.png)


## <a name="step-2-build-caffe-on-spark-for-hdinsight-on-the-head-node"></a>Passaggio 2: Compilare Caffe in Spark per HDInsight nel nodo head

Il secondo passaggio consiste nel compilare Caffe nel nodo head e quindi distribuire le librerie compilate in tutti i nodi del ruolo di lavoro. In questo passaggio è necessario accedere tramite [SSH al nodo head](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) e quindi seguire il [processo di compilazione di CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). Di seguito è riportato lo script che è possibile usare per compilare CaffeOnSpark con alcuni passaggi aggiuntivi. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Potrebbe essere necessario eseguire operazioni aggiuntive rispetto a quanto indicato nella documentazione di CaffeOnSpark. È necessario apportare le modifiche seguenti:
- Passare alla sola CPU e usare libatlas per questo scopo specifico.
- Inserire i set di dati nell'archivio BLOB, che offre un percorso condiviso accessibile a tutti i nodi del ruolo di lavoro per un uso successivo.
- Inserire le librerie Caffe compilate nell'archivio BLOB e copiarle successivamente in tutti i nodi usando le azioni script, per evitare tempi di compilazione aggiuntivi.


### <a name="troubleshooting-an-ant-buildexception-has-occured-exec-returned-2"></a>Risoluzione dei problemi: eccezione Ant BuildException ed esecuzione che restituisce 2

Quando si tenta per la prima volta di compilare CaffeOnSpark, si può ricevere il messaggio di errore seguente:

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occured: exec returned: 2

Per risolvere questo problema è sufficiente pulire il repository del codice con il comando "make clean" e quindi eseguire il comando "make build", purché le dipendenze siano corrette.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Risoluzione dei problemi: timeout della connessione al repository Maven

A volte, Maven restituisce un errore di timeout della connessione simile al seguente:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Il problema si risolve dopo un'attesa di alcuni minuti e poi è sufficiente provare a ricompilare il codice. È possibile che Maven limiti in qualche modo il traffico da un determinato indirizzo IP.


### <a name="troubleshooting-test-failure-for-caffe"></a>Risoluzione dei problemi: test non superato per Caffe

Durante il controllo finale per CaffeOnSpark, potrebbe essere visualizzato un errore di test non superato simile al seguente. Si tratta probabilmente di un problema legato alla codifica UTF-8, ma non dovrebbe influire sull'utilizzo di Caffe.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Passaggio 3: Distribuire le librerie necessarie in tutti i nodi del ruolo di lavoro

Il passaggio successivo consiste nel distribuire in tutti i nodi le librerie disponibili in CaffeOnSpark/caffe-public/distribute/lib/ and CaffeOnSpark/caffe-distri/distribute/lib/. Nel passaggio 2 tali librerie sono state inserite nell'archivio BLOB. In questo passaggio si usano le azioni script per copiarlo in tutti i nodi head e i nodi del ruolo di lavoro.

A tale scopo, è sufficiente eseguire un'azione di script come indicato di seguito, puntando al percorso specifico del cluster:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Dato che nel passaggio 2 le librerie sono state inserite nell'archivio BLOB, che è accessibile a tutti i nodi, in questo passaggio è sufficiente copiarlo in tutti i nodi.

## <a name="step-4-compose-a-caffe-model-and-run-it-distributely"></a>Passaggio 4: Creare un modello di Caffe ed eseguirlo in modalità distribuita

Dopo aver eseguito i passaggi precedenti, Caffe è installato nel nodo head ed è possibile procedere. Il passaggio successivo consiste nello scrivere un modello di Caffe. 

Caffe fa uso di un'architettura espressiva in cui, per creare un modello, è sufficiente definire un file di configurazione, nella maggior parte dei casi senza alcuna codifica. Di seguito viene illustrato il modello nel dettaglio. 

Il modello di cui si esegue il training in questo articolo è un modello di esempio per il training di MNIST. Il database MNIST di cifre scritte a mano ha un set di training di 60.000 esempi e un set di test di 10.000 esempi. Si tratta di un subset di un set più grande disponibile da NIST. Le dimensioni delle cifre sono state normalizzate e le cifre sono state inserite al centro in un'immagine di dimensioni fisse. CaffeOnSpark offre diversi script per scaricare il set di dati e convertirlo nel formato corretto.

CaffeOnSpark include alcuni esempi di topologia di rete per il training di MNIST. Suddivide in modo utile l'architettura di rete, ovvero la topologia della rete, e ne consente l'ottimizzazione. In questo caso sono necessari i due file descritti di seguito. 

Il file "solver" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) viene usato per supervisionare l'ottimizzazione e per generare aggiornamenti dei parametri. Indica ad esempio se usare la CPU o la GPU, il momento, il numero di iterazioni e così via, oltre a definire quale topologia di rete neurale debba essere usata dal programma, ovvero il secondo file necessario. Per altre informazioni sul file "solver", vedere la [documentazione di Caffe](http://caffe.berkeleyvision.org/tutorial/solver.html).

Dato che in questo esempio si usa la CPU anziché la GPU, occorre modificare l'ultima riga come indicato di seguito:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Configurazione di Caffe](./media/hdinsight-deep-learning-caffe-spark/Caffe-1.png)

È possibile modificare le altre righe in base alle esigenze.

Il secondo file (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) definisce l'aspetto della rete neurale e il relativo file di input e di output. Il file deve essere aggiornato in base al percorso dei dati di training. Nel file lenet_memory_train_test.prototxtt modificare la parte seguente, puntando al percorso specifico del cluster:

- Modificare "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" in "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb".
- Modificare "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" in "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb".

![Configurazione di Caffe](./media/hdinsight-deep-learning-caffe-spark/Caffe-2.png)

Per altre informazioni su come definire la rete, vedere la [documentazione di Caffe sul set di dati MNIST](http://caffe.berkeleyvision.org/gathered/examples/mnist.html).

Ai fini di questo articolo, viene usato questo semplice esempio di MNIST. Eseguire il comando seguente dal nodo head:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

Il comando distribuisce i file lenet_memory_solver.prototxt e lenet_memory_train_test.prototxt necessari a tutti i contenitori YARN e imposta il relativo percorso di ogni driver o executor Spark su LD_LIBRARY_PATH, definito nel frammento di codice precedente, che punta al percorso delle librerie di CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi

Nella modalità cluster YARN usata il driver Spark viene pianificato in un contenitore arbitrario e un nodo del ruolo di lavoro arbitrario. L'output visualizzato nella console dovrebbe quindi essere simile al seguente:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Per conoscere i dettagli, in genere è necessario ottenere il log del driver Spark, che contiene altre informazioni. In questo caso è necessario passare all'interfaccia utente di YARN per trovare i relativi log. L'interfaccia utente di YARN è disponibile all'URL seguente: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![Interfaccia utente di Yarn](./media/hdinsight-deep-learning-caffe-spark/YARN-UI-1.png)

È possibile esaminare il numero di risorse allocate per questa applicazione specifica. Facendo clic sul collegamento "Scheduler" (Utilità di pianificazione) si noterà che per questa applicazione vengono eseguiti nove contenitori. È stato chiesto a YARN di fornire otto executor, il nono contenitore è destinato al processo del driver. 

![Utilità di pianificazione di YARN](./media/hdinsight-deep-learning-caffe-spark/YARN-Scheduler.png)

In caso di errore è consigliabile controllare i log del driver o del contenitore. Per i log del driver, è possibile fare clic sull'ID applicazione nell'interfaccia utente di YARN e quindi fare clic sul pulsante "Logs" (Log). I log del driver sono scritti in STDERR.

![Interfaccia utente 2 di YARN](./media/hdinsight-deep-learning-caffe-spark/YARN-UI-2.png)

Ad esempio, l'errore seguente nei log del driver indica che sono stati allocati troppi executor.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

In alcuni casi il problema si può verificare negli executor anziché nei driver. In tal caso è necessario controllare i log del contenitore. È sempre possibile ottenere il log del contenitore del driver e quindi il contenitore in stato di errore. Ad esempio, durante l'esecuzione di Caffe si può verificare questo errore:

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

In tal caso è necessario ottenere l'ID del contenitore in stato di errore. Nell'esempio precedente si tratta di container_1485916338528_0008_05_000005. Quindi è necessario eseguire 

    yarn logs -containerId container_1485916338528_0008_03_000005

dal nodo head. L'errore del contenitore è dovuto all'uso della modalità GPU in lenet_memory_solver.prototxt, che invece prevede l'uso della modalità CPU.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Risultati

Con l'allocazione di otto executor in una topologia di rete così semplice, saranno sufficienti circa 30 minuti per ottenere i risultati. Dalla riga di comando si noterà che il modello è stato inserito in wasb:///mnist.model e i risultati sono stati inseriti in una cartella denominata wasb:///mnist_features_result.

Per ottenere i risultati, eseguire:

    hadoop fs -cat hdfs:///mnist_features_result/*

I risultati avranno un aspetto simile al seguente:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID rappresenta l'ID nel set di dati MNIST e l'etichetta è il numero identificato dal modello.


## <a name="conclusion"></a>Conclusione

Questo documento ha illustrato come installare CaffeOnSpark tramite l'esecuzione di un semplice esempio. HDInsight è una piattaforma cloud di calcolo distribuito completamente gestita. È la soluzione ideale per l'esecuzione di carichi di lavoro di apprendimento automatico e analisi avanzata in set di dati di grandi dimensioni, nonché per l'apprendimento avanzato distribuito. Per l'esecuzione di attività di apprendimento avanzato è possibile usare Caffe in HDInsight Spark.


## <a name="seealso"></a>Vedere anche
* [Panoramica: Apache Spark su Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Scenari
* [Spark con Machine Learning: usare Spark in HDInsight per l'analisi della temperatura di compilazione tramite dati HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Machine Learning: usare Spark in HDInsight per prevedere i risultati del controllo degli alimenti](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Gestire risorse
* [Gestire le risorse del cluster Apache Spark in Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


