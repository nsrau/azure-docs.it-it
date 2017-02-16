---
title: Usare Apache Spark con Kafka in Azure HDInsight | Documentazione Microsoft
description: Informazioni sull&quot;uso di Spark in HDInsight per leggere e scrivere dati in un cluster Kafka in HDInsight. Questo esempio si serve di Scala in un notebook di Jupyter per scrivere dati casuali in Kafka in HDInsight e poi leggerli usando lo streaming Spark.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 52692a21671b48581bba348455f6902935a08fa8

---
# <a name="use-apache-spark-with-kafka-preview-on-hdinsight"></a>Usare Apache Spark con Kafka (anteprima) in HDInsight

È possibile usare Apache Spark per trasmettere dati all'interno o all'esterno di Apache Kafka. Questo documento illustra come trasmettere dati all'interno o all'esterno di Kafka usando un notebook di Jupyter da Spark in HDInsight.

> [!NOTE]
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Spark in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
> 
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure

* Un client SSH, perché sono necessari i comandi `ssh` e `scp`. Per altre informazioni sull'uso di SSH con HDInsight, vedere i documenti seguenti:

    * [Usare SSH con HDInsight basato su Linux da Linux, Unix e Mac OS](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usare SSH con HDInsight basato su Linux da Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

* [cURL](https://curl.haxx.se/): utilità multipiattaforma per l'esecuzione di richieste HTTP.

* [jq](https://stedolan.github.io/jq/): utilità multipiattaforma per l'analisi di documenti JSON.

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Spark e Kafka si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Spark e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Anche se Kafka è limitato alle comunicazioni all'interno della rete virtuale, è possibile accedere ad altri servizi del cluster tramite Internet, ad esempio SSH e Ambari. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Anche se è possibile creare manualmente cluster Spark e Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire cluster Spark e Kafka e una rete virtuale di Azure nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile all'indirizzo **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet.json**.

2. Usare le informazioni seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
   
    ![Distribuzione personalizzata di HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.

    * **Località**: scegliere una località geograficamente vicina. La località deve corrispondere a quella indicata nella sezione __IMPOSTAZIONI__.

    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Spark e Kafka. Ad esempio, se si immette **hdi** viene creato un cluster Spark denominato spark-hdi e un cluster Kafka denominato **kafka-hdi**.

    * **Cluster Login User Name** (Nome utente di accesso del cluster): nome utente amministratore per i cluster Spark e Kafka.

    * **Cluster Login Password** (Password di accesso del cluster): password amministratore per i cluster Spark e Kafka.

    * **SSH User Name** (Nome utente SSH): utente SSH da creare per i cluster Spark e Kafka.

    * **SSH Password** (Password SSH): password dell'utente SSH per i cluster Spark e Kafka.

    * **Location** (Località): area in cui vengono creati i cluster.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

Dopo aver creato le risorse, si viene reindirizzati a un pannello del gruppo di risorse che contiene i cluster e il dashboard Web.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **spark-BASENAME** e **kafka-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="get-the-code"></a>Ottenere il codice

Il codice per l'esempio illustrato in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

## <a name="understand-the-code"></a>Informazioni sul codice

Questo esempio si serve di un'applicazione Scala in un notebook di Jupyter. Il codice nel notebook si basa sui dati seguenti:

* __Broker Kafka__: il processo broker viene eseguito in ogni nodo del ruolo di lavoro del cluster Kafka. L'elenco dei broker è necessario per il componente producer, che scrive dati in Kafka.

* __Host Zookeeper__: gli host Zookeeper per il cluster Kafka vengono usati durante l'utilizzo, ovvero la lettura, di dati da Kafka.

* __Nome dell'argomento__: nome dell'argomento in cui vengono scritti dati e da cui vengono letti. Questo esempio prevede un argomento denominato `sparktest`.

Per sapere come ottenere informazioni sul broker Kafka e l'host Zookeeper, vedere la sezione [Informazioni sull'host Kafka](#kafkahosts).

Il codice nel notebook esegue queste attività:

* Crea un consumer che legge i dati da un argomento Kafka denominato `sparktest`, conta ogni parola nei dati e archivia parola e conteggio in una tabella temporanea denominata `wordcounts`.

* Crea un producer che scrive frasi casuali nell'argomento Kafka denominato `sparktest`.

* Seleziona dati dalla tabella `wordcounts` per visualizzare i conteggi.

Ogni cella del progetto contiene commenti o una sezione di testo che illustra le operazioni eseguite dal codice.

##<a name="a-idkafkahostsakafka-host-information"></a><a id="kafkahosts"></a>Informazioni sull'host Kafka

La prima operazione da eseguire quando si crea un'applicazione compatibile con Kafka in HDInsight è ottenere informazioni sul broker Kafka e gli host Zookeeper per il cluster Kafka. Questo viene usato dalle applicazioni client per comunicare con Kafka.

> [!NOTE]
> Il broker Kafka e gli host Zookeeper non sono accessibili direttamente tramite Internet. Qualsiasi applicazione che usa Kafka deve essere eseguita nel cluster Kafka o all'interno della stessa rete virtuale di Azure del cluster Kafka. In questo caso l'esempio viene eseguito in un cluster Spark in HDInsight nella stessa rete virtuale.

Dall'ambiente di sviluppo usare i comandi seguenti per recuperare le informazioni sul broker e su Zookeeper. Sostituire __PASSWORD__ con la password di accesso (amministratore) usata durante la creazione del cluster. Sostituire __BASENAME__ con il nome di base usato durante la creazione del cluster.

* Per ottenere informazioni sul __broker Kafka__:

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'

    > [!IMPORTANT]
    > Quando si usa questo comando da Windows PowerShell, si potrebbe ricevere un messaggio di errore sulla shell racchiusa tra virgolette. In tal caso, usare il comando seguente: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")'

* Per ottenere informazioni sull'__host Zookeeper__:

        curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")'
    
    > [!IMPORTANT]
    > Quando si usa questo comando da Windows PowerShell, si potrebbe ricevere un messaggio di errore sulla shell racchiusa tra virgolette. In tal caso, usare il comando seguente: `curl -u admin:PASSWORD -G "https://kafka-BASENAME.azurehdinsight.net/api/v1/clusters/kafka-BASENAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")'`

Entrambi i comandi restituiscono informazioni simili al testo seguente:

* __Broker Kafka__: `wn0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092,wn1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:9092`

* __Host Zookeeper__: `zk0-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk1-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181,zk2-kafka.4rf4ncirvydube02fuj0gpxp4e.ex.internal.cloudapp.net:2181`

> [!IMPORTANT]
> Salvare queste informazioni, perché vengono usate in diversi passaggi di questo documento.

## <a name="use-the-jupyter-notebook"></a>Usare il notebook di Jupyter

Per usare il notebook di Jupyter di esempio è necessario caricarlo nel server Jupyter Notebook nel cluster Spark. Per caricare notebook, seguire questa procedura:

1. Nel Web browser usare l'URL seguente per connettersi al server Jupyter Notebook nel cluster Spark. Sostituire __BASENAME__ con il nome di base usato durante la creazione del cluster.

        https://spark-BASENAME.azurehdinsight.net/jupyter

    Quando richiesto, immettere l'account di accesso (amministratore) e la password usati durante la creazione del cluster.

2. Usare il pulsante __Upload__ (Carica) in alto a destra nella pagina per caricare il file `KafkaStreaming.ipynb`. Selezionare il file nella finestra di dialogo del browser file e scegliere __Open__ (Apri). 

    ![Usare il pulsante di caricamento per selezionare e caricare un notebook](./media/hdinsight-apache-spark-with-kafka/upload-button.png)

    ![Selezionare il file KafkaStreaming.ipynb](./media/hdinsight-apache-spark-with-kafka/select-notebook.png)

3. Trovare la voce __KafkaStreaming.ipynb__ nell'elenco dei notebook e fare clic sul pulsante __Upload__ (Carica) accanto alla voce.

    ![Usare il pulsante di caricamento accanto alla voce KafkaStreaming.ipynb per eseguire il caricamento nel server Jupyter Notebook](./media/hdinsight-apache-spark-with-kafka/upload-notebook.png)

4. Dopo aver caricato il file, selezionare la voce __KafkaStreaming.ipynb__ per aprire il notebook. Per completare questo esempio, seguire le istruzioni contenute nel notebook.

## <a name="delete-the-cluster"></a>Eliminazione del cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. In questo modo vengono rimosse tutte le risorse create seguendo le istruzioni di questo documento, la rete virtuale di Azure e l'account di archiviazione usato dai cluster.

## <a name="next-steps"></a>Passaggi successivi

Questo documento ha illustrato l'uso di Spark per leggere e scrivere in Kafka. Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Introduzione ad Apache Kafka (anteprima) in HDInsight](hdinsight-apache-kafka-get-started.md)
* [Usare MirrorMaker per creare una replica di Kafka in HDInsight](hdinsight-apache-kafka-mirroring.md)
* [Usare Apache Storm (anteprima) con Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)




<!--HONumber=Jan17_HO4-->


