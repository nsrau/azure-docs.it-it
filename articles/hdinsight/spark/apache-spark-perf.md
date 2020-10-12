---
title: Ottimizzare i processi Spark per ottenere prestazioni ottimali - Azure HDInsight
description: Illustra alcune strategie comuni per ottenere prestazioni ottimali dai cluster Apache Spark in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/21/2020
ms.custom: contperfq1
ms.openlocfilehash: fbd0da43e79ee2c27f654f9bd07614e08c12fd30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88756929"
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
