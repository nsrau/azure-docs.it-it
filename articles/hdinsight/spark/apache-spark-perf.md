---
title: Ottimizzare i processi Spark per ottenere prestazioni ottimali - Azure HDInsight
description: Illustra alcune strategie comuni per ottenere prestazioni ottimali dai cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780099"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Ottimizzare i processi Apache Spark in HDInsight

Questo articolo fornisce una panoramica delle strategie per ottimizzare i processi Apache Spark in Azure HDInsight.

## <a name="overview"></a>Panoramica

Le prestazioni dei processi di Apache Spark sono influenzate da più fattori. Tali fattori includono: la modalità di archiviazione dei dati, la configurazione del cluster e le operazioni usate durante l'elaborazione dei dati.

I problemi riscontrati più comunemente includono limitazioni della memoria dovute a configurazioni non corrette, operazioni con esecuzione prolungata e attività che generano operazioni cartesiane.

Esistono inoltre diverse strategie che consentono di superare tali problemi, ad esempio la memorizzazione nella cache e la possibilità di asimmetria dei dati.

Negli articoli seguenti è possibile trovare problemi comuni e soluzioni per aspetti diversi dell'ottimizzazione di Spark.

* [Ottimizzare l'archiviazione dei dati](optimize-data-storage.md)
* [Ottimizzare l'elaborazione dati](optimize-data-processing.md)
* [Ottimizzare l'utilizzo della memoria](optimize-memory-usage.md)
* [Ottimizzare la configurazione del cluster](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight](apache-spark-job-debugging.md)
* [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
* [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)
