---
title: Ottimizzare i processi Spark per ottenere prestazioni ottimali - Azure HDInsight
description: Illustra alcune strategie comuni per ottenere prestazioni ottimali dai cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117952"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Ottimizzare i processi Apache Spark in HDInsight

Questo articolo fornisce una panoramica delle strategie per ottimizzare i processi Apache Spark in Azure HDInsight.

## <a name="overview"></a>Panoramica

Le prestazioni dei processi di Apache Spark sono influenzate da più fattori. Tali fattori includono: la modalità di archiviazione dei dati, la configurazione del cluster e le operazioni usate durante l'elaborazione dei dati.

Le difficoltà comuni che è possibile affrontare includono i vincoli di memoria dovuti a esecutori di dimensioni non corrette, operazioni con esecuzione prolungata e attività che generano operazioni cartesiane.

Sono inoltre disponibili molte ottimizzazioni che consentono di superare tali problemi, ad esempio la memorizzazione nella cache, e la possibilità di asimmetria dei dati.

In ognuno degli articoli seguenti è possibile trovare informazioni su diversi aspetti dell'ottimizzazione di Spark.

* [Ottimizzare l'archiviazione dei dati per Apache Spark](optimize-data-storage.md)
* [Ottimizzare l'elaborazione dei dati per Apache Spark](optimize-data-processing.md)
* [Ottimizzare l'utilizzo della memoria per Apache Spark](optimize-memory-usage.md)
* [Ottimizzare la configurazione del cluster HDInsight per Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il debug di processi Apache Spark in esecuzione in Azure HDInsight](apache-spark-job-debugging.md)
* [Gestire le risorse di un cluster Apache Spark in HDInsight](apache-spark-resource-manager.md)
* [Configurare le impostazioni di Apache Spark](apache-spark-settings.md)
