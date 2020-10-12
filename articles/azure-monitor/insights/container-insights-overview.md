---
title: Panoramica di Monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive Monitoraggio di Azure per contenitori, che consente di monitorare la soluzione relativa alle informazioni dettagliate sui contenitori del servizio Azure Kubernetes, e il valore che offre attraverso il monitoraggio dell'integrità dei cluster del servizio Azure Kubernetes e di Istanze di Container in Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 215427adc38ebd554ca7ac650ec65a05f5a781e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317158"
---
# <a name="azure-monitor-for-containers-overview"></a>Panoramica di Monitoraggio di Azure per contenitori

Monitoraggio di Azure per contenitori è una funzionalità progettata per monitorare le prestazioni dei carichi di lavoro del contenitore distribuiti in:

- Cluster Managed Kubernetes ospitati in [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md)
- Cluster Kubernetes autogestiti ospitati in Azure con il [motore AKS](https://github.com/Azure/aks-engine)
- [Istanze di Azure Container](../../container-instances/container-instances-overview.md)
- Cluster Kubernetes autogestiti ospitati in [Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910&preserve-view=true) o in locale
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure Arc abilitato Kubernetes](../../azure-arc/kubernetes/overview.md) (anteprima)

Il monitoraggio di Azure per i contenitori supporta i cluster che eseguono il sistema operativo Linux e Windows Server 2019. I runtime del contenitore supportati sono Docker, Moby e qualsiasi runtime compatibile con CRI, ad esempio CRI-O e contenitori.

Il monitoraggio dei contenitori ha un'importanza critica, soprattutto quando si gestisce un cluster di produzione su larga scala con più applicazioni.

Monitoraggio di Azure per contenitori assicura la visibilità sulle prestazioni raccogliendo metriche sulla memoria e sul processore da controller, nodi e contenitori disponibili in Kubernetes tramite l'API per le metriche. Vengono raccolti anche i log dei contenitori.  Dopo aver abilitato il monitoraggio dai cluster Kubernetes, le metriche e i log vengono automaticamente raccolti tramite una versione in contenitori dell'agente di Log Analytics per Linux. Le metriche vengono scritte nell'archivio di metriche e i dati di log vengono scritti nell'archivio dei log associato all'area di lavoro [log Analytics](../log-query/log-query-overview.md) .

![Architettura di monitoraggio di Azure per contenitori](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-azure-monitor-for-containers-provide"></a>Che cosa fornisce Monitoraggio di Azure per contenitori?

Monitoraggio di Azure per i contenitori offre un'esperienza di monitoraggio completa con diverse funzionalità di monitoraggio di Azure. Queste funzionalità consentono di comprendere le prestazioni e l'integrità del cluster Kubernetes che esegue il sistema operativo Linux e Windows Server 2019 e i carichi di lavoro dei contenitori. Con monitoraggio di Azure per i contenitori è possibile:

* Identificare i contenitori servizio Azure Kubernetes in esecuzione nel nodo e il relativo uso medio del processore e della memoria, in modo da individuare facilmente i colli di bottiglia delle risorse.
* Identificare l'utilizzo di processori e memoria dei gruppi di contenitori e dei relativi contenitori ospitati in Istanze di Azure Container.
* Identificare la posizione del contenitore in un controller o in un pod, in modo da visualizzare facilmente le prestazioni complessive del controller o del pod.
* Esaminare l'uso delle risorse dei carichi di lavoro in esecuzione nell'host non correlati ai processi standard che supportano il pod.
* Comprendere il comportamento del cluster con carichi medi e più pesanti. Queste informazioni sono utili per identificare i requisiti di capacità e determinare il carico massimo che può sostenere il cluster.
* Configurare gli avvisi per notificare in modo proattivo l'utente o registrarlo quando l'utilizzo della CPU e della memoria nei nodi o nei contenitori supera le soglie o quando si verifica un cambiamento dello stato di integrità nel cluster nell'infrastruttura o nel rollup dello stato dei nodi.
* Eseguire l'integrazione con [Prometeo](https://prometheus.io/docs/introduction/overview/) per visualizzare le metriche dell'applicazione e del carico di lavoro che raccoglie da nodi e Kubernetes usando le [query](container-insights-log-search.md) per creare avvisi personalizzati, dashboard ed eseguire analisi dettagliate.
* Monitorare i carichi di lavoro dei contenitori [distribuiti nel motore AKS](https://github.com/Azure/aks-engine) locale e nel [motore AKS in Azure stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908&preserve-view=true).
* Monitorare i carichi di lavoro dei contenitori [distribuiti in Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Il supporto per Azure Red Hat OpenShift è una funzionalità di anteprima pubblica al momento.
    >

* Monitorare i carichi di lavoro del contenitore [distribuiti in Azure Arc abilitato Kubernetes (anteprima)](../../azure-arc/kubernetes/overview.md).

Le principali differenze nel monitoraggio di un cluster di Windows Server rispetto a un cluster Linux sono le seguenti:

- Windows non dispone di una metrica RSS di memoria e, di conseguenza, non è disponibile per i nodi e i contenitori di Windows. La metrica [working set](/windows/win32/memory/working-set) è disponibile.
- Le informazioni sulla capacità di archiviazione su disco non sono disponibili per i nodi Windows.
- Vengono monitorati solo gli ambienti Pod, non gli ambienti docker.
- Con la versione di anteprima, sono supportati un massimo di 30 contenitori di Windows Server. Questa limitazione non si applica ai contenitori Linux.

Per informazioni sul monitoraggio del cluster AKS con monitoraggio di Azure per i contenitori, vedere il video seguente che fornisce un approfondimento di livello intermedio.

> [!VIDEO https://www.youtube.com/embed/RjsNmapggPU]

## <a name="how-do-i-access-this-feature"></a>Come si accede a questa funzionalità?

È possibile accedere a Monitoraggio di Azure per contenitori in due modi: da Monitoraggio di Azure o direttamente dal cluster servizio Azure Kubernetes selezionato. Da monitoraggio di Azure è disponibile una prospettiva globale di tutti i contenitori distribuiti, monitorati e non consentiti, che consentono di eseguire ricerche e filtri tra le sottoscrizioni e i gruppi di risorse e quindi di eseguire il drill-through di monitoraggio di Azure per i contenitori dal contenitore selezionato.  In caso contrario, è possibile accedere alla funzionalità direttamente da un contenitore AKS selezionato dalla pagina AKS.

![Panoramica dei metodi di accesso a Monitoraggio di Azure per contenitori](./media/container-insights-overview/azmon-containers-experience.png)

Se si è interessati al monitoraggio e alla gestione degli host di contenitori Docker e Windows in esecuzione all'esterno di AKS per visualizzare la configurazione, il controllo e l'utilizzo delle risorse, vedere la [soluzione di monitoraggio dei contenitori](./containers.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a monitorare il cluster Kubernetes, vedere [come abilitare il monitoraggio di Azure per i contenitori](container-insights-onboard.md) per comprendere i requisiti e i metodi disponibili per abilitare il monitoraggio.

