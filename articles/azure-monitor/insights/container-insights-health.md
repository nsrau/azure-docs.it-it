---
title: Monitorare l'integrità del cluster Kubernetes con Monitoraggio di Azure per i contenitori. Documenti Microsoft
description: Questo articolo descrive come visualizzare e analizzare l'integrità dei cluster AKS e non AKS con Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 12/01/2019
ms.openlocfilehash: f50ef13efca78bbb5285b99759b8111dc1915ad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843991"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Informazioni sull'integrità del cluster Kubernetes con Monitoraggio di Azure per i contenitoriUnderstand Kubernetes cluster health with Azure Monitor for containers

Con Monitoraggio di Azure per i contenitori, monitora e segnala lo stato di integrità dei componenti dell'infrastruttura gestita e di tutti i nodi in esecuzione in qualsiasi cluster Kubernetes supportato da Monitoraggio di Azure per i contenitori. Questa esperienza si estende oltre lo stato di integrità del cluster calcolato e riportato nella [visualizzazione multicluster,](container-insights-analyze.md#multi-cluster-view-from-azure-monitor)in cui ora è possibile comprendere se uno o più nodi del cluster sono vincolati alle risorse o se un nodo o un pod non è disponibile che potrebbe influire su un'applicazione in esecuzione nel cluster in base a metriche curate.

>[!NOTE]
>La funzionalità Integrità è attualmente in anteprima pubblica.
>

Per informazioni su come abilitare Monitoraggio di Azure per i contenitori, vedere [Onboard Azure Monitor per i contenitori.](container-insights-onboard.md)

>[!NOTE]
>Per supportare i cluster del motore AKS, verificare che soddisfi gli elementi seguenti:To support AKS Engine clusters, verify it meets the following:
>- Utilizza la versione più recente del [client HELM.](https://helm.sh/docs/using_helm/)
>- La versione dell'agente containerizzato è *microsoft/oms:ciprod11012019*. Per aggiornare l'agente, vedere [Aggiornamento dell'agente nel cluster Kubernetes](container-insights-manage-agent.md#upgrade-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Panoramica

In Monitoraggio di Azure per i contenitori, la funzionalità integrità (anteprima) fornisce il monitoraggio dell'integrità proattivo del cluster Kubernetes per identificare e diagnosticare i problemi. Ti dà la possibilità di visualizzare i problemi significativi rilevati. Monitora la valutazione dell'integrità del cluster eseguito nell'agente containerizzato nel cluster e i dati di integrità vengono scritti nella tabella **KubeHealth** nell'area di lavoro di Log Analytics. 

L'integrità del cluster Kubernetes si basa su una serie di scenari di monitoraggio organizzati dai seguenti oggetti e astrazioni Kubernetes:

- Infrastruttura Kubernetes: fornisce un rollup del server API Kubernetes, ReplicaSets e DaemonSets in esecuzione sui nodi distribuiti nel cluster valutando l'utilizzo della CPU e della memoria e una disponibilità di Pods

    ![Visualizzazione rollup dello stato dell'infrastruttura Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nodi: fornisce un rollup dei pool di nodi e lo stato dei singoli nodi in ogni pool, valutando l'utilizzo della CPU e della memoria e lo stato di un nodo come riportato da Kubernetes.

    ![Visualizzazione rollup integrità nodi](./media/container-insights-health/health-view-nodes-01.png)

Attualmente, è supportato solo lo stato di un kubelet virtuale. Lo stato di integrità per l'utilizzo della CPU e della memoria dei nodi kublet virtuali viene segnalato come **Sconosciuto**, poiché non viene ricevuto un segnale.

Tutti i monitoraggi vengono visualizzati in un layout gerarchico nel riquadro Gerarchia integrità, in cui un monitoraggio aggregato che rappresenta l'oggetto Kubernetes o l'astrazione (ovvero l'infrastruttura Kubernetes o i nodi) è il monitoraggio più importante che riflette l'integrità combinata di tutti monitor figlio dipendente. Gli scenari di monitoraggio principali utilizzati per derivare l'integrità sono:The key monitoring scenarios used to derive health are:

* Valutare l'utilizzo della CPU dal nodo e dal contenitore.
* Valutare l'utilizzo della memoria dal nodo e dal contenitore.
* Stato di Pod e Nodi in base al calcolo del loro stato pronto segnalato da Kubernetes.

Le icone utilizzate per indicare lo stato sono le seguenti:

|Icona|Significato|  
|--------|-----------|  
|![L'icona con il segno di spunta verde indica lo stato integro](./media/container-insights-health/healthyicon.png)|Operazione riuscita, integrità corretta (verde)|  
|![Il triangolo giallo con un punto esclamativo indica un avviso](./media/container-insights-health/warningicon.png)|Avviso (giallo)|  
|![Il pulsante rosso con una X bianca indica lo stato critico](./media/container-insights-health/criticalicon.png)|Stato critico (rosso)|  
|![Icona in grigio](./media/container-insights-health/grayicon.png)|Sconosciuto (grigio)|  

## <a name="monitor-configuration"></a>Configurazione del monitor

Per comprendere il comportamento e la configurazione di ogni monitoraggio che supporta la funzionalità di integrità di Monitoraggio di Azure per i contenitori, vedere Guida alla configurazione di [Monitoraggio dell'integrità](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com)di Azure . 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Visualizzare l'integrità di un cluster AKS o non AKSView health of an AKS or non-AKS cluster

L'accesso alla funzionalità Monitoraggio di Azure per i contenitori di integrità (anteprima) è disponibile direttamente da un cluster AKS selezionando Insights nel riquadro sinistro del portale di Azure.Access to the Azure Monitor for containers Health (preview) feature is available directly from an AKS cluster by selecting **Insights** from the left pane in the Azure portal. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**. 

Per visualizzare l'integrità da un cluster non AKS, ovvero un cluster del motore AKS ospitato in locale o in Azure Stack, selezionare **Monitoraggio di Azure** nel riquadro sinistro del portale di Azure.To view health from a non-AKS cluster, that is an AKS Engine cluster hosted on-premises or on Azure Stack, select Azure Monitor from the left pane in the Azure portal. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.  Nella pagina multi-cluster selezionare il cluster non AKS dall'elenco.

In Monitoraggio di Azure per i contenitori selezionare **Integrità**nella pagina **Cluster.**

![Esempio di dashboard di integrità del cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Esaminare l'integrità del clusterReview cluster health

Quando si apre la pagina Integrità, per impostazione predefinita **l'infrastruttura Kubernetes** è selezionata nella griglia **Aspetto integrità.**  La griglia riepiloga lo stato di rollup dello stato di integrità corrente dell'infrastruttura Kubernetes e dei nodi del cluster. La selezione di entrambi gli aspetti relativi all'integrità aggiorna i risultati nel riquadro Gerarchia integrità, ovvero nel riquadro centrale, e mostra tutti i monitoraggi figlio in un layout gerarchico, visualizzando lo stato di integrità corrente. Per visualizzare ulteriori informazioni su qualsiasi monitoraggio dipendente, è possibile selezionarne uno e un riquadro delle proprietà viene visualizzato automaticamente sul lato destro della pagina. 

![Riquadro delle proprietà di integrità del cluster](./media/container-insights-health/health-view-property-pane.png)

Nel riquadro delle proprietà, si apprende quanto segue:

- Nella scheda **Panoramica,** mostra lo stato corrente del monitoraggio selezionato, la data dell'ultimo calcolo del monitoraggio e la data dell'ultima modifica dello stato. Ulteriori informazioni vengono visualizzate a seconda del tipo di monitoraggio selezionato nella gerarchia.

    Se si seleziona un monitoraggio aggregato nel riquadro Gerarchia integrità, nella scheda **Panoramica** del riquadro delle proprietà viene visualizzato un rollup del numero totale di monitoraggi figlio nella gerarchia e del numero di monitoraggi aggregati in uno stato critico, di avviso e integro. 

    ![Scheda Panoramica del riquadro delle proprietà dell'integrità per il monitoraggio aggregato](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se si seleziona un monitoraggio unità nel riquadro Gerarchia integrità, viene visualizzato anche in **Ultima modifica dello stato** dei campioni precedenti calcolati e segnalati dall'agente in contenitore nelle ultime quattro ore. Questo si basa sul calcolo dei monitoraggi unità per confrontare diversi valori consecutivi per determinarne lo stato. Ad esempio, se è stato selezionato il monitor unità *di stato Pod ready,* vengono visualizzati gli ultimi due campioni controllati dal parametro *ConsecutiveSamplesForStateTransition*. Per ulteriori informazioni, vedere la descrizione dettagliata dei [monitoraggi unità](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Scheda Panoramica del riquadro delle proprietà dell'integrità](./media/container-insights-health/health-overview-unit-monitor.png)

    Se l'ora riportata da **Ultima modifica dello stato** è un giorno o più vecchio, è il risultato di nessuna modifica dello stato per il monitoraggio. Tuttavia, se l'ultimo campione ricevuto per un monitoraggio unità ha più di quattro ore, è probabile che l'agente containerizzato non abbia inviato dati. Se l'agente sa che esiste una determinata risorsa, ad esempio un nodo, ma non ha ricevuto dati dalla CPU del nodo o dai monitoraggi per l'utilizzo della memoria (ad esempio), lo stato di integrità del monitoraggio è impostato su **Sconosciuto.**  

- Nella scheda**Config** vengono visualizzate le impostazioni predefinite dei parametri di configurazione (solo per i monitoraggi unità, non i monitoraggi aggregati) e i relativi valori.
- Nella scheda **Knowledge Base** contiene informazioni che spiegano il comportamento del monitoraggio e come viene valutato per la condizione non integra.

I dati di monitoraggio in questa pagina non vengono aggiornati automaticamente ed è necessario selezionare **Aggiorna** nella parte superiore della pagina per visualizzare lo stato di integrità più recente ricevuto dal cluster.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinite ed esempi per valutare o personalizzare l'avviso, la visualizzazione o l'analisi dei cluster.
