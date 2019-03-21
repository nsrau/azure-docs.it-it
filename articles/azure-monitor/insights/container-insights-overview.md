---
title: Panoramica di Monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive Monitoraggio di Azure per contenitori, che consente di monitorare la soluzione relativa alle informazioni dettagliate sui contenitori del servizio Azure Kubernetes, e il valore che offre attraverso il monitoraggio dell'integrità dei cluster del servizio Azure Kubernetes e di Istanze di Container in Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: f292ad5802af6765ec26df7de1aa271e1214e0e9
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983014"
---
# <a name="azure-monitor-for-containers-overview"></a>Panoramica di Monitoraggio di Azure per contenitori

Monitoraggio di Azure per i contenitori è una funzionalità progettata per monitorare le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Vengono raccolti anche i log dei contenitori.  Dopo aver abilitato il monitoraggio dai cluster di Kubernetes, le metriche e i log vengono raccolti automaticamente tramite una versione dell'agente di Log Analytics per Linux inclusa in un contenitore e vengono archiviati nell'area di lavoro di [Log Analytics](../log-query/log-query-overview.md). 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Che cosa fornisce Monitoraggio di Azure per contenitori?

Monitoraggio di Azure per contenitori include varie visualizzazioni predefinite che mostrano i carichi di lavoro nei contenitori residenti e gli elementi che influiscono sull'integrità delle prestazioni del cluster Kubernetes monitorato. È così possibile:  

* Identificare i contenitori servizio Azure Kubernetes in esecuzione nel nodo e il relativo uso medio del processore e della memoria, in modo da individuare facilmente i colli di bottiglia delle risorse.
* Identificare l'utilizzo di processori e memoria dei gruppi di contenitori e dei relativi contenitori ospitati in Istanze di Azure Container.  * Identificare la posizione del contenitore in un controller o in un pod, in modo da visualizzare facilmente le prestazioni complessive del controller o del pod.
* Esaminare l'uso delle risorse dei carichi di lavoro in esecuzione nell'host non correlati ai processi standard che supportano il pod.
* Comprendere il comportamento del cluster con carichi medi e più pesanti. Queste informazioni sono utili per identificare i requisiti di capacità e determinare il carico massimo che può sostenere il cluster. 

## <a name="how-do-i-access-this-feature"></a>Come si accede a questa funzionalità?
È possibile accedere a Monitoraggio di Azure per contenitori in due modi: da Monitoraggio di Azure o direttamente dal cluster servizio Azure Kubernetes selezionato. Da Monitoraggio di Azure si ottiene una prospettiva globale di tutti i contenitori distribuiti, monitorati e non, con la possibilità di cercare e filtrare le sottoscrizioni e i gruppi di risorse e quindi analizzare le informazioni di Monitoraggio di Azure per contenitori dal contenitore selezionato.  In alternativa, è possibile accedere alla funzionalità direttamente da un contenitore servizio Azure Kubernetes selezionato dalla pagina servizio Azure Kubernetes.  

![Panoramica dei metodi di accesso a Monitoraggio di Azure per contenitori](./media/container-insights-overview/azmon-containers-views-1812.png)

Per informazioni sul monitoraggio e la gestione degli host di contenitori Docker e Windows per visualizzare la configurazione, il controllo e l'uso delle risorse, vedere la [soluzione Monitoraggio contenitori](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a monitorare un cluster servizio Azure Kubernetes, esaminare [Come eseguire l'onboarding di Monitoraggio di Azure per contenitori](container-insights-onboard.md) per conoscere i requisiti e i metodi disponibili per abilitare il monitoraggio.  
