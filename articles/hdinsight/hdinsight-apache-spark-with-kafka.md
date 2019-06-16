---
title: 'Streaming Apache Spark con Apache Kafka: Azure HDInsight'
description: Informazioni su come è possibile usare Apache Spark per trasmettere dati in streaming all'interno o all'esterno di Apache Kafka per mezzo di DStreams. In questo esempio i dati vengono trasmessi in streaming tramite un notebook Jupyter da Spark in HDInsight.
keywords: esempio kafka,kafka zookeeper,spark streaming kafka,esempio spark streaming kafka
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e0c39ae5f5c23ae0715ef1eee38b6dd34704538a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690953"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Esempio dello streaming Apache Spark (DStream) con Apache Kafka in HDInsight

Informazioni su come è possibile usare [Apache Spark](https://spark.apache.org/) per trasmettere dati in streaming all'interno o all'esterno di [Apache Kafka](https://kafka.apache.org/) su HDInsight per mezzo di [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Questo esempio usa un oggetto [Jupyter Notebook](https://jupyter.org/) che viene eseguito nel cluster Spark.

> [!NOTE]  
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Spark in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
>
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

> [!IMPORTANT]  
> In questo esempio viene usata la tecnologia DStreams, una tecnologia di streaming Spark meno recente. Per un esempio con funzionalità di streaming Spark più recenti, vedere il documento [Streaming strutturato Spark con Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Spark e Kafka si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Spark e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Anche se Kafka è limitato alle comunicazioni all'interno della rete virtuale, è possibile accedere ad altri servizi del cluster tramite Internet, ad esempio SSH e Ambari. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Anche se è possibile creare manualmente cluster Spark e Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire cluster Spark e Kafka e una rete virtuale di Azure nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile in **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Per garantire la disponibilità di Kafka in HDInsight, il cluster deve contenere almeno tre nodi del ruolo di lavoro. Questo modello crea un cluster Kafka contenente tre nodi di lavoro.

    Questo modello crea un cluster HDInsight 3.6 sia per Kafka che per Spark.

2. Usare le informazioni seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
   
    ![Distribuzione personalizzata di HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.

    * **Località**: scegliere una località geograficamente vicina.

    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Spark e Kafka. Ad esempio, se si immette **hdistreaming** viene creato un cluster Spark denominato __spark-hdistreaming__ e un cluster Kafka denominato **kafka-hdistreaming**.

    * **Nome utente dell'account di accesso del cluster**: nome utente amministratore per i cluster Spark e Kafka.

    * **Password dell'account di accesso del cluster**: password dell'utente amministratore per i cluster Spark e Kafka.

    * **Nome utente SSH**: utente SSH da creare per i cluster Spark e Kafka.

    * **Password SSH**: password dell'utente SSH per i cluster Spark e Kafka.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Infine selezionare **Acquisto**. La creazione dei cluster richiede circa 20 minuti.

Dopo avere create le risorse, verrà visualizzata una pagina di riepilogo.

![Riepilogo del gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]  
> Si noti che i nomi dei cluster HDInsight sono **spark-BASENAME** e **kafka-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="use-the-notebooks"></a>Usare i notebook

Il codice di esempio descritto in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Per completare questo esempio, seguire i passaggi nel file `README.md`.

## <a name="delete-the-cluster"></a>Eliminare il cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Le procedure illustrate in questo documento creano entrambi i cluster nello stesso gruppo di risorse di Azure. È quindi possibile eliminare il gruppo di risorse dal portale di Azure. In questo modo vengono rimosse tutte le risorse create seguendo le istruzioni di questo documento, la rete virtuale di Azure e l'account di archiviazione usato dai cluster.

## <a name="next-steps"></a>Passaggi successivi

Questo esempio ha illustrato l'uso di Spark per leggere e scrivere in Kafka. Per trovare altri modi per lavorare con Kafka, vedere i collegamenti seguenti:

* [Introduzione ad Apache Kafka (anteprima) in HDInsight](kafka/apache-kafka-get-started.md)
* [Usare MirrorMaker per creare una replica di Apache Kafka in HDInsight](kafka/apache-kafka-mirroring.md)
* [Usare Apache Storm con Apache Kafka in HDInsight](hdinsight-apache-storm-with-kafka.md)

