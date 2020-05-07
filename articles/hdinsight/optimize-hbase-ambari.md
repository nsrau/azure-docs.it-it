---
title: Ottimizzare Apache HBase con Apache Ambari in Azure HDInsight
description: Usare l'interfaccia utente Web di Apache Ambari per configurare e ottimizzare Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: a7da6bc23d797e0e89b2338f446fc850b0fd0577
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82797162"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Ottimizzare Apache HBase con Apache Ambari in Azure HDInsight

Apache Ambari è un'interfaccia Web per la gestione e il monitoraggio di cluster HDInsight. Per un'introduzione all'interfaccia utente Web di Ambariri, vedere [gestire i cluster HDInsight tramite l'interfaccia utente Web di Apache Ambari](hdinsight-hadoop-manage-ambari.md).

La configurazione di Apache HBase viene modificata dalla scheda **configs di HBase** . Le sezioni seguenti descrivono alcune delle importanti impostazioni di configurazione che influiscono sulle prestazioni di HBase.

## <a name="set-hbase_heapsize"></a>Impostare HBASE_HEAPSIZE

Le dimensioni dell'heap HBase specificano, in megabyte, la quantità massima di heap che devono essere usati dai server di *area* e *master*. Il valore predefinito è 1.000 MB. Questo valore deve essere ottimizzato per il carico di lavoro del cluster.

1. Per modificarlo, passare al riquadro **Advanced HBase-env** (Ambiente HBase avanzato) nella scheda **Configs** (Configurazioni) e quindi trovare l'impostazione `HBASE_HEAPSIZE`.

1. Impostare il valore predefinito su 5.000 MB.

    ![' Apache Ambari HBase Memory heapsize '](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Ottimizzare i carichi di lavoro con numero elevato di letture

Le configurazioni seguenti sono importanti per migliorare le prestazioni dei carichi di lavoro con numero elevato di letture.

### <a name="block-cache-size"></a>Dimensioni della cache dei blocchi

La cache dei blocchi è la cache di lettura, le cui dimensioni sono controllate dal parametro `hfile.block.cache.size`. Il valore predefinito è 0,4, che corrisponde al 40% della memoria totale del server di area. La velocità delle letture casuali sarà direttamente proporzionale alle dimensioni della cache di blocchi.

1. Per modificare questo parametro, passare alla scheda **Settings** (Impostazioni) nella scheda **Configs** (Configurazioni) di HBase e quindi individuare **% of RegionServer Allocated to Read Buffers** (% di server di area allocata ai buffer di lettura).

    ![Dimensioni della cache del blocco di memoria Apache HBase](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Per modificare il valore, selezionare l'icona **Edit** (Modifica).

### <a name="memstore-size"></a>Dimensioni del memstore

Tutte le modifiche vengono archiviate nel buffer di memoria, denominato *memstore*. Questo buffer aumenta la quantità totale di dati che possono essere scritti su disco in un'unica operazione. Consente inoltre di velocizzare l'accesso alle modifiche recenti. Le dimensioni del memstore sono definite dai due parametri seguenti:

* `hbase.regionserver.global.memstore.UpperLimit`: definisce la percentuale massima del server di area che può essere usata dal memstore combinato.

* `hbase.regionserver.global.memstore.LowerLimit`: definisce la percentuale minima del server di area che può essere usata dal memstore combinato.

Per ottimizzare le letture casuali, è possibile ridurre i limiti massimo e minimo del memstore.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Numero di righe recuperate quando si esegue l'analisi dal disco

L'impostazione `hbase.client.scanner.caching` definisce il numero di righe lette dal disco quando il metodo `next` viene chiamato su uno scanner.  Il valore predefinito è 100. Maggiore è il numero, meno saranno le chiamate remote eseguite dal client al server di area e le analisi saranno quindi più rapide. Questa impostazione, tuttavia, aumenterà anche il numero di richieste di memoria nel client.

![Numero di righe Apache HBase recuperate](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Non impostare il valore in modo tale che l'intervallo di tempo tra le chiamate al metodo next su uno scanner sia superiore al timeout dello scanner. La durata del timeout dello scanner è definita dalla proprietà `hbase.regionserver.lease.period`.

## <a name="optimize-write-heavy-workloads"></a>Ottimizzare i carichi di lavoro con numero elevato di scritture

Le configurazioni seguenti sono importanti per migliorare le prestazioni dei carichi di lavoro con numero elevato di scritture.

### <a name="maximum-region-file-size"></a>Dimensioni massime del file di area

HBase archivia i dati in un formato di file interno, denominato *HFile*. La proprietà `hbase.hregion.max.filesize` definisce le dimensioni di un singolo HFile per un'area.  Un'area viene divisa in due aree se la somma di tutti gli HFile di un'area è superiore al valore di questa impostazione.

![' Apache HBase HRegion max filesize '](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Maggiori sono le dimensioni del file di area, minore è il numero di suddivisioni. È possibile aumentare le dimensioni del file per determinare un valore che garantisca prestazioni in scrittura ottimali.

### <a name="avoid-update-blocking"></a>Evitare il blocco degli aggiornamenti

* La proprietà `hbase.hregion.memstore.flush.size` definisce a quali dimensioni il memstore viene scaricato su disco. Le dimensioni predefinite sono pari a 128 MB.

* Il moltiplicatore di blocco Region HBase è `hbase.hregion.memstore.block.multiplier`definito da. Il valore predefinito è 4. Il valore massimo consentito è 8.

* HBase blocca gli aggiornamenti se il memstore è pari a (`hbase.hregion.memstore.flush.size` * `hbase.hregion.memstore.block.multiplier`) byte.

    Con i valori predefiniti delle dimensioni di flush e del moltiplicatore di blocco, gli aggiornamenti vengono bloccati quando le dimensioni del memstore sono pari a 128 * 4 = 512 MB. Per ridurre il conteggio dei blocchi degli aggiornamenti, aumentare il valore di `hbase.hregion.memstore.block.multiplier`.

![Moltiplicatore di blocco dell'area Apache HBase](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definire le dimensioni del memstore

Le dimensioni del memstore sono definite dai parametri `hbase.regionserver.global.memstore.UpperLimit` e `hbase.regionserver.global.memstore.LowerLimit`. Se si impostano questi parametri sullo stesso valore, si riducono le pause tra un'operazione di scrittura e l'altra (aumentando anche la frequenza di flush) e migliorano le prestazioni in scrittura.

## <a name="set-memstore-local-allocation-buffer"></a>Impostare il buffer di allocazione locale del memstore

L'utilizzo del buffer di allocazione locale del memstore è determinato dalla proprietà `hbase.hregion.memstore.mslab.enabled`. Se abilitata (true), questa impostazione impedisce la frammentazione dell'heap durante un'operazione di scrittura intensiva. Il valore predefinito è true.

![hbase.hregion.memstore.mslab.enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i cluster HDInsight con l'interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Ottimizzare i cluster](./hdinsight-changing-configs-via-ambari.md)
* [Ottimizza Apache Hive](./optimize-hive-ambari.md)
* [Ottimizzare Apache Pig](./optimize-pig-ambari.md)
