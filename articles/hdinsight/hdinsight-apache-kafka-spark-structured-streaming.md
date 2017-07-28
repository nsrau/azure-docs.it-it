---
title: 'Streaming strutturato Apache Spark con Kafka: Azure HDInsight | Microsoft Docs'
description: "Informazioni su come è possibile usare lo streaming Apache Spark (DStream) per trasmettere dati all&quot;interno o all&quot;esterno di Apache Kafka. In questo esempio i dati vengono trasmessi in streaming tramite un notebook Jupyter da Spark in HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/09/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: 02b49e13e8f54c3d55310f4d2b21c7e09c91fe81
ms.contentlocale: it-it
ms.lasthandoff: 06/16/2017

---

# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>Usare lo streaming strutturato Spark con Kafka (anteprima) in HDInsight

Informazioni su come usare lo streaming strutturato Spark per leggere i dati da Apache Kafka in Azure HDInsight.

Lo streaming strutturato Spark è un motore di elaborazione del flusso basato su Spark SQL. Consente di esprimere i calcoli di streaming come il calcolo di batch in dati statici. Per altre informazioni sullo streaming strutturato, vedere la pagina [Structured Streaming Programming Guide [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) di Apache.org.

> [!IMPORTANT]
> In questo esempio viene usato Spark 2.1 in HDInsight 3.6. Lo streaming strutturato è considerato __alfa__ in Spark 2.1.
>
> La procedura descritta in questo documento permette di creare un gruppo di risorse di Azure che contiene sia un cluster Spark in HDInsight che un cluster Kafka in HDInsight. Entrambi questi cluster si trovano all'interno di una rete virtuale di Azure, che consente al cluster Spark di comunicare direttamente con il cluster Kafka.
>
> Al termine della procedura descritta in questo documento, eliminare i cluster per evitare costi supplementari.

## <a name="create-the-clusters"></a>Creare i cluster

Apache Kafka in HDInsight non fornisce l'accesso ai broker Kafka tramite Internet pubblico. Tutto ciò che comunica con Kafka deve trovarsi nella stessa rete virtuale di Azure dei nodi del cluster Kafka. Per questo esempio, i cluster Spark e Kafka si trovano entrambi in una rete virtuale di Azure. Il diagramma seguente illustra il flusso delle comunicazioni tra i cluster:

![Diagramma dei cluster Spark e Kafka in una rete virtuale di Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Il servizio Kafka è limitato alle comunicazioni all'interno della rete virtuale. Altri servizi nel cluster, ad esempio SSH e Ambari, sono accessibili tramite Internet. Per altre informazioni sulle porte pubbliche disponibili con HDInsight, vedere [Porte e URI usati da HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Anche se è possibile creare manualmente cluster Spark e Kafka e una rete virtuale di Azure, è più semplice usare un modello di Azure Resource Manager. Seguire questa procedura per distribuire cluster Spark e Kafka e una rete virtuale di Azure nella sottoscrizione di Azure.

1. Usare il pulsante seguente per accedere ad Azure e aprire il modello nel portale di Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Il modello di Azure Resource Manager è disponibile su **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    Questo modello crea le risorse seguenti:

    * Kafka nel cluster HDInsight 3.5.
    * Spark nel cluster HDInsight 3.6.
    * Una rete virtuale Azure contenente i cluster HDInsight.

    > [!IMPORTANT]
    > Il notebook di streaming strutturato che è stato usato in questo esempio richiede Spark in HDInsight 3.6. Se si usa una versione precedente di Spark in HDInsight, si ricevono errori durante l'uso del notebook.

2. Usare le informazioni seguenti per popolare le voci nel pannello **Distribuzione personalizzata**:
   
    ![Distribuzione personalizzata di HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Gruppo di risorse**: creare un gruppo o selezionarne uno esistente. Questo gruppo contiene il cluster HDInsight.

    * **Località**: scegliere una località geograficamente vicina.

    * **Base Cluster Name** (Nome di base del cluster): questo valore viene usato come nome di base per i cluster Spark e Kafka. Ad esempio, se si immette **hdi** viene creato un cluster Spark denominato spark-hdi e un cluster Kafka denominato **kafka-hdi**.

    * **Cluster Login User Name** (Nome utente di accesso del cluster): nome utente amministratore per i cluster Spark e Kafka.

    * **Cluster Login Password** (Password di accesso del cluster): password amministratore per i cluster Spark e Kafka.

    * **SSH User Name** (Nome utente SSH): utente SSH da creare per i cluster Spark e Kafka.

    * **SSH Password** (Password SSH): password dell'utente SSH per i cluster Spark e Kafka.

3. Leggere le **Condizioni** e quindi selezionare **Accetto le condizioni riportate sopra**.

4. Selezionare infine **Aggiungi al dashboard** e quindi **Acquista**. La creazione dei cluster richiede circa 20 minuti.

Dopo avere creato le risorse, si viene reindirizzati al pannello del gruppo di risorse.

![Pannello Gruppo di risorse per la rete virtuale e i cluster](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Si noti che i nomi dei cluster HDInsight sono **spark-BASENAME** e **kafka-BASENAME**, dove BASENAME è il nome specificato per il modello. Questi nomi verranno usati nei passaggi successivi per la connessione ai cluster.

## <a name="get-the-kafka-brokers"></a>Recupero dei broker Kafka

Il codice in questo esempio consente di connettersi agli host dei broker Kafka nel cluster Kafka. Per trovare gli host dei broker Kafka, usare l'esempio seguente di PowerShell o Bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin:$PASSWORD -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")'
```

> [!NOTE]
> Questo esempio prevede che `$PASSWORD` contenga la password per l'accesso al cluster e `$CLUSTERNAME` per contenere il nome del cluster Kafka.
>
> Questo esempio usa l'utilità [jq](https://stedolan.github.io/jq/) per analizzare i dati del documento JSON.

L'output è simile al testo seguente:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn2-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn3-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Salvare queste informazioni, perché vengono usate nelle sezioni seguenti di questo documento.

## <a name="get-the-notebooks"></a>Ottenere i notebook

Il codice per l'esempio illustrato in questo documento è disponibile su [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Caricare i notebook

Usare i passaggi seguenti per caricare i notebook dal progetto a Spark nel cluster HDInsight:

1. Nel Web browser connettersi al notebook Jupyter nel cluster Spark. Nell'URL seguente sostituire `CLUSTERNAME` con il nome del cluster Kafka:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando richiesto, immettere l'account di accesso (amministratore) e la password usati durante la creazione del cluster.

2. Usare il pulsante __Carica__ in alto a destra nella pagina per caricare il file __Stream-Tweets-To_Kafka.ipynb__ nel cluster. Selezionare __Apri__ per avviare il caricamento.

    ![Usare il pulsante di caricamento per selezionare e caricare un notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Selezionare il file KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Trovare la voce __Stream-Tweets-To_Kafka.ipynb__ nell'elenco dei notebook e selezionare il pulsante __Carica__ accanto.

    ![Usare il pulsante di caricamento accanto alla voce KafkaStreaming.ipynb per eseguire il caricamento nel server Jupyter Notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. Ripetere i passaggi da 1 a 3 per caricare il notebook __Spark-Structured-Streaming-From-Kafka.ipynb__.

## <a name="load-tweets-into-kafka"></a>Caricamento di tweet in Kafka

Dopo che i file sono stati caricati, selezionare la voce __Stream-Tweets-To_Kafka.ipynb__ per aprire il notebook. Seguire i passaggi del notebook per caricare i tweet in Kafka.

## <a name="process-tweets-using-spark-structured-streaming"></a>Elaborazione dei tweet con lo streaming strutturato Spark

Dalla home page del notebook Jupyter selezionare la voce __Spark-Structured-Streaming-From-Kafka.ipynb__. Seguire i passaggi del notebook per caricare i tweet di Kafka tramite lo streaming strutturato Spark.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare lo streaming strutturato Spark, vedere i documenti seguenti per trovare altre informazioni su come lavorare con Spark e Kafka:

* [Come usare lo streaming Spark (DStream) con Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniziare a usare un notebook Jupyter e Spark in HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md)
