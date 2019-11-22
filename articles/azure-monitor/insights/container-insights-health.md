---
title: Monitorare l'integrità del cluster Kubernetes con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive come visualizzare e analizzare l'integrità dei cluster AKS e non AKS con monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/18/2019
ms.author: magoedte
ms.openlocfilehash: 08f7cf5a26108608aa3719085d69ec9543f4aa51
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279644"
---
# <a name="understand-kubernetes-cluster-health-with-azure-monitor-for-containers"></a>Informazioni sull'integrità del cluster Kubernetes con monitoraggio di Azure per i contenitori

Con monitoraggio di Azure per i contenitori, monitora e segnala lo stato di integrità dei componenti dell'infrastruttura gestita e di tutti i nodi in esecuzione su qualsiasi cluster Kubernetes supportato da monitoraggio di Azure per i contenitori. Questa esperienza si estende oltre lo stato di integrità del cluster calcolato e segnalato sulla visualizzazione a più [cluster](container-insights-analyze.md#multi-cluster-view-from-azure-monitor), in cui è ora possibile capire se uno o più nodi del cluster sono vincolati alle risorse oppure se un nodo o un pod non è disponibile che può influisca su un'applicazione in esecuzione nel cluster in base a metriche curate.

>[!NOTE]
>La funzionalità di integrità è attualmente in anteprima pubblica.
>

Per informazioni su come abilitare monitoraggio di Azure per i contenitori, vedere caricare il [monitoraggio di Azure per i contenitori](container-insights-onboard.md).

>[!NOTE]
>Per supportare i cluster del motore AKS, verificare che soddisfi i requisiti seguenti:
>- Usa la versione più recente del [client Helm](https://helm.sh/docs/using_helm/).
>- La versione dell'agente in contenitori è *Microsoft/OMS: ciprod11012019*. Per aggiornare l'agente, vedere [aggiornamento dell'agente nel cluster Kubernetes](container-insights-manage-agent.md#upgrading-agent-on-monitored-kubernetes-cluster).
>

## <a name="overview"></a>Overview

In monitoraggio di Azure per i contenitori la funzionalità integrità (anteprima) fornisce il monitoraggio proattivo dello stato del cluster Kubernetes per consentire l'identificazione e la diagnosi dei problemi. Offre la possibilità di visualizzare problemi significativi rilevati. I monitoraggi che valutano l'integrità del cluster vengono eseguiti nell'agente in contenitori nel cluster e i dati di integrità vengono scritti nella tabella **KubeHealth** dell'area di lavoro log Analytics. 

L'integrità del cluster Kubernetes si basa su una serie di scenari di monitoraggio organizzati dagli oggetti e dalle astrazioni di Kubernetes seguenti:

- Infrastruttura Kubernetes: fornisce un rollup del server API Kubernetes, ReplicaSets e gli elementi daemonset in esecuzione nei nodi distribuiti nel cluster, valutando l'utilizzo della CPU e della memoria e una disponibilità di Pod

    ![Visualizzazione rollup stato dell'infrastruttura Kubernetes](./media/container-insights-health/health-view-kube-infra-01.png)

- Nodi: fornisce un rollup dei pool di nodi e lo stato dei singoli nodi in ogni pool, valutando l'utilizzo della CPU e della memoria e lo stato di un nodo come riportato da Kubernetes.

    ![Visualizzazione rollup stato nodi](./media/container-insights-health/health-view-nodes-01.png)

Attualmente è supportato solo lo stato di un kubelet virtuale. Lo stato di integrità per l'utilizzo della CPU e della memoria dei nodi kublet virtuali viene segnalato come **sconosciuto**, dal momento che non viene ricevuto un segnale.

Tutti i monitoraggi sono mostrati in un layout gerarchico nel riquadro della gerarchia di integrità, in cui un monitoraggio aggregato che rappresenta l'oggetto o l'astrazione Kubernetes (ovvero l'infrastruttura o i nodi di Kubernetes) è il monitor più alto che riflette lo stato combinato di tutti i monitoraggi figlio dipendenti. Gli scenari di monitoraggio principali utilizzati per derivare l'integrità sono:

* Valutare l'utilizzo della CPU dal nodo e dal contenitore.
* Valutare l'utilizzo della memoria dal nodo e dal contenitore.
* Stato dei pod e dei nodi in base al calcolo dello stato pronto indicato da Kubernetes.

Le icone utilizzate per indicare lo stato sono le seguenti:

|Icona|Significato|  
|--------|-----------|  
|![Icona di segno di spunta verde indica integro](./media/container-insights-health/healthyicon.png)|Operazione riuscita, integrità OK (verde)|  
|![Il triangolo giallo e il punto esclamativo è un avviso](./media/container-insights-health/warningicon.png)|Avviso (giallo)|  
|![Il pulsante rosso con una X bianca indica lo stato critico](./media/container-insights-health/criticalicon.png)|Critico (rosso)|  
|![Icona in grigio](./media/container-insights-health/grayicon.png)|Sconosciuto (grigio)|  

## <a name="monitor-configuration"></a>Configurazione del monitoraggio

Per comprendere il comportamento e la configurazione di ogni monitoraggio che supporta monitoraggio di Azure per la funzionalità di integrità dei contenitori, vedere la [Guida alla configurazione di Health Monitor](container-insights-health-monitors-config.md).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="view-health-of-an-aks-or-non-aks-cluster"></a>Visualizzare l'integrità di un cluster AKS o non AKS

L'accesso alla funzionalità di monitoraggio di Azure per i contenitori (anteprima) è disponibile direttamente da un cluster AKS selezionando **Insights** nel riquadro a sinistra nel portale di Azure. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**. 

Per visualizzare l'integrità da un cluster non AKS, ovvero un cluster del motore AKS ospitato in locale o in Azure Stack, selezionare **monitoraggio di Azure** nel riquadro sinistro della portale di Azure. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.  Nella pagina multicluster selezionare il cluster non AKS dall'elenco.

In monitoraggio di Azure per i contenitori, nella pagina **cluster** selezionare **stato**.

![Esempio di dashboard integrità cluster](./media/container-insights-health/container-insights-health-page.png)

## <a name="review-cluster-health"></a>Esaminare l'integrità del cluster

Quando viene visualizzata la pagina integrità, per impostazione predefinita l' **infrastruttura Kubernetes** è selezionata nella griglia dell' **aspetto dell'integrità** .  La griglia riepiloga lo stato corrente del rollup dell'integrità dell'infrastruttura Kubernetes e dei nodi del cluster. La selezione di uno dei due aspetti di integrità consente di aggiornare i risultati nel riquadro della gerarchia di integrità (ovvero il riquadro centrale) e di visualizzare tutti i monitoraggi figlio in un layout gerarchico, visualizzandone lo stato di integrità corrente. Per visualizzare ulteriori informazioni su qualsiasi monitoraggio dipendente, è possibile selezionarne uno e un riquadro delle proprietà visualizzato automaticamente sul lato destro della pagina. 

![Riquadro Proprietà integrità cluster](./media/container-insights-health/health-view-property-pane.png)

Nel riquadro delle proprietà vengono illustrate le operazioni seguenti:

- Nella scheda **Panoramica** viene visualizzato lo stato corrente del monitoraggio selezionato, al momento dell'ultimo calcolo del monitoraggio e al momento in cui si è verificata l'ultima modifica dello stato. Le informazioni aggiuntive vengono visualizzate a seconda del tipo di monitoraggio selezionato nella gerarchia.

    Se si seleziona un monitoraggio aggregato nel riquadro gerarchia di integrità, nella scheda **Panoramica** del riquadro proprietà viene visualizzato un rollup del numero totale di monitoraggi figlio nella gerarchia e il numero di monitoraggi aggregati che si trovano in uno stato critico, di avviso e integro. 

    ![Scheda Panoramica del riquadro delle proprietà di integrità per il monitoraggio aggregato](./media/container-insights-health/health-overview-aggregate-monitor.png)

    Se si seleziona un monitoraggio unità nel riquadro gerarchia di integrità, viene visualizzato anche in **ultimo stato modificare** gli esempi precedenti calcolati e segnalati dall'agente in contenitori nelle ultime quattro ore. Si basa sull'unità che monitora il calcolo per confrontare diversi valori consecutivi per determinarne lo stato. Se, ad esempio, è stato selezionato il monitoraggio unità *stato pronto per pod* , vengono visualizzati gli ultimi due campioni controllati dal parametro *ConsecutiveSamplesForStateTransition*. Per ulteriori informazioni, vedere la descrizione dettagliata dei [monitoraggi unità](container-insights-health-monitors-config.md#unit-monitors).
    
    ![Scheda Panoramica del riquadro Proprietà integrità](./media/container-insights-health/health-overview-unit-monitor.png)

    Se l'ora indicata dall' **Ultima modifica dello stato** è un giorno o un valore più vecchio, è il risultato di nessuna modifica dello stato per il monitoraggio. Tuttavia, se l'ultimo campione ricevuto per un monitoraggio unità è più vecchio di quattro ore, probabilmente indica che l'agente in contenitori non ha inviato dati. Se l'agente sa che esiste una risorsa specifica, ad esempio un nodo, ma non ha ricevuto i dati dai monitoraggi di utilizzo della CPU o della memoria del nodo (come esempio), lo stato di integrità del monitoraggio è impostato su **sconosciuto**.  

- Nella scheda**config** vengono visualizzate le impostazioni predefinite dei parametri di configurazione (solo per i monitoraggi unità, non per i monitoraggi aggregati) e i relativi valori.
- Nella scheda **Knowledge** base sono incluse informazioni che spiegano il comportamento del monitoraggio e il modo in cui viene valutata la condizione di tipo non integro.

Il monitoraggio dei dati in questa pagina non viene aggiornato automaticamente ed è necessario selezionare **Aggiorna** nella parte superiore della pagina per visualizzare lo stato di integrità più recente ricevuto dal cluster.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti da valutare o personalizzare per visualizzare avvisi, visualizzare o analizzare i cluster.