<properties
 pageTitle="Elaborare i dati del sensore veicolo con Apache Storm in HDInsight | Microsoft Azure"
 description="Informazioni su come elaborare i dati del sensore veicolo dagli hub eventi usando Apache Storm in HDInsight Aggiungere i dati del modello del database DocumentDB e archiviarne l'output nella risorsa di archiviazione."
 services="hdinsight,documentdb,notification-hubs"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="09/23/2015"
ms.author="larryfr"/>

#Elaborare i dati del sensore veicolo dagli hub eventi di Azure usando Apache Storm in HDInsight

Informazioni su come elaborare i dati del sensore veicolo dagli hub eventi di Azure usando Apache Storm in HDInsight Questo esempio legge i dati del sensore dagli hub eventi di Azure, arricchisce i dati facendo riferimento ai dati archiviati in Azure DocumentDB e infine archivia i dati in Archiviazione di Azure usando il file system Hadoop (HDFS).

![HDInsight e il diagramma dell'architettura IoT (Internet of Things)](./media/hdinsight-storm-iot-eventhub-documentdb/iot.png)

##Panoramica

L'aggiunta di sensori ai veicoli consente di prevedere problemi alle apparecchiature in base alle tendenze dei dati cronologici, nonché di apportare miglioramenti a future versioni basate sull'analisi dei modelli di utilizzo. Sebbene sia possibile usare l'elaborazione batch MapReduce tradizionale per questa analisi, è necessario poter caricare i dati da tutti i veicoli in Hadoop in modo rapido ed efficiente prima di eseguire l'elaborazione MapReduce. Inoltre, può essere utile eseguire analisi in tempo reale per i percorsi di errore critico, quali temperatura del motore, freni e così via.

Gli hub eventi di Azure sono progettati per gestire l'ingente volume di dati generati dai sensori e Apache Storm in HDInsight può essere usato per caricare ed elaborare i dati prima dell'archiviazione in HDFS (con il supporto di Archiviazione di Azure) per ulteriori operazioni di elaborazione di MapReduce.

##Soluzione

I dati di telemetria relativi a temperatura del motore, temperatura ambiente e velocità del veicolo vengono registrati dai sensori, quindi inviati agli hub eventi insieme al numero identificativo del veicolo (numero di telaio) e a un timestamp. A questo punto, una topologia Storm in esecuzione in un cluster Apache Storm in HDInsight legge i dati, li elabora e li archivia in HDFS.

Durante l'elaborazione viene usato il numero identificativo del veicolo per recuperare informazioni sul modello da Azure DocumentDB. Questo viene aggiunto al flusso di dati prima dell'archiviazione.

I componenti usati nella topologia Storm sono:

* **EventHubSpout**: legge i dati dagli hub eventi di Azure

* **TypeConversionBolt**: converte la stringa JSON dagli hub eventi in una tupla contenente i valori dei singoli dati relativi a temperatura del motore, temperatura ambiente, velocità, numero identificativo del veicolo e timestamp

* **DataReferencBolt**: cerca il modello del veicolo in DocumentDB usando il numero identificativo del veicolo

* **WasbStoreBolt**: archivia i dati in HDFS (Archiviazione di Azure)

Di seguito è riportato un diagramma di questa soluzione:

![topologia storm](./media/hdinsight-storm-iot-eventhub-documentdb/iottopology.png)

> [AZURE.NOTE]Si tratta di un diagramma semplificato e ogni componente della soluzione può avere più istanze. Ad esempio, le varie istanze di ogni componente nella topologia vengono distribuite tra i nodi del cluster Storm in HDInsight.

##Implementazione

Una soluzione completa e automatizzata per questo scenario è disponibile nel repository di <a href="https://github.com/hdinsight/hdinsight-storm-examples" target="_blank">esempi relativi a Storm in HDInsight</a> su GitHub. Per usare questo esempio, attenersi alla procedura riportata in [IoTExample README. MD](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md).

## Passaggi successivi

Per altri esempi di topologie Storm, vedere [Topologie di esempio per Storm in HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=Sept15_HO4-->