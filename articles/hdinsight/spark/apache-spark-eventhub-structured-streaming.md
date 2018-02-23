---
title: Usare Apache Spark Structured Streaming con Hub eventi in Azure HDInsight | Microsoft Docs
description: Creare un esempio di streaming di Apache Spark su come inviare un flusso di dati a Hub eventi di Azure e quindi ricevere tali eventi nel cluster HDInsight Spark usando un'applicazione Scala.
keywords: streaming apache spark, streaming spark, esempio spark, esempio streaming spark apache, esempio azure hub eventi, esempio spark
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jgao
ms.openlocfilehash: 14cc32c22653d5d8bd3dd5a1a41d2f64cfd8c73c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Apache Spark Structured Streaming in HDInsight per elaborare gli eventi da Hub eventi

In questo articolo viene illustrato come elaborare i dati di telemetria in tempo reale usando Spark Structured Streaming. A tale scopo, è necessario seguire questa procedura generale:

1. Compilare ed eseguire nella workstation locale un'applicazione producer di eventi di esempio che genera eventi da inviare a Hub eventi.
2. Usare la [shell di Spark](apache-spark-shell.md) per definire ed eseguire una semplice applicazione Spark Structured Streaming.

## <a name="prerequisites"></a>prerequisiti

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un cluster Apache Spark in HDInsight. Per istruzioni, vedere l'articolo dedicato alla [creazione di cluster Apache Spark in Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Oracle Java Development Kit. Per installarlo, fare clic [qui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. È possibile scaricarlo [qui](https://maven.apache.org/download.cgi). Le istruzioni per scaricare Maven sono disponibili [qui](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Compilare, configurare ed eseguire il producer di eventi
In questa attività si clona un'applicazione di esempio che crea eventi casuali e li invia a un hub eventi configurato. Questa applicazione di esempio è disponibile in GitHub all'indirizzo [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Verificare che gli strumenti Git siano installati. È possibile scaricarli da [GIT Downloads](http://www.git-scm.com/downloads) (Download GIT). 
2. Aprire un prompt dei comandi o una shell del terminale ed eseguire il comando seguente dalla directory scelta per clonare il progetto.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. Viene creata una nuova cartella denominata eventhubs-client-sample. Nella shell o nel prompt dei comandi passare a questa cartella.
4. Eseguire Maven per compilare l'applicazione usando il comando seguente:

          mvn package

5. Nella shell o nel prompt dei comandi passare alla directory di destinazione creata contenente il file ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. È quindi necessario creare la riga di comando per eseguire il producer di eventi nell'hub eventi. A questo scopo, sostituire i valori nel comando come segue:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. Un comando completo, ad esempio, sarà simile al seguente:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. Il comando viene avviato e, se la configurazione è corretta, in pochi istanti viene visualizzato l'output relativo agli eventi inviati all'hub eventi, simile al seguente:

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Lasciare in esecuzione il producer di eventi mentre si continua con la procedura.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Eseguire la shell di Spark nel cluster HDInsight
In questa attività si stabilirà una connessione SSH al nodo head del cluster HDInsight, si avvierà la shell di Spark e si eseguirà un'applicazione Spark Streaming che recupera ed elabora gli eventi da Hub eventi. 

A questo punto il cluster HDInsight dovrebbe essere pronto. In caso contrario, è necessario attendere il termine del provisioning. Quando è pronto, iniziare la procedura seguente:

1. Connettersi al cluster HDInsight tramite SSH. Per istruzioni, vedere [Connettersi a HDInsight con SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. L'applicazione compilata richiede il pacchetto Hub eventi per Spark Streaming. Per eseguire la shell di Spark in modo che recuperi automaticamente questa dipendenza da [Maven Central](https://search.maven.org), assicurarsi di specificare l'opzione packages con le coordinate di Maven come segue:

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.5"

6. Al termine del caricamento della shell di Spark, verrà visualizzato:

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.1.2.6.2.3-1
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_151)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. Copiare il frammento di codice seguente in un editor di testo e modificarlo in modo che la chiave dei criteri e lo spazio dei nomi corrispondano a quelli del proprio hub eventi.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )
            
8. Se si esamina l'endpoint compatibile con l'hub eventi nel formato seguente, la parte `iothub-xxxxxxxxxx` è il nome dello spazio dei nomi compatibile con l'hub eventi e può essere usata per `eventhubs.namespace`. Il campo `SharedAccessKeyName` può essere usato per `eventhubs.policyname` e `SharedAccessKey` per `eventhubs.policykey`: 

        Endpoint=sb://iothub-xxxxxxxxxx.servicebus.windows.net/;SharedAccessKeyName=xxxxx;SharedAccessKey=xxxxxxxxxx 

9. Incollare il frammento modificato nel prompt scala> in attesa e premere INVIO. Verrà visualizzato un output simile al seguente:

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

10. Si inizia quindi a creare una query di Spark Structured Streaming specificando l'origine. Incollare il codice seguente nella shell di Spark e premere INVIO.

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

11. Verrà visualizzato un output simile al seguente:

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

12. Modificare quindi la query in modo che scriva l'output nella console. A tale scopo, incollare il codice seguente nella shell di Spark e premere INVIO.

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

13. Alcuni batch verranno avviati con un output simile al seguente

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

14. Seguono i risultati di output dell'elaborazione di ogni microbatch di eventi. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

15. Quando arrivano nuovi eventi dal producer di eventi, vengono elaborati da questa query di Structured Streaming.
16. Assicurarsi di eliminare il cluster HDInsight dopo aver completato l'esecuzione dell'esempio.



## <a name="see-also"></a>Vedere anche 

* [Panoramica di Spark Streaming](apache-spark-streaming-overview.md)













