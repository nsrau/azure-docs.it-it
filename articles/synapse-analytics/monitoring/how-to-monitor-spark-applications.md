---
title: Monitorare applicazioni Apache Spark
description: Usare Azure sinapsi Studio per monitorare le applicazioni Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430747"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Usare Azure sinapsi Studio (anteprima) per monitorare le applicazioni di Apache Spark

Con l'analisi delle sinapsi di Azure, è possibile usare Spark per eseguire notebook, processi e altri tipi di applicazioni nei pool Spark nell'area di lavoro.

Questo articolo illustra come monitorare le applicazioni Spark, consentendo di tenere sotto controllo lo stato, i problemi e lo stato di avanzamento più recenti.

## <a name="accessing-the-list-of-spark-applications"></a>Accesso all'elenco di applicazioni Spark

Per visualizzare l'elenco delle applicazioni Spark nell'area di lavoro, aprire prima di tutto [Azure sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

  > [!div class="mx-imgBorder"]
  > ![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

  > [!div class="mx-imgBorder"]
  > ![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **applicazioni Spark** per visualizzare l'elenco di applicazioni Spark.

  > [!div class="mx-imgBorder"]
  > ![Selezionare le applicazioni Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtrare le applicazioni Spark

È possibile filtrare l'elenco di applicazioni Spark a quelle a cui si è interessati. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo le applicazioni Spark che contengono il nome "Sales":

  > [!div class="mx-imgBorder"]
  > ![Pulsante Filter (Filtro)](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtro di esempio](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visualizzazione di dettagli su un'applicazione Spark specifica

Per visualizzare i dettagli di una delle applicazioni Spark, selezionare l'applicazione Spark e visualizzare i dettagli. Se l'applicazione Spark è ancora in esecuzione, è possibile monitorare lo stato di avanzamento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare la pipeline di esecuzione di Azure sinapsi Studio](how-to-monitor-pipeline-runs.md) .  
