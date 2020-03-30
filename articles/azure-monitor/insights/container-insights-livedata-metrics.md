---
title: Visualizzare le metriche in tempo reale con Monitoraggio di Azure per i contenitori. Documenti Microsoft
description: Questo articolo descrive la visualizzazione in tempo reale delle metriche senza usare kubectl con Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 4604635c985057ec0b7f49a0d1cca7111dfc8eec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79216570"
---
# <a name="how-to-view-metrics-in-real-time"></a>Come visualizzare le metriche in tempo reale

La funzionalità Monitoraggio di Azure per contenitori Live Data (anteprima) consente di visualizzare le metriche sullo stato del nodo e del pod in un cluster in tempo reale. Emula l'accesso `kubectl top nodes`diretto `kubectl get pods –all-namespaces`ai `kubectl get nodes` comandi , e per chiamare, analizzare e visualizzare i dati nei grafici delle prestazioni inclusi in questa Insight. 

In questo articolo viene fornita una panoramica dettagliata e viene illustrato come utilizzare questa funzionalità.  

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzione si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico. 

>[!NOTE]
>Questa funzionalità è disponibile in tutte le aree di Azure, inclusa la Cina di Azure.This feature is available in all Azure regions, including Azure China. Attualmente non è disponibile in Azure US Government.It is currently not available in Azure US Government.

Per assistenza con la configurazione o la risoluzione dei problemi relativi alla funzionalità Live Data (anteprima), consulta la nostra [guida alla configurazione.](container-insights-livedata-setup.md)

## <a name="how-it-works"></a>Funzionamento 

La funzionalità Live Data (anteprima) accede direttamente all'API Kubernetes e ulteriori informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/). 

Questa funzionalità esegue un'operazione di polling `/api/v1/nodes` `/apis/metrics.k8s.io/v1beta1/nodes`sugli `/api/v1/pods`endpoint delle metriche (inclusi , e ), che è ogni cinque secondi per impostazione predefinita. Questi dati vengono memorizzati nella cache nel browser e registrati nei quattro grafici delle prestazioni inclusi in Monitoraggio di Azure per i contenitori nella scheda **Cluster** selezionando **Passa allo stato Live (anteprima).** Ogni sondaggio successivo viene suddiviso in una finestra di visualizzazione di cinque minuti. 

![Opzione Passa allo stato Live nella vista Cluster](./media/container-insights-livedata-metrics/cluster-view-go-live-example-01.png)

L'intervallo di polling viene configurato dall'elenco a discesa **Imposta intervallo** che consente di impostare il polling per i nuovi dati ogni 1, 5, 15 e 30 secondi. 

![Intervallo di polling a discesa Go Live](./media/container-insights-livedata-metrics/cluster-view-polling-interval-dropdown.png)

>[!IMPORTANT]
>È consigliabile impostare l'intervallo di polling su un secondo durante la risoluzione di un problema per un breve periodo di tempo. Queste richieste possono influire sulla disponibilità e la limitazione dell'API Kubernetes nel cluster. Successivamente, riconfigurare a un intervallo di polling più lungo. 

>[!IMPORTANT]
>Nessun dato viene memorizzato in modo permanente durante il funzionamento di questa funzione. Tutte le informazioni acquisite durante questa sessione vengono immediatamente eliminate quando si chiude il browser o si esce dalla funzione. I dati rimangono presenti solo per la visualizzazione all'interno della finestra di cinque minuti; anche tutte le metriche più vecchie di cinque minuti vengono eliminate definitivamente.

Questi grafici non possono essere aggiunti all'ultimo dashboard di Azure visualizzato in modalità live.

## <a name="metrics-captured"></a>Metriche acquisite

### <a name="node-cpu-utilization---node-memory-utilization-"></a>Utilizzo CPU nodo % / % utilizzo memoria nodo 

Questi due grafici delle prestazioni `kubectl top nodes` eseguono il mapping a un equivalente di richiamare e acquisire i risultati delle colonne **CPU%** e **MEMORY%** nel rispettivo grafico. 

![Risultati dell'esempio di esempio dei nodi principali di KubectlKubectl top nodes example results](./media/container-insights-livedata-metrics/kubectl-top-nodes-example.png)

![Tabella degli percentuali di utilizzo della CPU dei nodi](./media/container-insights-livedata-metrics/cluster-view-node-cpu-util.png)

![Grafico percentuale di utilizzo della memoria del nodoNode Memory utilization percent chart](./media/container-insights-livedata-metrics/cluster-view-node-memory-util.png)

I calcoli percentili funzioneranno in cluster più grandi per identificare i nodi outlier nel cluster. Ad esempio, per capire se i nodi sono sottoutilizzati per scopi di scalabilità ridotta. Utilizzando l'aggregazione **Min** è possibile vedere quali nodi hanno un basso utilizzo nel cluster. Per analizzare ulteriormente, selezionare la scheda **Nodi** e ordinare la griglia in base all'utilizzo della CPU o della memoria.

Ciò consente inoltre di comprendere quali nodi vengono inviati ai limiti e se potrebbe essere necessaria la scalabilità orizzontale. L'utilizzo delle aggregazioni **Max** e **P95** consente di verificare se sono presenti nodi nel cluster con un utilizzo elevato delle risorse. Per ulteriori indagini, si passa nuovamente alla scheda **Nodi.**

### <a name="node-count"></a>Conteggio nodi

Questo grafico delle prestazioni viene `kubectl get nodes` mappato a un equivalente di richiamare e mappare la colonna **STATUS** a un grafico raggruppato per tipo di stato.

![Risultati di esempio di Kubectl get nodes](./media/container-insights-livedata-metrics/kubectl-get-nodes-example.png)

![Grafico di conteggio dei nodi](./media/container-insights-livedata-metrics/cluster-view-node-count-01.png)

I nodi vengono segnalati in uno stato **Pronto** o **Non Pronto.** Vengono conteggiati (e viene creato un conteggio totale) e vengono tracciati i risultati di queste due aggregazioni.
Ad esempio, per capire se i nodi rientrano in stati di errore. Utilizzando l'aggregazione **Non pronto** è possibile visualizzare rapidamente il numero di nodi nel cluster attualmente nello stato **Non pronto.**

### <a name="active-pod-count"></a>Conteggio pod attivi

Questo grafico delle prestazioni viene `kubectl get pods –all-namespaces` mappato a un equivalente di richiamare e mappare la colonna **STATUS** del grafico raggruppato per tipo di stato.

![Risultati di esempio di Kubectl get pods](./media/container-insights-livedata-metrics/kubectl-get-pods-example.png)

![Grafico di conteggio del pod dei nodi](./media/container-insights-livedata-metrics/cluster-view-node-pod-count.png)

>[!NOTE]
>I nomi di `kubectl` stato interpretati da potrebbero non corrispondere esattamente nel grafico. 

## <a name="next-steps"></a>Passaggi successivi

Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query ed esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.
