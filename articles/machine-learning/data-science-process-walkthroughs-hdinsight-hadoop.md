---
title: Procedure dettagliate di data science per HDInsight Hadoop con Hive in Azure | Microsoft Docs
description: Esempi del processo di data science per i team che descrive l'uso di Hive in Azure HDInsight Hadoop per eseguire analisi predittive.
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: fb06c3c1b1ae30d970a2e4d45a49e22e9d78b876
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>Procedure dettagliate di data science per HDInsight Hadoop con Hive in Azure 

Queste procedure dettagliate usano Hive con un cluster HDInsight Hadoop per eseguire analisi predittive. Seguono i passaggi descritti nel processo di data science per i team. Per una panoramica del processo di data science per i team, vedere [Processo di data science](data-science-process-overview.md). Per un'introduzione ad Azure HDInsight, vedere [Introduzione ad Azure HDInsight, allo stack di tecnologie Hadoop e ai cluster Hadoop](../hdinsight/hdinsight-hadoop-introduction.md).

Le procedure dettagliate di data science aggiuntive che eseguono il processo di data science per i team sono raggruppate in base alla **piattaforma** che usano: 

[!INCLUDE [tdsp-walkthroughs-by-platform](../../includes/tdsp-walkthroughs-by-platform.md)]


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Stimare le mance dei taxi usando Hive con HDInsight Hadoop

La procedura dettagliata [Usare cluster HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) utilizza i dati relativi ai taxi di New York per prevedere: 

- Se viene lasciata una mancia 
- La distribuzione degli importi delle mance

Lo scenario viene implementato usando Hive con un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/). Viene illustrato come archiviare ed esplorare i dati e progettarne le funzionalità da un set di dati relativo a corse e tariffe dei taxi di NYC disponibile pubblicamente. Si usa inoltre Azure Machine Learning per compilare e distribuire i modelli.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Stimare i clic sugli annunci usando Hive con HDInsight Hadoop

La procedura dettagliata [Usare cluster Hadoop di Azure HDInsight in un set di dati da 1 TB](machine-learning-data-science-process-hive-criteo-walkthrough.md) usa un set di dati relativo ai clic su [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) disponibile pubblicamente per stimare se viene lasciata una mancia e l'intervallo di importi previsto. Lo scenario viene implementato usando Hive con un [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) per archiviare ed esplorare i dati di esempio nonché progettarne le funzionalità e sottocampionarli. Usa Azure Machine Learning per compilare, eseguire il training e assegnare un punteggio a un modello di classificazione binario che prevede se un utente fa clic su un annuncio. La procedura dettagliata si conclude illustrando come pubblicare uno di questi modelli come servizio Web.


## <a name="next-steps"></a>Passaggi successivi

Per una descrizione dei componenti principali che costituiscono il processo di data science per i team, vedere [Panoramica del processo di data science per i team](data-science-process-overview.md).

Per una descrizione del ciclo di vita del processo di data science per i team che è possibile usare per definire la struttura dei progetti di data science, vedere [Ciclo di vita del processo di data science per i team](data-science-process-lifecycle.md). Il ciclo di vita descrive tutti i passaggi generalmente seguiti dai progetti in fase di esecuzione. 


