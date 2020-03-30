---
title: Panoramica di Monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive Monitoraggio di Azure per contenitori, che consente di monitorare la soluzione relativa alle informazioni dettagliate sui contenitori del servizio Azure Kubernetes, e il valore che offre attraverso il monitoraggio dell'integrità dei cluster del servizio Azure Kubernetes e di Istanze di Container in Azure.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 3ff2c35ae9f5838447ce90e2a020649427920a43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275230"
---
# <a name="azure-monitor-for-containers-overview"></a>Panoramica di Monitoraggio di Azure per contenitori

Monitoraggio di Azure per i contenitori è una funzionalità progettata per monitorare le prestazioni dei carichi di lavoro dei contenitori distribuiti in:Azure Monitor for containers is a feature designed to monitor the performance of container workloads deployed to:

- Cluster Kubernetes gestiti ospitati nel [servizio Azure Kubernetes (AKS)Managed Kubernetes clusters hosted on Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Cluster Kubernetes autogestiti ospitati in Azure con [il motore AKS](https://github.com/Azure/aks-engine)
- [Istanze del contenitore di AzureAzure Container Instances](../../container-instances/container-instances-overview.md)
- Cluster Kubernetes autogestiti ospitati in [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) o in locale
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Monitoraggio di Azure per i contenitori supporta i cluster che eseguono il sistema operativo Linux e Windows Server 2019.Azure Monitor for containers supports clusters running the Linux and Windows Server 2019 operating system. 

Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Vengono raccolti anche i log dei contenitori.  Dopo aver abilitato il monitoraggio dai cluster Kubernetes, le metriche e i log vengono raccolti automaticamente tramite una versione in contenitori dell'agente log Analytics per Linux.After you enable monitoring from Kubernetes clusters, metrics and logs are automatically collected for you through a containerized version of the Log Analytics agent for Linux. Le metriche vengono scritte nell'archivio delle metriche e i dati di log vengono scritti nell'archivio dei log associato all'area di lavoro di [Log Analytics.](../log-query/log-query-overview.md) 

![Architettura di Monitoraggio di Azure per i contenitoriAzure Monitor for containers architecture](./media/container-insights-overview/azmon-containers-architecture-01.png)
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>Che cosa fornisce Monitoraggio di Azure per contenitori?

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio completa usando diverse funzionalità di Monitoraggio di Azure.Azure Monitor for containers deliver a comprehensive monitoring experience using different features of Azure Monitor. Queste funzionalità consentono di comprendere le prestazioni e l'integrità del cluster Kubernetes che esegue il sistema operativo Linux e Windows Server 2019 e i carichi di lavoro dei contenitori. Con Monitor di Azure per i contenitori è possibile:With Azure Monitor for containers you can:

* Identificare i contenitori servizio Azure Kubernetes in esecuzione nel nodo e il relativo uso medio del processore e della memoria, in modo da individuare facilmente i colli di bottiglia delle risorse.
* Identificare l'utilizzo di processori e memoria dei gruppi di contenitori e dei relativi contenitori ospitati in Istanze di Azure Container.  
* Identificare la posizione del contenitore in un controller o in un pod, in modo da visualizzare facilmente le prestazioni complessive del controller o del pod. 
* Esaminare l'uso delle risorse dei carichi di lavoro in esecuzione nell'host non correlati ai processi standard che supportano il pod.
* Comprendere il comportamento del cluster con carichi medi e più pesanti. Queste informazioni sono utili per identificare i requisiti di capacità e determinare il carico massimo che può sostenere il cluster. 
* Configurare gli avvisi per notificare o registrare in modo proattivo quando l'utilizzo della CPU e della memoria su nodi o contenitori supera le soglie o quando si verifica una modifica dello stato di integrità nel cluster nel rollup dello stato dell'infrastruttura o dei nodi.
* Integrazione con [Prometheus](https://prometheus.io/docs/introduction/overview/) per visualizzare le metriche dell'applicazione e del carico di lavoro raccolte da nodi e Kubernetes utilizzando [le query](container-insights-log-search.md) per creare avvisi personalizzati, dashboard e analisi dettagliate per l'esecuzione di analisi dettagliate.
* Monitorare i carichi di lavoro dei contenitori [distribuiti nel motore AKS](https://github.com/Azure/aks-engine) locale e nel [motore AKS in Azure Stack.](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)
* Monitorare i carichi di lavoro del contenitore [distribuiti in Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Il supporto per Azure Red Hat OpenShift è una funzionalità in anteprima pubblica in questo momento.
    >

Le principali differenze nel monitoraggio di un cluster di Windows Server rispetto a un cluster Linux sono le seguenti:

- La metrica RSS di memoria non è disponibile per i contenitori e i nodi di Windows.Memory RSS metric isn't available for Windows node and containers.
- Le informazioni sulla capacità di archiviazione su disco non sono disponibili per i nodi Windows.Disk storage capacity information isn't available for Windows nodes.
- I log del contenitore non sono disponibili per i contenitori in esecuzione nei nodi Windows.Container logs aren't available for containers running in Windows nodes.
- Il supporto delle funzionalità Live Data (anteprima) è disponibile, ad eccezione dei log del contenitore di Windows.
- Vengono monitorati solo gli ambienti pod, non gli ambienti Docker.
- Con la versione di anteprima, sono supportati un massimo di 30 contenitori di Windows Server. Questa limitazione non si applica ai contenitori Linux.This limitation doesn't apply to Linux containers. 

Guarda il video seguente che offre un'immersione approfondita di livello intermedio per scoprire come monitorare il cluster AKS con Monitoraggio di Azure per i contenitori.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Come si accede a questa funzionalità?

È possibile accedere a Monitoraggio di Azure per contenitori in due modi: da Monitoraggio di Azure o direttamente dal cluster servizio Azure Kubernetes selezionato. Da Monitoraggio di Azure si ha una prospettiva globale di tutti i contenitori distribuiti, che vengono monitorati e che non lo sono, consentendo di cercare e filtrare le sottoscrizioni e i gruppi di risorse, quindi eseguire il drill-down di Monitoraggio di Azure per i contenitori dal contenitore selezionato.  In caso contrario, è possibile accedere alla funzionalità direttamente da un contenitore AKS selezionato dalla pagina AKS.  

![Panoramica dei metodi di accesso a Monitoraggio di Azure per contenitori](./media/container-insights-overview/azmon-containers-experience.png)

Se si è interessati al monitoraggio e alla gestione degli host contenitore Docker e Windows in esecuzione all'esterno di AKS per visualizzare la configurazione, il controllo e l'utilizzo delle risorse, vedere la [soluzione di monitoraggio dei contenitori](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare il cluster Kubernetes, vedere [Come abilitare Monitoraggio di Azure per](container-insights-onboard.md) i contenitori per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio. 
