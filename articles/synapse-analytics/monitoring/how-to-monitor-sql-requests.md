---
title: Come monitorare le richieste SQL in sinapsi Studio
description: Informazioni su come monitorare le richieste SQL usando sinapsi Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 7296fec91decaf1bd80829f9f3a743a518fbb7a7
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467405"
---
# <a name="use-synapse-studio-to-monitor-your-sql-requests"></a>Usare sinapsi Studio per monitorare le richieste SQL

Con sinapsi studio è possibile eseguire script SQL sui pool SQL nell'area di lavoro.

Questo articolo illustra come monitorare le richieste SQL, consentendo di tenere sotto controllo lo stato delle richieste in esecuzione e di individuare i dettagli delle richieste cronologiche.

## <a name="access-sql-requests-list"></a>Accedi all'elenco richieste SQL

Per visualizzare l'elenco delle richieste SQL nell'area di lavoro, aprire prima di tutto [sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **richieste SQL** per visualizzare l'elenco delle richieste SQL.

 ![Seleziona richieste SQL](./media/how-to-monitor-sql-requests/monitor-hub-nav-sql-requests.png)

## <a name="select-a-sql-pool"></a>Selezionare un pool SQL

Per impostazione predefinita, in questa visualizzazione viene visualizzata la cronologia delle richieste SQL per il pool SQL senza server incorporato. È possibile selezionare uno dei pool SQL dedicati per visualizzare la cronologia delle richieste SQL del pool.

![Seleziona pool SQL](./media/how-to-monitor-sql-requests/select-pool.png)

## <a name="filter-your-sql-requests"></a>Filtrare le richieste SQL

È possibile filtrare l'elenco delle richieste SQL a quelle che interessano. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo le richieste SQL inviate da `maria@contoso.com` :

![Filtro di esempio](./media/how-to-monitor-sql-requests/filter-example.png)

## <a name="view-details-about-a-specific-sql-request"></a>Visualizza i dettagli di una richiesta SQL specifica

Per visualizzare i dettagli relativi a una delle richieste SQL, aprire la richiesta SQL per passare alla visualizzazione dettagli. Per le richieste complesse in esecuzione su pool SQL dedicati, è possibile monitorare lo stato di avanzamento.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare le esecuzioni della pipeline in sinapsi Studio](how-to-monitor-pipeline-runs.md) . 

Per altre informazioni sul monitoraggio Apache Spark applicazioni, vedere l'articolo [monitorare le applicazioni Apache Spark in sinapsi Studio](how-to-monitor-spark-applications.md) .