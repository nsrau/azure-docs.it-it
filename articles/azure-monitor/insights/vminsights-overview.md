---
title: Descrizione di Monitoraggio di Azure per le macchine virtuali
description: Panoramica di Monitoraggio di Azure per le macchine virtuali, che monitora lo stato e le prestazioni delle macchine virtuali di Azure e individua e mappa automaticamente i componenti dell'applicazione e le relative dipendenze.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: e5eaf2d7075ca09aeb3cfaa2dfea81fd0f8d65ad
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685308"
---
# <a name="overview-of-azure-monitor-for-vms"></a>Panoramica di Monitoraggio di Azure per le macchine virtuali

Monitoraggio di Azure per le macchine virtuali monitora le prestazioni e l'integrità delle macchine virtuali e dei set di scalabilità di macchine virtuali, inclusi i processi in esecuzione e le dipendenze da altre risorse. Consente di offrire prestazioni prevedibili e la disponibilità di applicazioni essenziali, identificando i colli di bottiglia delle prestazioni e i problemi di rete, nonché di comprendere se un problema è correlato ad altre dipendenze.

Monitoraggio di Azure per le macchine virtuali supporta i sistemi operativi Windows e Linux nei seguenti argomenti:

- Macchine virtuali di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchine virtuali ibride connesse ad Azure Arc
- Macchine virtuali locali
- Macchine virtuali ospitate in un altro ambiente cloud
  

Monitoraggio di Azure per le macchine virtuali archivia i dati nei log di monitoraggio di Azure, in modo da consentire l'aggregazione e il filtraggio potenti e analizzare le tendenze dei dati nel tempo. È possibile visualizzare i dati in una singola VM direttamente dalla macchina virtuale oppure è possibile usare monitoraggio di Azure per fornire una visualizzazione aggregata di più macchine virtuali.

![Prospettiva di informazioni dettagliate della macchina virtuale nel portale di Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Prezzi
Non è previsto alcun costo diretto per Monitoraggio di Azure per le macchine virtuali, ma l'attività viene addebitata nell'area di lavoro Log Analytics. Sulla base delle tariffe pubblicate nella [pagina dei prezzi di Monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/), la fatturazione di Monitoraggio di Azure per le macchine virtuali viene effettuata per:

- Dati inseriti dagli agenti e archiviati nell'area di lavoro.
- Dati sullo stato di integrità raccolti dall'integrità Guest (anteprima)
- Regole di avviso basate sui dati di log e di integrità.
- Notifiche inviate dalle regole di avviso.

Le dimensioni del log variano in base alle lunghezze di stringa dei contatori delle prestazioni e possono aumentare con il numero di dischi logici e schede di rete allocate alla VM. Se si usa già Mapping dei servizi, l'unica modifica che si vedrà sono i dati aggiuntivi sulle prestazioni inviati al tipo di dati di monitoraggio di Azure `InsightsMetrics` .


## <a name="configuring-azure-monitor-for-vms"></a>Configurazione di Monitoraggio di Azure per le macchine virtuali
I passaggi per configurare Monitoraggio di Azure per le macchine virtuali sono i seguenti. Per istruzioni dettagliate su ogni passaggio, seguire ogni collegamento:

- [Creare Log Analytics area di lavoro.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Aggiungere la soluzione VMInsights all'area di lavoro.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Installare gli agenti nella macchina virtuale e nel set di scalabilità di macchine virtuali da monitorare.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Passaggi successivi

- Vedere [distribuire monitoraggio di Azure per le macchine virtuali](vminsights-enable-overview.md) per i requisiti e i metodi che consentono il monitoraggio delle macchine virtuali.

