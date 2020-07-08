---
title: Come monitorare le applicazioni Apache Spark
description: Usare Azure sinapsi Studio per monitorare le applicazioni Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: e646f1bc1a25f58dd54437cbd77750479ac9ae99
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970901"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Usare Azure sinapsi Studio (anteprima) per monitorare le applicazioni di Apache Spark

Con l'analisi delle sinapsi di Azure, è possibile usare Spark per eseguire notebook, processi e altri tipi di applicazioni nei pool Spark nell'area di lavoro.

Questo articolo illustra come monitorare le applicazioni Apache Spark, consentendo di tenere sotto controllo lo stato, i problemi e lo stato di avanzamento più recenti.

## <a name="accessing-the-list-of-apache-spark-applications"></a>Accesso all'elenco di applicazioni di Apache Spark

Per visualizzare l'elenco delle applicazioni Apache Spark nell'area di lavoro, aprire prima di tutto [Azure sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **Apache Spark applicazioni** per visualizzare l'elenco di applicazioni Apache Spark.

 ![Selezionare le applicazioni Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-apache-spark-applications"></a>Filtraggio delle applicazioni di Apache Spark

È possibile filtrare l'elenco di applicazioni di Apache Spark a quelle a cui si è interessati. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo le applicazioni Apache Spark che contengono il nome "Sales":

![Pulsante Filter (Filtro)](./media/common/filter-button.png)

![Filtro di esempio](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-apache-spark-application"></a>Visualizzazione di dettagli su un'applicazione Apache Spark specifica

Per visualizzare i dettagli relativi a una delle applicazioni Apache Spark, selezionare l'applicazione Apache Spark e visualizzare i dettagli. Se il Apache Spark applicazione è ancora in esecuzione, è possibile monitorare lo stato di avanzamento. [Altre informazioni](apache-spark-applications.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare la pipeline di esecuzione di Azure sinapsi Studio](how-to-monitor-pipeline-runs.md) . 

Per altre informazioni sul debug di Apache Spark applicazione, vedere l'articolo [monitorare le applicazioni di Apache Spark in Azure sinapsi Studio](apache-spark-applications.md) .