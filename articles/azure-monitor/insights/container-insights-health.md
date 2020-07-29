---
title: Monitorare le prestazioni del cluster del servizio Kubernetes con Monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come è possibile visualizzare e analizzare l'integrità dei cluster di servizio Azure Kubernetes o non di servizio Azure Kubernetes con Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 94fdb6388134eae326ed5a8e46fbd3d13f8da2d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83649586"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Comprendere l'integrità del cluster del servizio Kubernetes con Monitoraggio di Azure per i contenitori

Monitoraggio di Azure per i contenitori monitora e segnala lo stato di integrità dei componenti dell'infrastruttura gestita e di tutti i nodi in esecuzione su qualsiasi cluster Kubernetes supportato da Monitoraggio di Azure per i contenitori. Questa esperienza si estende oltre lo stato di integrità del cluster calcolato e segnalato nella [vista multicluster](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), in cui ora è possibile capire se uno o più nodi del cluster sono vincolati alle risorse o se un nodo o un pod non è disponibile, influendo su un'applicazione in esecuzione nel cluster in base a metriche curate.

>[!NOTE]
>La funzionalità per l'integrità verrà spostata in un'anteprima privata alla fine di giugno 2020. Per altre informazioni, vedere l'[annuncio sugli aggiornamenti di Azure](https://azure.microsoft.com/updates/ci-health-limited-preview/) seguente.
>

Per informazioni sull'abilitazione di Monitoraggio di Azure per i contenitori, vedere [Come abilitare Monitoraggio di Azure per i contenitori](container-insights-onboard.md).

>[!NOTE]
>Per supportare i cluster del motore del servizio Azure Kubernetes, verificare che soddisfi i seguenti requisiti:
>- È in uso la versione più recente del [client HELM](https://helm.sh/docs/using_helm/).
>- La versione dell'agente in contenitori è *microsoft/oms:ciprod11012019*. Per aggiornare l'agente, vedere l'articolo sull'[aggiornamento dell'agente nel cluster Kubernetes](container-insights-manage-agent.md#how-to-upgrade-the-azure-monitor-for-containers-agent).
>

## <a name="overview"></a>Panoramica

In Monitoraggio di Azure per i contenitori la funzionalità Integrità (anteprima) fornisce il monitoraggio proattivo dell'integrità del cluster Kubernetes per consentire l'identificazione e la diagnosi dei problemi. Offre la possibilità di visualizzare i problemi significativi rilevati. I monitoraggi che valutano l'integrità del cluster vengono eseguiti sull'agente incluso in contenitori nel cluster e i dati relativi all'integrità vengono scritti nella tabella **KubeHealth** nell'area di lavoro Log Analytics. 

L'integrità del cluster Kubernetes si basa su una serie di scenari di monitoraggio organizzati dagli oggetti e dalle astrazioni di Kubernetes seguenti:

- Infrastruttura Kubernetes: fornisce un rollup del server API Kubernetes, dei ReplicaSets e degli oggetti DaemonSet in esecuzione sui nodi distribuiti nel cluster, valutando l'utilizzo della CPU e della memoria e una disponibilità di Pod

    ![Vista del rollup dell'integrità dell'infrastruttura Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nodi: fornisce un rollup dei pool di nodi e lo stato dei singoli nodi in ogni pool, valutando l'utilizzo della CPU e della memoria e lo stato di un nodo come riportato da Kubernetes.

    ![Vista del rollup dell'integrità dei nodi](./media/container-insights-health/health-view-nodes-01.png)

Attualmente è supportato solo lo stato di un kubelet virtuale. Lo stato di integrità per l'utilizzo della CPU e della memoria dei nodi kubelet virtuali è segnalato come **Sconosciuto** dal momento che questi non ricevono un segnale.

Tutti i monitoraggi vengono mostrati in un layout gerarchico nel riquadro della gerarchia dell'integrità, in cui un monitoraggio aggregato che rappresenta l'oggetto o l'astrazione di Kubernetes (ovvero, l'infrastruttura o i nodi di Kubernetes) è il più alto monitoraggio che riflette lo stato combinato di tutti i monitoraggi figlio dipendenti. Gli scenari di monitoraggio principali usati per derivare l'integrità sono:

* Valutare l'utilizzo della CPU dal nodo e dal contenitore.
* Valutare l'utilizzo della memoria dal nodo e dal contenitore.
* Stato dei pod e dei nodi in base al calcolo dello stato Pronto indicato da Kubernetes.

Le icone usate per indicare lo stato sono le seguenti:

|Icona|Significato|  
|--------|-----------|  
|![L'icona con il segno di spunta verde indica lo stato integro](./media/container-insights-health/healthyicon.png)|Operazione riuscita, integrità OK (verde)|  
|![Il triangolo giallo con un punto esclamativo indica un avviso](./media/container-insights-health/warningicon.png)|Avviso (giallo)|  
|![Il pulsante rosso con una X bianca indica lo stato critico](./media/container-insights-health/criticalicon.png)|Critico (rosso)|  
|![Icona disabilitata](./media/container-insights-health/grayicon.png)|Sconosciuto (grigio)|  

## <a name="monitor-configuration"></a>Configurazione del monitoraggio

Per comprendere il comportamento e la configurazione di ogni monitoraggio che supporta Monitoraggio di Azure per la funzionalità di integrità dei contenitori, vedere la [Guida alla configurazione del monitoraggio dell'integrità](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Visualizzare l'integrità di un cluster di servizio Azure Kubernetes o non di servizio Azure Kubernetes

L'accesso alla funzionalità Integrità (anteprima) di Monitoraggio di Azure per i contenitori è disponibile direttamente da un cluster di servizio Azure Kubernetes selezionando **Informazioni dettagliate** dal riquadro a sinistra nel portale di Azure. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**. 

Per visualizzare l'integrità da un cluster non di servizio Azure Kubernetes, ovvero un cluster del motore Azure Kubernetes ospitato in locale o in Azure Stack, selezionare **Monitoraggio di Azure** nel riquadro a sinistra nel portale di Azure. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.  Nella pagina multicluster selezionare il cluster non di servizio Azure Kubernetes dall'elenco.

In Monitoraggio di Azure per i contenitori, nella pagina **Cluster** selezionare **Integrità**.

![Esempio del dashboard dell'integrità del cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Controllare l'integrità del cluster

Quando si apre la pagina relativa all'integrità, per impostazione predefinita nella griglia **Aspetto integrità** viene selezionata **Infrastruttura Kubernetes**.  La griglia riepiloga lo stato corrente del rollup dell'integrità dell'infrastruttura Kubernetes e dei nodi del cluster. La selezione di uno dei due aspetti di integrità consente di aggiornare i risultati nel riquadro della gerarchia di integrità (ovvero il riquadro centrale) e di visualizzare tutti i monitoraggi figlio in un layout gerarchico, visualizzandone lo stato di integrità corrente. Per visualizzare altre informazioni su qualsiasi monitoraggio dipendente, è possibile selezionarne uno e verrà automaticamente visualizzato un riquadro delle proprietà sul lato destro della pagina. 

![Riquadro delle proprietà dell'integrità del cluster](./media/container-insights-health/health-view-property-pane.png)

Nel riquadro delle proprietà è illustrato quanto segue:

- Nella scheda **Panoramica** viene visualizzato lo stato corrente del monitoraggio selezionato, quando l'ultimo monitoraggio è stato calcolato e quando si è verificata l'ultima modifica dello stato. Le informazioni aggiuntive vengono visualizzate a seconda del tipo di monitoraggio selezionato nella gerarchia.

    Se si seleziona un monitoraggio aggregato nel riquadro della gerarchia dell'integrità, nella scheda **Panoramica** del riquadro delle proprietà viene visualizzato un rollup del numero totale di monitoraggi figlio nella gerarchia e il numero di monitoraggi aggregati in uno stato critico, di avviso e integro. 

    ![Scheda Panoramica del riquadro delle proprietà dell'integrità per il monitoraggio aggregato](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se si seleziona un monitoraggio unità nel riquadro della gerarchia dell'integrità, in **Ultima modifica dello stato** vengono visualizzati anche gli esempi precedenti calcolati e segnalati dall'agente in contenitori nelle ultime quattro ore. Si basa sull'unità che monitora il calcolo per confrontare diversi valori consecutivi per determinarne lo stato. Se, ad esempio, è stato selezionato il monitoraggio dell'unità *Pod in stato Pronto*, vengono visualizzati gli ultimi due campioni controllati dal parametro *ConsecutiveSamplesForStateTransition*. Per altre informazioni, vedere la descrizione dettagliata dei [monitoraggi unità](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Scheda Panoramica del riquadro delle proprietà integrità](./media/container-insights-health/health-overview-unit-monitor.png)

    Se l'ora indicata da **Ultima modifica dello stato** è un giorno o un valore più vecchio, è il risultato della mancata modifica dello stato per il monitoraggio. Tuttavia, se l'ultimo campione ricevuto per un monitoraggio unità risale a più di quattro ore prima, probabilmente indica che l'agente in contenitori non ha inviato dati. Se l'agente sa che esiste una risorsa specifica, ad esempio un nodo, ma non ha ricevuto i dati dai monitoraggi di utilizzo della CPU o della memoria del nodo (come esempio), lo stato di integrità del monitoraggio è impostato su **Sconosciuto**.  

- Nella scheda **Config** vengono visualizzate le impostazioni predefinite dei parametri di configurazione (solo per i monitoraggi unità, non per i monitoraggi aggregati) e i relativi valori.
- Nella scheda **Knowledge** (Informazioni) sono contenute informazioni che spiegano il comportamento del monitoraggio e la modalità in cui viene valutata per la condizione di non integrità.

Il monitoraggio dei dati in questa pagina non viene aggiornato automaticamente ed è necessario selezionare **Aggiorna** nella parte superiore della pagina per visualizzare lo stato di integrità più recente ricevuto dal cluster.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query ed esempi predefiniti per la valutazione o la personalizzazione per avvisare, visualizzare o analizzare i cluster.
