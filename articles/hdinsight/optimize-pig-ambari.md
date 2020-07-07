---
title: Ottimizzare Apache Pig con Apache Ambari in Azure HDInsight
description: Usare l'interfaccia utente Web di Apache Ambari per configurare e ottimizzare Apache Pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 469019eb1e90654d1953156337593d5de99b46c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82796681"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Ottimizzare Apache Pig con Apache Ambari in Azure HDInsight

Apache Ambari è un'interfaccia Web per la gestione e il monitoraggio di cluster HDInsight. Per un'introduzione all'interfaccia utente Web di Ambariri, vedere [gestire i cluster HDInsight tramite l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Le proprietà di Apache Pig possono essere modificate dall'interfaccia utente Web Ambari per ottimizzare le query di Pig. Se le proprietà di Pig vengono modificate da Ambari, vengono direttamente modificate anche nel file `/etc/pig/2.4.2.0-258.0/pig.properties`.

1. Per modificare le proprietà di Pig, passare alla scheda **Configs** (Configurazioni) di Pig e quindi espandere il riquadro **Advanced pig-properties** (Proprietà Pig avanzate).

1. Trovare, rimuovere il commento e cambiare il valore della proprietà che si vuole modificare.

1. Selezionare **Salva** nel lato superiore destro della finestra per salvare il nuovo valore. Alcune proprietà possono richiedere un riavvio del servizio.

    ![Proprietà avanzate di Apache Pig](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Le impostazioni a livello di sessione eseguono l'override dei valori delle proprietà nel file `pig.properties`.

## <a name="tune-execution-engine"></a>Ottimizzare il motore di esecuzione

Per eseguire gli script di Pig, sono disponibili due motori di esecuzione: MapReduce e Tez. Tez è un motore ottimizzato ed è molto più veloce di MapReduce.

1. Per modificare il motore di esecuzione, nel riquadro **Advanced pig-properties** (Proprietà Pig avanzate) trovare la proprietà `exectype`.

1. Il valore predefinito è **MapReduce**. Impostarlo su **Tez**.

## <a name="enable-local-mode"></a>Abilitare la modalità locale

Come in Hive, la modalità locale viene usata per velocizzare i processi con quantità relativamente piccole di dati.

1. Per abilitare la modalità locale, impostare `pig.auto.local.enabled` su **true**. Il valore predefinito è false.

1. I processi con dati di input di dimensioni inferiori al valore della proprietà `pig.auto.local.input.maxbytes` sono considerati processi di piccole dimensioni. Il valore predefinito è 1 GB.

## <a name="copy-user-jar-cache"></a>Copiare la cache JAR dell'utente

Pig copia i file JAR necessari per le funzioni definite dall'utente in una cache distribuita per renderli disponibili per i nodi attività. Questi jar non cambiano di frequente. Se abilitata, l'impostazione `pig.user.cache.enabled` consente di inserire i file JAR in una cache per riutilizzarli per i processi eseguiti dallo stesso utente. Questa impostazione comporta un lieve aumento delle prestazioni del processo.

1. Per abilitarla, impostare `pig.user.cache.enabled` su true. Il valore predefinito è false.

1. Per impostare il percorso di base dei file JAR memorizzati nella cache, impostare `pig.user.cache.location` sul percorso di base. Il valore predefinito è `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Ottimizzare le prestazioni con le impostazioni della memoria

Le impostazioni della memoria seguenti consentono di ottimizzare le prestazioni di uno script di Pig.

* `pig.cachedbag.memusage`: Quantità di memoria assegnata a un contenitore. Un contenitore è una raccolta di tuple. Una tupla è un set ordinato di campi e un campo è costituito da dati. Se i dati in un contenitore superano la memoria specificata, vengono distribuiti su disco. Il valore predefinito è 0,2, che rappresenta il 20% della memoria disponibile. Questa memoria viene condivisa tra tutti i contenitori di un'applicazione.

* `pig.spill.size.threshold`: i contenitori con dimensioni superiori a questa soglia di distribuzione (in byte) vengono distribuiti su disco. Il valore predefinito è 5 MB.

## <a name="compress-temporary-files"></a>Comprimere i file temporanei

Pig genera file temporanei durante l'esecuzione dei processi. La compressione dei file temporanei produce un aumento delle prestazioni durante la lettura o la scrittura di file su disco. Per comprimere i file temporanei, si possono usare le impostazioni seguenti.

* `pig.tmpfilecompression`: se true, abilita la compressione dei file temporanei. Il valore predefinito è false.

* `pig.tmpfilecompression.codec`: codec di compressione da usare per comprimere i file temporanei. I codec di compressione consigliati sono LZO e snapy per un uso più basso della CPU.

## <a name="enable-split-combining"></a>Abilitare la combinazione per la suddivisione

Se abilitata, i file di piccole dimensioni vengono combinati per ottenere un numero inferiore di attività mappe, Questa impostazione migliora l'efficienza dei processi con molti file di piccole dimensioni. Per abilitarla, impostare `pig.noSplitCombination` su true. Il valore predefinito è false.

## <a name="tune-mappers"></a>Ottimizzare i mapper

Il numero di mapper viene controllato modificando la proprietà `pig.maxCombinedSplitSize`, Questa proprietà specifica le dimensioni dei dati che devono essere elaborati da una singola attività mappa. Il valore predefinito corrisponde alle dimensioni di blocco predefinite del file system. L'aumento di questo valore comporta un numero inferiore di attività del mapper.

## <a name="tune-reducers"></a>Ottimizzare i riduttori

Il numero di riduttori viene calcolato in base al parametro `pig.exec.reducers.bytes.per.reducer`. Il parametro specifica il numero di byte elaborati per riduttore. L'impostazione predefinita è 1 GB. Per limitare il numero massimo di riduttori, impostare la `pig.exec.reducers.max` proprietà per impostazione predefinita 999.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con l'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Ottimizzare i cluster](./hdinsight-changing-configs-via-ambari.md)
* [Ottimizzare Apache HBase](./optimize-hbase-ambari.md)
* [Ottimizzare Apache Hive](./optimize-hive-ambari.md)
