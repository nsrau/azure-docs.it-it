---
title: La pipeline di monitoraggio esegue Azure Synapse Studio (anteprima)Monitor pipeline runs Azure Synapse Studio (preview)
description: Usare Azure Synapse Studio per monitorare le esecuzioni della pipeline dell'area di lavoro.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430786"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Usare Azure Synapse Studio per monitorare le esecuzioni della pipeline dell'area di lavoroUse Azure Synapse Studio to monitor your workspace pipeline runs

Con Azure Synapse Analytics è possibile creare pipeline complesse in grado di automatizzare e orchestrare lo spostamento dei dati, la trasformazione dei dati e le attività di calcolo all'interno della soluzione. È possibile creare e monitorare queste pipeline usando Azure Synapse Studio (anteprima).

In questo articolo viene illustrato come monitorare le esecuzioni della pipeline, che consente di tenere d'occhio lo stato, i problemi e lo stato più recenti delle pipeline.

## <a name="access-the-list-of-pipeline-runs"></a>Accedere all'elenco delle esecuzioni della pipeline

Per visualizzare l'elenco delle esecuzioni della pipeline nell'area di lavoro, aprire innanzitutto [Azure Synapse Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, seleziona la sezione **Monitor** a sinistra.

![Selezionare Monitora hub](./media/common/left-nav.png)

Selezionare **Pipeline viene eseguita** per visualizzare l'elenco delle esecuzioni della pipeline.

![Selezionare le esecuzioni della pipeline](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtraggio delle esecuzioni della pipeline

È possibile filtrare l'elenco delle esecuzioni della pipeline in base a quelle a cui si è interessati. I filtri nella parte superiore dello schermo consentono di specificare un campo in base al quale si desidera filtrare.

Ad esempio, è possibile filtrare la vista per visualizzare solo le esecuzioni della pipeline per la pipeline denominata "vacanza":

![Pulsante Filter (Filtro)](./media/common/filter-button.png)

![Filtro campione](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Visualizzazione dei dettagli relativi a un'esecuzione della pipeline specifica

Per visualizzare i dettagli relativi all'esecuzione della pipeline, selezionare l'esecuzione della pipeline. Visualizzare quindi le esecuzioni di attività associate all'esecuzione della pipeline. Se la pipeline è ancora in esecuzione, è possibile monitorare lo stato di avanzamento. 
  
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle applicazioni, vedere l'articolo Monitorare le applicazioni Apache Spark.To learn more about monitoring applications, see the [Monitor Apache Spark applications article.](how-to-monitor-spark-applications.md) 
