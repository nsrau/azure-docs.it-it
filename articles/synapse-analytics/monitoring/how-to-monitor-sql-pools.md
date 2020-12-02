---
title: Come monitorare i pool SQL in sinapsi Studio
description: Informazioni su come monitorare i pool SQL usando sinapsi Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 63b63526333435387c3ff5b5c9d5599ec851c1a8
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467420"
---
# <a name="use-synapse-studio-to-monitor-your-sql-pools"></a>Usare sinapsi Studio per monitorare i pool SQL

Con sinapsi studio è possibile eseguire script SQL sui pool SQL nell'area di lavoro.

Questo articolo illustra come monitorare i pool SQL, consentendo di tenere d'occhio lo stato e le attività dei pool.

## <a name="access-sql-pools-list"></a>Accedi all'elenco di pool SQL

Per visualizzare l'elenco dei pool SQL nell'area di lavoro, aprire prima di tutto [sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **pool SQL** per visualizzare l'elenco dei pool SQL.

 ![Seleziona pool SQL](./media/how-to-monitor-sql-pools/monitor-hub-nav-sql-pools.png)

## <a name="filter-your-sql-pools"></a>Filtrare i pool SQL

È possibile filtrare l'elenco di pool SQL con quelli che interessano. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo i pool SQL che contengono il nome "salesrecords":

![Filtro di esempio](./media/how-to-monitor-sql-pools/filter-example.png)

## <a name="view-details-about-a-specific-sql-pool"></a>Visualizzare i dettagli relativi a un pool SQL specifico

Per visualizzare i dettagli relativi a uno dei pool SQL, selezionare il pool SQL per visualizzare i dettagli.

![Dettagli pool SQL](./media/how-to-monitor-sql-pools/sql-pool-details.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare le esecuzioni della pipeline in sinapsi Studio](how-to-monitor-pipeline-runs.md) . 

Per altre informazioni sul monitoraggio delle richieste SQL, vedere l'articolo [monitorare le richieste SQL in sinapsi Studio](how-to-monitor-sql-requests.md) .