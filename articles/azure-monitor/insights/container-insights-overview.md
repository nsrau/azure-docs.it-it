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
ms.date: 08/02/2019
ms.author: magoedte
ms.openlocfilehash: e1fca1532352c4955f478df70f818ee3151053c1
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742215"
---
# <a name="azure-monitor-for-containers-overview"></a>Panoramica di Monitoraggio di Azure per contenitori

Monitoraggio di Azure per i contenitori è una funzionalità progettata per monitorare le prestazioni dei carichi di lavoro dei contenitori distribuiti in Istanze di Azure Container o in cluster Kubernetes gestiti ospitati nel servizio Azure Kubernetes. Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Vengono raccolti anche i log dei contenitori.  Dopo aver abilitato il monitoraggio dai cluster Kubernetes, le metriche e i log vengono automaticamente raccolti tramite una versione in contenitori dell'agente di Log Analytics per Linux. Le metriche vengono scritte nell'archivio di metriche e i dati di log vengono scritti nell'archivio dei log associato all'area di lavoro [log Analytics](../log-query/log-query-overview.md) . 

![Architettura di monitoraggio di Azure per contenitori](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Che cosa fornisce Monitoraggio di Azure per contenitori?

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio completa con diverse funzionalità di monitoraggio di Azure che consentono di comprendere le prestazioni e l'integrità del cluster Kubernetes e dei carichi di lavoro dei contenitori. Con monitoraggio di Azure per i contenitori è possibile:

* Identificare i contenitori servizio Azure Kubernetes in esecuzione nel nodo e il relativo uso medio del processore e della memoria, in modo da individuare facilmente i colli di bottiglia delle risorse.
* Identificare l'utilizzo di processori e memoria dei gruppi di contenitori e dei relativi contenitori ospitati in Istanze di Azure Container.  
* Identificare la posizione del contenitore in un controller o in un pod, in modo da visualizzare facilmente le prestazioni complessive del controller o del pod.
* Esaminare l'uso delle risorse dei carichi di lavoro in esecuzione nell'host non correlati ai processi standard che supportano il pod.
* Comprendere il comportamento del cluster con carichi medi e più pesanti. Queste informazioni sono utili per identificare i requisiti di capacità e determinare il carico massimo che può sostenere il cluster. 
* Configurare gli avvisi per notificare in modo proattivo o registrarlo quando l'utilizzo della CPU e della memoria nei nodi o nei contenitori supera le soglie.
* Eseguire l'integrazione con [Prometeo](https://prometheus.io/docs/introduction/overview/) per visualizzare le metriche dell'applicazione e del carico di lavoro che raccoglie da nodi e Kubernetes usando le [query](container-insights-log-search.md) per creare avvisi personalizzati, dashboard ed eseguire analisi dettagliate.

    >[!NOTE]
    >Il supporto per Prometheus è una funzionalità di anteprima pubblica al momento.
    >

* Monitorare i carichi di lavoro dei contenitori [distribuiti nel motore AKS](https://github.com/microsoft/OMS-docker/tree/aks-engine).

Per informazioni sul monitoraggio del cluster AKS con monitoraggio di Azure per i contenitori, vedere il video seguente che fornisce un approfondimento di livello intermedio.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Come si accede a questa funzionalità?

È possibile accedere a Monitoraggio di Azure per contenitori in due modi: da Monitoraggio di Azure o direttamente dal cluster servizio Azure Kubernetes selezionato. Da monitoraggio di Azure è disponibile una prospettiva globale di tutti i contenitori distribuiti, monitorati e non consentiti, che consentono di eseguire ricerche e filtri tra le sottoscrizioni e i gruppi di risorse e quindi di esaminare monitoraggio di Azure per i contenitori dal contenitore selezionato.  In caso contrario, è possibile accedere alla funzionalità direttamente da un contenitore AKS selezionato dalla pagina AKS.  

![Panoramica dei metodi di accesso a Monitoraggio di Azure per contenitori](./media/container-insights-overview/azmon-containers-experience.png)

Se si è interessati al monitoraggio e alla gestione degli host di contenitori Docker e Windows in esecuzione all'esterno di AKS per visualizzare la configurazione, il controllo e l'utilizzo delle risorse, vedere la [soluzione di monitoraggio dei contenitori](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare il monitoraggio del cluster AKS, vedere [come abilitare il monitoraggio di Azure per i contenitori](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.  
