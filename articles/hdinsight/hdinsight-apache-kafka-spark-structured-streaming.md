---
title: 'Streaming strutturato Apache Spark con Kafka: Azure HDInsight | Microsoft Docs'
description: Informazioni su come è possibile usare lo streaming Apache Spark (DStream) per trasmettere dati all'interno o all'esterno di Apache Kafka. In questo esempio i dati vengono trasmessi in streaming tramite un notebook Jupyter da Spark in HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Usare lo streaming strutturato Spark con Kafka in HDInsight

Informazioni su come usare lo streaming strutturato Spark per leggere i dati da Apache Kafka in Azure HDInsight.

Lo streaming strutturato Spark è un motore di elaborazione del flusso basato su Spark SQL. Consente di esprimere i calcoli di streaming come il calcolo di batch in dati statici. Per altre informazioni sullo streaming strutturato, vedere la pagina [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) di Apache.org.

> [!IMPORTANT]
> Questo esempio usa Spark 2.2 su HDInsight 3.6.
>
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Spark in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
>
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Qualunque elemento che usa Kafka deve trovarsi nella stessa rete virtuale di Azure. In questa esercitazione i cluster Kafka e Spark si trovano nella stessa rete virtuale di Azure. 

Il diagramma seguente illustra il flusso delle comunicazioni tra Spark e Kafka:

![Diagramma dei cluster Spark e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Il servizio Kafka è limitato alle comunicazioni all'interno della rete virtuale. Altri servizi nel cluster, ad esempio SSH e Ambari, sono accessibili tramite Internet. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Per praticità, la procedura seguente usa un modello di Azure Resource Manager per creare cluster Kafka e Spark in una rete virtuale.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile in **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Questo modello crea le risorse seguenti:

    * Kafka nel cluster HDInsight 3.6.
    * Spark 2.2.0 nel cluster HDInsight 3.6.
    * Una rete virtuale Azure contenente i cluster HDInsight.

    > [!IMPORTANT]
    > Il notebook di streaming strutturato che è stato usato in questo esempio richiede Spark in HDInsight 3.6. Se si usa una versione precedente di Spark in HDInsight, si ricevono errori durante l'uso del notebook.

2. Usare le informazioni seguenti per popolare le voci nella sezione **Modello personalizzato**:

    | Impostazione | Valore |
    | --- | --- |
    | Sottoscrizione | Sottoscrizione di Azure |
    | Gruppo di risorse | Gruppo di risorse che contiene le risorse. |
    | Località | Area di Azure in cui vengono create le risorse. |
    | Nome del cluster Spark | Nome del cluster Spark. |
    | Nome del cluster Kafka | Nome del cluster Kafka. |
    | Nome utente dell'account di accesso del cluster | Nome utente dell'amministratore per i cluster. |
    | Password di accesso al cluster | Password dell'utente amministratore per i cluster. |
    | Nome utente SSH | Utente SSH da creare per i cluster. |
    | Password SSH | Password per l'utente SSH. |
   
    ![Screenshot del modello personalizzato](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. 

> [!NOTE]
> La creazione dei cluster può richiedere fino a 20 minuti.

## <a name="get-the-notebook"></a>Ottenere il notebook

Il codice di esempio descritto in questo documento è disponibile all'indirizzo [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Caricare i notebook

Per caricare il notebook dal progetto nel cluster Spark su HDInsight, seguire questa procedura:

1. Nel Web browser connettersi al notebook Jupyter nel cluster Spark. Nell'URL seguente sostituire `CLUSTERNAME` con il nome del cluster __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando richiesto, immettere l'account di accesso (amministratore) e la password usati durante la creazione del cluster.

2. Usare il pulsante __Carica__ in alto a destra nella pagina per caricare il file __spark-structured-streaming-kafka.ipynb__ nel cluster. Selezionare __Apri__ per avviare il caricamento.

    ![Usare il pulsante di caricamento per selezionare e caricare un notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Selezionare il file KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Trovare la voce __spark-structured-streaming-kafka.ipynb__ nell'elenco dei notebook e selezionare il pulsante __Carica__ accanto.

    ![Per caricare Notebook, usare il pulsante di caricamento accanto alla voce KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Usare il notebook

Dopo aver caricato i file, selezionare la voce __spark-structured-streaming-kafka.ipynb__ per aprire il notebook. Per informazioni sull'uso dello streaming strutturato di Spark con Kafka su HDInsight, seguire le istruzioni disponibili nel notebook.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create da questa esercitazione, eliminare il gruppo di risorse. Se si elimina il gruppo di risorse, vengono eliminati anche il cluster HDInsight associato e tutte le altre risorse correlate al gruppo di risorse.

Per rimuovere il gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e quindi scegliere __Gruppi di risorse__ per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e quindi fare clic con il pulsante destro del mouse su __Altro__ (...) a destra dell'elenco.
3. Scegliere __Elimina gruppo di risorse__ e quindi confermare.

> [!WARNING]
> La fatturazione del cluster HDInsight inizia dopo la creazione del cluster e si interrompe solo quando questo viene eliminato. La fatturazione avviene con tariffa oraria, perciò si deve sempre eliminare il cluster in uso quando non lo si usa più.
> 
> Se si elimina un cluster Kafka su HDInsight vengono eliminati anche eventuali dati archiviati in Kafka.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare lo streaming strutturato Spark, vedere i documenti seguenti per trovare altre informazioni su come lavorare con Spark e Kafka:

* [Come usare lo streaming Spark (DStream) con Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniziare a usare un notebook Jupyter e Spark in HDInsight](spark/apache-spark-jupyter-spark-sql.md)