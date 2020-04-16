---
title: Monitorare le applicazioni Apache Spark
description: Usare Azure Synapse Studio per monitorare le applicazioni Apache Spark.Use the Azure Synapse Studio to monitor your Apache Spark applications.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: f231693fdcf3519e29eed38e47db52d92acc00fa
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430747"
---
# <a name="use-the-azure-synapse-studio-preview-to-monitor-your-apache-spark-applications"></a>Usare Azure Synapse Studio (anteprima) per monitorare le applicazioni Apache SparkUse the Azure Synapse Studio (preview) to monitor your Apache Spark applications

Con Azure Synapse Analytics è possibile usare Spark per eseguire blocchi appunti, processi e altri tipi di applicazioni nei pool Spark nell'area di lavoro.

Questo articolo spiega come monitorare le applicazioni Spark, consentendo di tenere d'occhio lo stato, i problemi e lo stato più recenti.

## <a name="accessing-the-list-of-spark-applications"></a>Accesso all'elenco delle applicazioni Spark

Per visualizzare l'elenco delle applicazioni Spark nell'area di lavoro, aprire innanzitutto [Azure Synapse Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

  > [!div class="mx-imgBorder"]
  > ![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, seleziona la sezione **Monitor** a sinistra.

  > [!div class="mx-imgBorder"]
  > ![Selezionare Monitora hub](./media/common/left-nav.png)

Selezionare **Applicazioni Spark** per visualizzare l'elenco delle applicazioni Spark.

  > [!div class="mx-imgBorder"]
  > ![Selezionare le applicazioni Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-sparkapplications.png)

## <a name="filtering-your-spark-applications"></a>Filtraggio delle applicazioni Spark

Puoi filtrare l'elenco delle applicazioni Spark in base a quelle che ti interessano. I filtri nella parte superiore dello schermo consentono di specificare un campo in base al quale si desidera filtrare.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo le applicazioni Spark che contengono il nome "vendite":

  > [!div class="mx-imgBorder"]
  > ![Pulsante Filter (Filtro)](./media/common/filter-button.png)

  > [!div class="mx-imgBorder"]
  > ![Filtro campione](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="viewing-details-about-a-specific-spark-application"></a>Visualizzazione dei dettagli di un'applicazione Spark specifica

Per visualizzare i dettagli relativi a una delle applicazioni Spark, selezionare l'applicazione Spark e visualizzare i dettagli. Se l'applicazione Spark è ancora in esecuzione, è possibile monitorare lo stato di avanzamento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni della pipeline, vedere l'articolo Monitorare la [pipeline per l'esecuzione di Azure Synapse Studio.For](how-to-monitor-pipeline-runs.md) more information on monitoring pipeline runs, see the Monitor pipeline runs Azure Synapse Studio article.  
