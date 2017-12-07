---
title: Visualizzazione delle dipendenze in Azure Migrate | Microsoft Docs
description: Offre una panoramica dei calcoli delle valutazioni nel servizio Azure Migrate.
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 78e52157-edfd-4b09-923f-f0df0880e0e0
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: raynew
ms.openlocfilehash: a8a8cee327dac8adfb0ae53d101c382ef20599d2
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="dependency-visualization"></a>Visualizzazione delle dipendenze

Il servizio [Azure Migrate](migrate-overview.md) valuta i gruppi di computer locali per la migrazione ad Azure. Per raggruppare i computer, è possibile usare la visualizzazione delle dipendenze. Questo articolo contiene informazioni su questa funzionalità.


## <a name="overview"></a>Panoramica

La visualizzazione delle dipendenze in Azure Migrate consente di creare gruppi per valutare la migrazione in modo più attendibile. Usando questa funzionalità è possibile visualizzare le dipendenze di rete di computer specifici o all'interno di un gruppo. In questo modo si ha la sicurezza che nessuna funzionalità venga persa (o nessun computer dimenticato) nel corso del processo di migrazione, quando app e carichi di lavoro vengono eseguiti su più computer.  

## <a name="how-does-it-work"></a>Come funziona?

Per visualizzare le dipendenze, Azure Migrate usa la soluzione [Elenco dei servizi](../operations-management-suite/operations-management-suite-service-map.md) in [Log Analytics](../log-analytics/log-analytics-overview.md).
- Quando si crea un progetto di migrazione di Azure, nella sottoscrizione viene creata un'area di lavoro di OMS Log Analytics.
- Il nome dell'area di lavoro è quello specificato per il progetto di migrazione, preceduto dal prefisso **migrate-** e, facoltativamente, seguito da un suffisso numerico. 
- Passare all'area di lavoro di Log Analytics dalla sezione **Informazioni di base** della pagina **Panoramica**.
- L'area di lavoro creata è contrassegnata con la chiave **MigrateProject** e il valore del **nome del progetto**. È possibile usare questi valori per eseguire ricerche nel portale di Azure.  

    ![Area di lavoro di Log Analytics](./media/concepts-dependency-visualization/oms-workspace.png)

Per usare la visualizzazione delle dipendenze, è necessario scaricare e installare gli agenti in ogni computer locale che si vuole analizzare.  

## <a name="do-i-need-to-pay-for-it"></a>Il servizio è a pagamento?

Sì. L'area di lavoro di Log Analytics viene creata per impostazione predefinita, ma viene usata solo per la visualizzazione delle dipendenze in Azure Migrate. Se si usa la visualizzazione delle dipendenze, oppure l'area di lavoro all'esterno di Azure Migrate, viene addebitato un costo per l'utilizzo dell'area di lavoro.  Per altre informazioni sul prezzo della soluzione Elenco dei servizi, vedere [questa pagina](https://azure.microsoft.com/pricing/details/insight-analytics/). 

## <a name="how-do-i-manage-the-workspace"></a>Come si gestisce l'area di lavoro?

È possibile usare l'area di lavoro di Log Analytics all'esterno di Azure Migrate. L'area di lavoro non viene eliminata se si rimuove il progetto di migrazione in cui è stata creata. Se non è più necessaria, [eliminarla](../log-analytics/log-analytics-manage-access.md) manualmente.

Non eliminare l'area di lavoro creata da Azure Migrate a meno che non si rimuova il progetto di migrazione. Se si esegue questa operazione, le dipendenze non funzioneranno come previsto.

## <a name="next-steps"></a>Passaggi successivi

[Raggruppare i computer usando le dipendenze dei computer](how-to-create-group-machine-dependencies.md)