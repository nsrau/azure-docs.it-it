---
title: Visualizzare le metriche in tempo reale con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive la visualizzazione in tempo reale delle metriche senza usare kubectl con monitoraggio di Azure per i contenitori.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/15/2019
ms.author: magoedte
ms.openlocfilehash: 4608058d347888ab77bd4303600f00b270d6fed5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73514602"
---
# <a name="how-to-view-metrics-in-real-time"></a>Come visualizzare le metriche in tempo reale

Il monitoraggio di Azure per i contenitori Live Data (anteprima) funzionalità consente di visualizzare le metriche sullo stato del nodo e del pod in un cluster in tempo reale. Emula l'accesso diretto ai comandi `kubectl top nodes`, `kubectl get pods –all-namespaces`e `kubectl get nodes` per chiamare, analizzare e visualizzare i dati nei grafici delle prestazioni inclusi in questa analisi. 

Questo articolo fornisce una panoramica dettagliata e consente di comprendere come usare questa funzionalità.  

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzionalità si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, tra cui Azure Cina. Attualmente non è disponibile in Azure per enti pubblici statunitensi.

Per informazioni sull'impostazione o la risoluzione dei problemi relativi alla funzionalità dati in tempo reale (anteprima), vedere la [Guida all'installazione](container-insights-livedata-setup.md).

## <a name="how-it-works"></a>Funzionamento 

La funzionalità dati in tempo reale (anteprima) consente di accedere direttamente all'API Kubernetes e altre informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Questa funzionalità consente di eseguire un'operazione di polling sugli endpoint di metrica (inclusi `/api/v1/nodes`, `/apis/metrics.k8s.io/v1beta1/nodes`e `/api/v1/pods`), che è ogni cinque secondi per impostazione predefinita. Questi dati vengono memorizzati nella cache del browser e grafici nei quattro grafici delle prestazioni inclusi in monitoraggio di Azure per i contenitori nella scheda **cluster** selezionando **Go Live (anteprima)** . Ogni polling successivo viene tracciato in una finestra di visualizzazione di cinque minuti in sequenza. 

![Opzione Go Live nella visualizzazione cluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

L'intervallo di polling viene configurato dall'elenco a discesa **Imposta intervallo** , che consente di impostare il polling per i nuovi dati ogni 1, 5, 15 e 30 secondi. 

![Intervallo di polling a discesa Go Live](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.ping.png)

>[!IMPORTANT]
>È consigliabile impostare l'intervallo di polling su un secondo durante la risoluzione di un problema per un breve periodo di tempo. Queste richieste possono influisca sulla disponibilità e sulla limitazione dell'API Kubernetes nel cluster. Successivamente, riconfigurare in un intervallo di polling più lungo. 

>[!IMPORTANT]
>Nessun dato viene archiviato in modo permanente durante il funzionamento di questa funzionalità. Tutte le informazioni acquisite durante questa sessione vengono eliminate immediatamente quando si chiude il browser o si esce dalla funzionalità. I dati rimangono presenti solo per la visualizzazione all'interno della finestra di cinque minuti; anche le metriche precedenti a cinque minuti vengono eliminate definitivamente.

Questi grafici non possono essere aggiunti all'ultimo dashboard di Azure visualizzato in modalità Live.

## <a name="metrics-captured"></a>Metriche acquisite

### <a name="node-cpu-utilization---node-memory-utilization-"></a>% Di utilizzo CPU nodo%/percentuale utilizzo memoria nodo 

Questi due grafici delle prestazioni vengono mappati a un equivalente di richiamare `kubectl top nodes` e acquisire i risultati delle colonne **% CPU** e **memoria%** nel rispettivo grafico. 

![Risultati di esempio di Kubectl Top nodes](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Grafico percentuale utilizzo CPU nodi](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Grafico percentuale utilizzo memoria nodo](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

I calcoli percentile funzioneranno in cluster più grandi per facilitare l'identificazione dei nodi outlier nel cluster. Ad esempio, per comprendere se i nodi sono sottoutilizzati per finalità di riduzione. Utilizzo dell'aggregazione **min** è possibile vedere quali nodi hanno un basso utilizzo nel cluster. Per approfondire la ricerca, selezionare la scheda **nodi** e ordinare la griglia in base all'utilizzo della CPU o della memoria.

Questo consente anche di comprendere quali nodi vengono spostati ai limiti e se è possibile che venga richiesto il ridimensionamento. L'uso di entrambe le aggregazioni **Max** e **P95** può essere utile per verificare se sono presenti nodi nel cluster con un utilizzo elevato delle risorse. Per un'analisi più approfondita, passare nuovamente alla scheda **nodi** .

### <a name="node-count"></a>Numero di nodi

Questo grafico delle prestazioni viene mappato a un equivalente di richiamando `kubectl get nodes` ed eseguendo il mapping della colonna **stato** a un grafico raggruppato per tipi di stato.

![Risultati di esempio di Kubectl Get nodes](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Grafico conteggio nodi](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

I nodi vengono segnalati in uno stato **pronto** o **non pronto** . Vengono conteggiati (viene creato un conteggio totale) e i risultati di queste due aggregazioni sono rappresentati in un grafico.
Ad esempio, per comprendere se i nodi sono in stato di errore. Utilizzando l'aggregazione **non pronta** è possibile visualizzare rapidamente il numero di nodi nel cluster attualmente nello stato **non pronto** .

### <a name="active-pod-count"></a>Numero di Pod attivi

Questo grafico delle prestazioni esegue il mapping a un equivalente di richiamando `kubectl get pods –all-namespaces` ed esegue il mapping della colonna di **stato** del grafico raggruppato in base ai tipi di stato.

![Risultati di esempio di Kubectl Get Pod](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Grafico conteggio Pod nodi](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>Il nome dello stato interpretato da `kubectl` potrebbe non corrispondere esattamente al grafico. 

## <a name="next-steps"></a>Passaggi successivi

Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.