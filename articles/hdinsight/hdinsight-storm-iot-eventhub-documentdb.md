---
title: Elaborare i dati del sensore veicolo con Apache Storm in HDInsight | Documentazione Microsoft
description: Informazioni su come elaborare i dati del sensore veicolo dagli hub eventi usando Apache Storm in HDInsight Aggiungere i dati del modello da Azure Cosmos DB e archiviarne l&quot;output nella memoria.
services: hdinsight,documentdb,notification-hubs
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 78980635-8bef-4c33-96c3-fae50e932e31
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/03/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8e8ebc724e1c70e8fcd56312adef5da2342373ea
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017


---
# <a name="process-vehicle-sensor-data-from-azure-event-hubs-using-apache-storm-on-hdinsight"></a>Elaborare i dati del sensore veicolo dagli hub eventi di Azure usando Apache Storm in HDInsight

Informazioni su come elaborare i dati del sensore veicolo dagli hub eventi di Azure usando Apache Storm in HDInsight In questo esempio vengono letti i dati del sensore da Hub eventi di Azure e vengono arricchiti tali dati facendo riferimento a quelli archiviati in Azure Cosmos DB. I dati vengono infine archiviati in Archiviazione di Azure usando il file system Hadoop (HDFS).

![HDInsight e il diagramma dell'architettura IoT (Internet of Things)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

## <a name="overview"></a>Panoramica

L'aggiunta di sensori ai veicoli consente di prevedere problemi alle apparecchiature in base alle tendenze dei dati cronologici, nonché di apportare miglioramenti a future versioni in base all'analisi dei modelli di utilizzo. È necessario poter caricare i dati da tutti i veicoli in Hadoop in modo rapido ed efficiente prima di eseguire l'elaborazione MapReduce. Inoltre, può essere utile eseguire analisi in tempo reale per i percorsi di errore critico, quali temperatura del motore, freni e così via.

Hub eventi di Azure è progettato per gestire l'ingente volume di dati generati dai sensori. È possibile usare Apache Storm per caricare ed elaborare i dati prima di archiviarli in HDFS.

## <a name="solution"></a>Soluzione

I dati di telemetria relativi a temperatura del motore, temperatura ambiente e velocità del veicolo vengono registrati dai sensori. Vengono quindi inviati a Hub eventi insieme al numero identificativo del veicolo (numero di telaio) e a un timestamp. A questo punto, una topologia Storm in esecuzione in un cluster Apache Storm in HDInsight legge i dati, li elabora e li archivia in HDFS.

Durante l'elaborazione viene usato il numero identificativo del veicolo per recuperare informazioni sul modello da Cosmos DB. Questi dati vengono aggiunti al flusso di dati prima dell'archiviazione.

I componenti usati nella topologia Storm sono:

* **EventHubSpout** : legge i dati dagli hub eventi di Azure
* **TypeConversionBolt**: converte la stringa JSON da Hub eventi in una tupla contenente i dati del sensore seguenti:
    * engineTemperature
    * Temperatura ambiente
    * speed
    * vin
    * Timestamp
* **DataReferencBolt**: cerca il modello del veicolo in Cosmos DB usando il numero identificativo del veicolo
* **WasbStoreBolt** : archivia i dati in HDFS (Archiviazione di Azure)

Di seguito è riportato un diagramma di questa soluzione:

![topologia storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

## <a name="implementation"></a>Implementazione

Una soluzione completa e automatizzata per questo scenario è disponibile nel repository di [esempi relativi a Storm in HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) su GitHub. Per usare questo esempio, attenersi alla procedura riportata in [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di topologie Storm, vedere [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md).


