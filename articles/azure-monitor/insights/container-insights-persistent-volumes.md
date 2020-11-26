---
title: Configurare il monitoraggio PV con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come configurare il monitoraggio dei cluster Kubernetes con volumi permanenti con monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: e7c547c137fc84e6e6dfb2807b871ef0329a3c13
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186848"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Configurare il monitoraggio PV con monitoraggio di Azure per i contenitori

A partire dalla versione dell'agente *ciprod10052020*, monitoraggio di Azure per i contenitori Integrated Agent supporta ora il monitoraggio dell'utilizzo di PV (volume permanente).

## <a name="pv-metrics"></a>Metriche PV

Monitoraggio di Azure per i contenitori avvia automaticamente il monitoraggio PV raccogliendo le metriche seguenti a intervalli 60sec e archiviando tali metriche nella tabella **InsightMetrics** .

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Spazio utilizzato in byte per un volume permanente specifico con un'attestazione utilizzata da un pod specifico. `pvCapacityBytes` viene ripiegata come dimensione nel campo tag per ridurre i costi di inserimento dei dati e per semplificare le query.|

## <a name="monitor-persistent-volumes"></a>Monitorare i volumi permanenti

Il monitoraggio di Azure per i contenitori include grafici preconfigurati per questa metrica in una cartella di lavoro per ogni cluster. È possibile trovare i grafici nella scheda volume permanente della cartella di lavoro **Dettagli carico** di lavoro direttamente da un cluster AKS selezionando cartelle di lavoro nel riquadro a sinistra e nell'elenco a discesa **Visualizza cartelle di lavoro** di Insight. È anche possibile abilitare un avviso consigliato per l'utilizzo PV, nonché eseguire query su queste metriche in Log Analytics.  

![Esempio di cartella di lavoro PV del monitoraggio di Azure](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulle metriche PV raccolte sono disponibili [qui](./container-insights-agent-config.md).