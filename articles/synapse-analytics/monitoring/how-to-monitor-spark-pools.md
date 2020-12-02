---
title: Come monitorare i pool di Apache Spark in sinapsi Studio
description: Informazioni su come monitorare i pool di Apache Spark usando sinapsi Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 8d95897e0c2d58b2a3955918be945800eed9ba56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467245"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-pools"></a>Usare sinapsi Studio per monitorare i pool di Apache Spark

Con l'analisi delle sinapsi di Azure, è possibile usare Spark per eseguire notebook, processi e altri tipi di applicazioni nei pool Spark nell'area di lavoro.

In questo articolo viene illustrato come monitorare i pool di Apache Spark, consentendo di tenere sotto controllo lo stato dei pool, incluso il numero di Vcore utilizzati da diversi utenti dell'area di lavoro.

## <a name="access-spark-pools-list"></a>Accedi all'elenco di pool Spark

Per visualizzare l'elenco dei pool di Apache Spark nell'area di lavoro, aprire prima di tutto [sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **Apache Spark pool** per visualizzare l'elenco dei pool di Apache Spark.

 ![Seleziona pool di Apache Spark](./media/how-to-monitor-spark-pools/monitor-hub-nav-spark-pools.png)

## <a name="filter-your-spark-pools"></a>Filtrare i pool Spark

È possibile filtrare l'elenco dei pool Spark con quelli che interessano. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo i pool Spark che contengono il nome "dataprep":

![Filtro di esempio](./media/how-to-monitor-spark-pools/filter-example.png)

## <a name="view-details-about-a-specific-spark-pool"></a>Visualizza i dettagli relativi a un pool Spark specifico

Per visualizzare i dettagli relativi a uno dei pool Spark, selezionare il pool Spark per visualizzare i dettagli.

![Dettagli del pool di Apache Spark](./media/how-to-monitor-spark-pools/spark-pool-details.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare le esecuzioni della pipeline in sinapsi Studio](how-to-monitor-pipeline-runs.md) . 

Per altre informazioni sul monitoraggio Apache Spark applicazioni, vedere l'articolo [monitorare le applicazioni Apache Spark in sinapsi Studio](how-to-monitor-spark-applications.md) .
