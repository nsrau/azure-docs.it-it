---
title: La pipeline di monitoraggio esegue Azure sinapsi Studio (anteprima)
description: Usare Azure sinapsi Studio per monitorare le esecuzioni della pipeline dell'area di lavoro.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 712dc62e29229f03dec12afdf18edbf55667dbdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430786"
---
# <a name="use-azure-synapse-studio-to-monitor-your-workspace-pipeline-runs"></a>Usare Azure sinapsi Studio per monitorare le esecuzioni della pipeline dell'area di lavoro

Con l'analisi delle sinapsi di Azure è possibile creare pipeline complesse che consentono di automatizzare e orchestrare le attività di spostamento dei dati, trasformazione dei dati e calcolo all'interno della soluzione. È possibile creare e monitorare queste pipeline usando Azure sinapsi Studio (anteprima).

Questo articolo illustra come monitorare le esecuzioni della pipeline, che consente di tenere sotto controllo lo stato, i problemi e lo stato di avanzamento delle pipeline più recenti.

## <a name="access-the-list-of-pipeline-runs"></a>Accedere all'elenco di esecuzioni di pipeline

Per visualizzare l'elenco delle esecuzioni di pipeline nell'area di lavoro, aprire prima di tutto [Azure sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **esecuzioni pipeline** per visualizzare l'elenco delle esecuzioni della pipeline.

![Selezionare le esecuzioni della pipeline](./media/how-to-monitor-pipeline-runs/monitor-hub-nav-pipelineruns.png)

## <a name="filtering-your-pipeline-runs"></a>Filtraggio delle esecuzioni della pipeline

È possibile filtrare l'elenco delle esecuzioni di pipeline a quelle a cui si è interessati. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo le esecuzioni di pipeline per la pipeline denominata "festività":

![Pulsante Filter (Filtro)](./media/common/filter-button.png)

![Filtro di esempio](./media/how-to-monitor-pipeline-runs/filter-example.png)

## <a name="viewing-details-about-a-specific-pipeline-run"></a>Visualizzazione dei dettagli relativi a un'esecuzione di pipeline specifica

Per visualizzare i dettagli relativi all'esecuzione della pipeline, selezionare l'esecuzione della pipeline. Quindi visualizzare le esecuzioni attività associate all'esecuzione della pipeline. Se la pipeline è ancora in esecuzione, è possibile monitorare lo stato di avanzamento. 
  
## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle applicazioni, vedere l'articolo [monitorare Apache Spark applicazioni](how-to-monitor-spark-applications.md) . 
