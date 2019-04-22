---
title: Monitorare le prestazioni del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive come è possibile visualizzare e analizzare i dati di log e prestazioni con Monitoraggio di Azure per contenitori.
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
ms.date: 04/09/2019
ms.author: magoedte
ms.openlocfilehash: 3261c2389a9706537366bcd60e00517bbcfb5f48
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59426393"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Conoscere le prestazioni del cluster del servizio Azure Kubernetes con Monitoraggio di Azure per contenitori 
Con Monitoraggio di Azure per i contenitori è possibile usare i grafici delle prestazioni e lo stato di integrità per monitorare il carico di lavoro dei cluster del servizio Azure Kubernetes (AKS) da due prospettive, direttamente da un cluster AKS o visualizzando tutti i cluster AKS in una sottoscrizione da Monitoraggio di Azure. Quando si monitora uno specifico cluster AKS è anche possibile visualizzare Istanze di Azure Container (ACI).

Questo articolo illustra l'esperienza tra le due prospettive e spiega come valutare, analizzare e risolvere rapidamente i problemi rilevati.

Per informazioni sull'abilitazione di Monitoraggio di Azure per contenitori, vedere [Caricare Monitoraggio di Azure per contenitori](container-insights-onboard.md).

Monitoraggio di Azure offre una visualizzazione multicluster che mostra lo stato di integrità di tutti i cluster di AKS monitorati distribuiti tra gruppi di risorse nelle sottoscrizioni.  Mostre inoltre i cluster AKS rilevati non monitorati dalla soluzione. È subito possibile comprendere l'integrità del cluster e, quindi, eseguire il drill down alla pagina delle prestazioni del nodo e del controller oppure è possibile vedere i grafici delle prestazioni per il cluster.  Per i cluster AKS individuati e identificati come non monitorati, si può abilitare il monitoraggio del cluster in qualsiasi momento.  

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Accedere al [portale di Azure](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Visualizzazione multicluster da Monitoraggio di Azure 
Per visualizzare lo stato di integrità di tutti i cluster di servizio Azure Kubernetes distribuiti, selezionare **Monitor** nel riquadro a sinistra nel portale di Azure.  Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.  

![Esempio di dashboard multicluster di Monitoraggio di Azure](./media/container-insights-analyze/azmon-containers-multiview-1018.png)

Dalla scheda **Cluster monitorati** si apprende quanto segue:

1. Quanti cluster si trovano in uno stato critico o non integro e quanti sono integri o non inviano dati (ossia si trovano in uno stato sconosciuto)?
1. Le distribuzioni del [motore del servizio Azure Kubernetes (AKS-engine)](https://github.com/Azure/aks-engine) sono tutte integre?
1. Quanti nodi o pod di sistema e utente sono distribuiti per ogni cluster?  

Gli stati di integrità inclusi sono: 

* **Integro**: nessun problema rilevato per la macchina virtuale che funziona come richiesto. 
* **Critico**: sono stati rilevati uno o più problemi critici che devono essere risolti per ripristinare il normale funzionamento come previsto.
* **Avviso**: sono stati rilevati uno o più problemi che devono essere risolti per evitare che la condizione di integrità diventi critica.
* **Sconosciuto**: se non è stato possibile stabilire una connessione con il nodo o il pod, viene impostato uno stato sconosciuto.
* **Non trovato**: l'area di lavoro, il gruppo di risorse o la sottoscrizione contenente l'area di lavoro per questa soluzione è stato eliminato.
* **Non autorizzato**: l'utente non ha le autorizzazioni necessarie per leggere i dati nell'area di lavoro.
* **Errore**: si è verificato un errore durante il tentativo di lettura dei dati dall'area di lavoro.
* **Configurazione non valida**: Monitoraggio di Azure per i contenitori non è stato configurato correttamente nell'area di lavoro specificata.
* **Dati non disponibili**: dati non inviati all'area di lavoro negli ultimi 30 minuti.

Lo stato di integrità calcola lo stato complessivo del cluster come il *peggiore* dei tre stati con un'unica eccezione: se uno dei tre stati è *sconosciuto*, lo stato complessivo del cluster è indicato come **Sconosciuto**.  

La tabella seguente fornisce i dettagli del calcolo controllando gli stati di integrità per un cluster monitorato nella vista multicluster.

| |Stato |Disponibilità |  
|-------|-------|-----------------|  
|**Pod utente**| | |  
| |Healthy |100% |  
| |Avviso |90 - 99% |  
| |Critico |<90% |  
| |Sconosciuto |Se non è stato segnalato negli ultimi 30 minuti |  
|**Pod sistema**| | |  
| |Healthy |100% |
| |Avviso |N/D |
| |Critico |<100% |
| |Sconosciuto |Se non è stato segnalato negli ultimi 30 minuti |
|**Node** | | |
| |Healthy |>85% |
| |Avviso |60 - 84% |
| |Critico |<60% |
| |Sconosciuto |Se non è stato segnalato negli ultimi 30 minuti |

Dall'elenco dei cluster, è possibile eseguire il drill-down alla pagina **Cluster** facendo clic sul nome del cluster, alla pagina delle prestazioni **Nodi** facendo clic sul rollup dei nodi nella colonna **Nodi** per il cluster specifico o alla pagina delle prestazioni **Controller** facendo clic sul rollup della colonna **Pod utente** o **Pod sistema**.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Visualizzare le prestazioni direttamente da un cluster di servizio Azure Kubernetes
L'accesso a Monitoraggio di Azure per i contenitori è disponibile direttamente da un cluster AKS selezionando **Informazioni dettagliate** dal riquadro a sinistra. La visualizzazione delle informazioni sul cluster servizio Azure Kubernetes è organizzata in base a quattro prospettive:

- HDInsight
- Nodi 
- Controller  
- Contenitori

La pagina predefinita che si apre facendo clic su **Informazioni dettagliate** è **Cluster** e include quattro grafici delle prestazioni a linee che visualizzano le metriche delle prestazioni chiave del cluster. 

![Grafici delle prestazioni di esempio nella scheda Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Nei grafici vengono visualizzate quattro metriche delle prestazioni:

- **% utilizzo CPU nodo&nbsp;%**: una prospettiva aggregata dell'uso della CPU per l'intero cluster. È possibile filtrare i risultati per l'intervallo di tempo selezionando **Avg** (Media), **Min**, **Max**, **50th** (50°), **90th** (90°) e **95th** (95°) tramite il selettore di percentili nella parte superiore del grafico, singolarmente o in combinazione. 
- **% utilizzo memoria nodo&nbsp;%**: una prospettiva aggregata dell'uso della memoria per l'intero cluster. È possibile filtrare i risultati per l'intervallo di tempo selezionando **Avg** (Media), **Min**, **Max**, **50th** (50°), **90th** (90°) e **95th** (95°) tramite il selettore di percentili nella parte superiore del grafico, singolarmente o in combinazione. 
- **Numero di nodi**: il numero e lo stato dei nodi in Kubernetes. Lo stato dei nodi del cluster rappresentati è *All* (Tutto), *Ready* (Pronto) o *Not Ready* (Non pronto) e può essere filtrato singolarmente o in combinazione tramite il selettore nella parte superiore del grafico. 
- **Numero di pod attivi**: il numero e lo stato dei pod in Kubernetes. Lo stato dei pod rappresentati può essere *All* (Tutto), *Pending* (In sospeso), *Running* (In esecuzione) o *Unknown* (Sconosciuto) e può essere filtrato singolarmente o in combinazione tramite il selettore nella parte superiore del grafico. 

È possibile usare i tasti di direzione freccia SINISTRA/DESTRA per scorrere ogni punto dati nel grafico e i tasti freccia SU/GIÙ per scorrere le linee di percentile.

Monitoraggio di Azure per contenitori supporta anche Azure Monitor [Esplora metriche](../platform/metrics-getting-started.md), dove è possibile creare il proprio tracciato di grafici, correlare e analizzare le tendenze e Aggiungi al dashboard. Da Esplora metriche, è anche possibile usare i criteri sono impostati per visualizzare le metriche come base per un [regola di avviso basata su metrica](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Visualizzare le metriche di contenitore in Esplora metriche
In Esplora metriche, è possibile visualizzare il nodo aggregato e pod metriche di utilizzo da monitoraggio di Azure per contenitori. La tabella seguente riepiloga i dettagli che consentono di comprendere come usare i grafici delle metriche per visualizzare metriche del contenitore.

|Spazio dei nomi | Metrica |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | PodCount |

È possibile applicare [suddivisione](../platform/metrics-charts.md#apply-splitting-to-a-chart) di una metrica per visualizzarlo in base alla dimensione e visualizzare come i vari segmenti del confronto tra loro. Per un nodo, è possibile segmentare il grafico per la *host* dimensione, e da un pod è possibile segmentare, in base alle dimensioni seguenti:

* Controller
* Spazio dei nomi Kubernetes
* Nodo
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizzare i nodi, i controller e dell'integrità dei contenitori

Quando si passa alle schede **Nodi**, **Controller** e **Contenitori**, sul lato destro della pagina viene visualizzato automaticamente il riquadro delle proprietà.  Mostra le proprietà di cui l'elemento-selezionata, incluse le etichette si definisce per organizzare gli oggetti Kubernetes. Fare clic sul collegamento **>>** nel riquadro per visualizzare o nascondere il riquadro.  

![Riquadro delle proprietà di esempio delle prospettive Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

Quando si espandono gli oggetti nella gerarchia, il riquadro delle proprietà viene aggiornato in base all'oggetto selezionato. Dal riquadro è anche possibile visualizzare gli eventi Kubernetes con ricerche nei log predefinite facendo clic sul collegamento **Visualizza log eventi di Kubernetes** nella parte superiore del riquadro. Per altre informazioni sulla visualizzazione dei dati dei log di Kubernetes, vedere [Eseguire ricerche nei log per analizzare i dati](#search-logs-to-analyze-data). Mentre si esaminano i contenitori nella visualizzazione **Contenitori**, è possibile vedere i log dei contenitori in tempo reale. Per altre informazioni su questa funzionalità e sulla configurazione necessaria per concedere e controllare l'accesso, vedere [Come visualizzare i log dei contenitori in tempo reale con Monitoraggio di Azure per i contenitori (Anteprima)](container-insights-live-logs.md). 

Usare l'opzione **+ Aggiungi filtro** nella parte superiore della pagina per filtrare i risultati per la visualizzazione in base a **Servizio**, **Nodo** o **Spazio dei nomi** e, dopo aver selezionato l'ambito, selezionare uno dei valori indicati nel campo **Seleziona valore/i**.  Dopo la configurazione, il filtro viene applicato a livello globale durante la visualizzazione di qualsiasi prospettiva del cluster AKS.  La formula supporta solo il segno di uguale.  È possibile aggiungere altri filtri sopra il primo per limitare ulteriormente i risultati.  Ad esempio, se si specificato un filtro in base a **Nodo**, il secondo filtro consente di selezionare solo **Servizio** oppure **Spazio dei nomi**.  

![Esempio di utilizzo del filtro per limitare i risultati](./media/container-insights-analyze/add-filter-option-01.png)

Se si specifica un filtro in una scheda esso continua a essere applicato quando se ne seleziona un altro e viene eliminato dopo aver fatto clic sul simbolo **x** accanto al filtro specificato.   

Passando alla scheda **Nodi**, la gerarchia delle righe seguirà il modello a oggetti Kubernetes, a partire da un nodo nel cluster. Espandere il nodo per visualizzare uno o più pod in esecuzione sul nodo. Se in un pod sono raggruppati più contenitori, questi vengono visualizzati come ultima riga nella gerarchia. È possibile visualizzare anche il numero di carichi di lavoro non correlati a pod in esecuzione sull'host nel caso di uso intensivo del processore o della memoria sull'host.

![Gerarchia di nodi Kubernetes di esempio nella vista prestazioni](./media/container-insights-analyze/containers-nodes-view.png)

I nodi virtuali di Istanze di Azure Container che eseguono il sistema operativo Linux vengono mostrati dopo l'ultimo nodo del cluster AKS nell'elenco.  Quando si espande un nodo virtuale ACI, è possibile visualizzare uno o più POD e contenitori ACI in esecuzione nel nodo.  Le metriche non vengono raccolte e inviate per i nodi, solo i per i pod.

![Gerarchia di nodi di esempio con Istanze di Container elencate](./media/container-insights-analyze/nodes-view-aci.png)

Da un nodo espanso è possibile eseguire il drill-down dal pod o contenitore in esecuzione nel nodo al controller per visualizzare i dati sulle prestazioni filtrati per il controller specifico. Fare clic sul valore nella colonna **Controller** per il nodo specifico.   
![Esempio di drill-down dal nodo al controller nella vista prestazioni](./media/container-insights-analyze/drill-down-node-controller.png)

È possibile selezionare controller o contenitori nella parte superiore della pagina e controllare lo stato e l'uso delle risorse per tali oggetti.  Se invece si vuole controllare l'uso della memoria, nell'elenco a discesa **Metrica** selezionare **Memoria RSS** o **Working set della memoria**. L'opzione **Memoria RSS** è supportata solo per Kubernetes versione 1.8 e successive. Se si usa un'altra versione, per i valori di **Min&nbsp;%** verrà visualizzato *NaN&nbsp;%*, ovvero un valore di tipo di dati numerico che indica un valore non definito o non rappresentabile. 

![Visualizzazione delle prestazioni dei nodi del contenitore](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Per impostazione predefinita, i dati sulle prestazioni sono basati sulle ultime sei ore, ma è possibile modificare l'intervallo tramite l'opzione **Intervallo di tempo** nell'angolo superiore sinistro. È anche possibile filtrare i risultati compresi nell'intervallo di tempo selezionando **Avg** (Media), **Min**, **Max**, **50th** (50°), **90th** (90°) e **95th** (95°) tramite il selettore di percentile. 

![Selezione del percentile per filtrare i dati](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando si posiziona il puntatore del mouse sopra il grafico a barre sotto la colonna **Tendenza**, ogni barra indica l'utilizzo di CPU o memoria, a seconda della metrica selezionata, all'interno di un periodo di campionamento di 15 minuti. Dopo aver selezionato il grafico di tendenza tramite una tastiera, è possibile usare i tasti ALT+PGSU o ALT+PGGIÙ per scorrere ogni barra singolarmente e ottenere gli stessi dettagli visualizzati al passaggio del mouse.

![Tendenza di grafico a barre al passaggio del mouse su esempio](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

Nell'esempio seguente è possibile osservare che per il primo nodo dell'elenco, *aks-nodepool1-*, il valore per **Contenitori** è 9, ovvero un rollup del numero totale di contenitori distribuiti.

![Esempio di rollup dei contenitori per ogni nodo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

In questo modo, è possibile determinare rapidamente se i contenitori sono ripartiti in modo equilibrato tra i nodi del cluster. 

Le informazioni presentate quando si visualizzano i nodi sono descritte nella tabella seguente:

| Colonna | DESCRIZIONE | 
|--------|-------------|
| NOME | Nome dell'host. |
| Stato | Visualizzazione Kubernetes dello stato del nodo. |
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (% media, % min, % max, % 50°, % 90°) | Percentuale media dei nodi in base al percentile durante l'intervallo di tempo selezionato. |
| Avg, Min, Max, 50th, 90th (Media, Min, Max, 50°, 90°) | Valore effettivo dei nodi medio durante l'intervallo di tempo selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un nodo. Per i pod e i contenitori, è il valore medio segnalato dall'host. |
| Contenitori | Numero di contenitori. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un nodo. |
| Controller | Solo per i contenitori e i pod. Mostra il controller in cui è presente. Non tutti i pod sono presenti in un controller. È quindi possibile che per alcuni sia visualizzato **N/A** per indicare che non sono disponibili. | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (Tendenza % media, % min, % max, % 50°, % 90°) | Grafico a barre della tendenza che presenta il valore percentuale medio delle metriche di percentile del controller. |

Nel selettore selezionare **Controller**.

![Selezione della visualizzazione per i controller](./media/container-insights-analyze/containers-controllers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei controller e dei controller di nodi virtuali ACI o dei pod di nodi virtuali non connessi a un controller.

![Vista prestazioni dei controller di <nome>](./media/container-insights-analyze/containers-controllers-view.png)

La gerarchia di righe inizia con un controller e quando si espande un controller vengono visualizzati uno o più pod.  Espandendo il pod, nell'ultima riga viene mostrato il contenitore raggruppato nel pod. Da un controller espanso è possibile eseguire il drill-down nel nodo in cui viene eseguito per visualizzare i dati sulle prestazioni filtrati per il nodo. I pod ACI non connessi a un controller vengono presentati per ultimi nell'elenco.

![Gerarchia di controller di esempio con pod di Istanze di Container elencati](./media/container-insights-analyze/controllers-view-aci.png)

Fare clic sul valore nella colonna **Nodo** per il controller specifico.   

![Esempio di drill-down dal nodo al controller nella vista prestazioni](./media/container-insights-analyze/drill-down-controller-node.png)

Le informazioni presentate quando si visualizzano i controller sono descritte nella tabella seguente:

| Colonna | DESCRIZIONE | 
|--------|-------------|
| NOME | Nome del controller.|
| Stato | Stato di rollup quando l'esecuzione è stata completata con uno stato, ad esempio *OK*, *Terminated* (Terminato), *Failed* (Non riuscito), *Stopped* (Arrestato) o *Paused* (In pausa). Se il contenitore è in esecuzione, ma lo stato non è stato presentato correttamente o non è stato rilevato dall'agente e non è stata inviata alcuna risposta per più di 30 minuti, lo stato è *Unknown* (Sconosciuto). La tabella seguente contiene dettagli aggiuntivi sull'icona dello stato.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (% media, % min, % max, % 50°, % 90°) | Rollup della media della percentuale media di ogni entità per la metrica selezionata e percentile. |
| Avg, Min, Max, 50th, 90th (Media, Min, Max, 50°, 90°)  | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Contenitori | Numero totale di contenitori per il controller o il pod. |
| Riavvii | Rollup del numero di riavvii dai contenitori. |
| Uptime | Rappresenta il tempo dall'avvio di un contenitore. |
| Nodo | Solo per i contenitori e i pod. Mostra il controller in cui è presente. | 
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (Tendenza % media, % min, % max, % 50°, % 90°)| Grafico a barre della tendenza che presenta il valore percentile medio del controller. |

Le icone nel campo relativo allo stato indicano lo stato online dei contenitori:
 
| Icona | Stato | 
|--------|-------------|
| ![Icona di stato In esecuzione (Pronto)](./media/container-insights-analyze/containers-ready-icon.png) | In esecuzione (Pronto)|
| ![Icona di stato In attesa o In pausa](./media/container-insights-analyze/containers-waiting-icon.png) | In attesa o In pausa|
| ![Icona di stato Ultima esecuzione segnalata](./media/container-insights-analyze/containers-grey-icon.png) | Ultima esecuzione segnalata ma senza risposta da oltre 30 minuti|
| ![Icona di stato Operazione riuscita](./media/container-insights-analyze/containers-green-icon.png) | Arresto completato o arresto non riuscito|

L'icona dello stato mostra un numero in base ai dati forniti dal pod. Vengono visualizzati i due stati peggiori. Quando si passa il puntatore del mouse sullo stato, viene visualizzato uno stato di rollup di tutti i pod nel contenitore. Se non è presente alcuno stato Pronto, come stato viene visualizzato **(0)**. 

Nel selettore selezionare **Contenitori**.

![Selezione della visualizzazione per i contenitori](./media/container-insights-analyze/containers-containers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei contenitori di Istanze di Azure Container e del servizio Azure Kubernetes.  

![Vista prestazioni dei controller di <nome>](./media/container-insights-analyze/containers-containers-view.png)

Da un contenitore è possibile eseguire il drill-down in un pod o nodo per visualizzare i dati sulle prestazioni filtrati per l'oggetto. Fare clic sul valore nella colonna **Pod** o **Nodo** per il controller specifico.   

![Esempio di drill-down dal nodo al controller nella vista prestazioni](./media/container-insights-analyze/drill-down-controller-node.png)

Le informazioni presentate quando si visualizzano i contenitori sono descritte nella tabella seguente:

| Colonna | DESCRIZIONE | 
|--------|-------------|
| NOME | Nome del controller.|
| Stato | Stato dei contenitori, se presente. La tabella seguente contiene dettagli aggiuntivi sull'icona dello stato.|
| Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (% media, % min, % max, % 50°, % 90°) | Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Avg, Min, Max, 50th, 90th (Media, Min, Max, 50°, 90°)  | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Pod | Contenitore in cui è presente il pod.| 
| Nodo |  Nodo in cui è presente il contenitore. | 
| Riavvii | Rappresenta il tempo dall'avvio di un contenitore. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un contenitore. |
| Trend Avg&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50th&nbsp;%, 90th&nbsp;% (Tendenza % media, % min, % max, % 50°, % 90°) | Grafico a barre della tendenza che presenta il valore percentuale medio delle metriche di percentile del contenitore. |

Le icone nel campo relativo allo stato indicano lo stato online dei pod, come descritto nella tabella seguente:
 
| Icona | Stato |  
|--------|-------------|  
| ![Icona di stato In esecuzione (Pronto)](./media/container-insights-analyze/containers-ready-icon.png) | In esecuzione (Pronto)|  
| ![Icona di stato In attesa o In pausa](./media/container-insights-analyze/containers-waiting-icon.png) | In attesa o In pausa|  
| ![Icona di stato Ultima esecuzione segnalata](./media/container-insights-analyze/containers-grey-icon.png) | Ultima esecuzione segnalata ma senza risposta da oltre 30 minuti|  
| ![Icona di stato Terminato](./media/container-insights-analyze/containers-terminated-icon.png) | Arresto completato o arresto non riuscito|  
| ![Icona di stato Non riuscito](./media/container-insights-analyze/containers-failed-icon.png) | Stato Non riuscito |  


## <a name="container-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati dei contenitori
Informazioni dettagliate dei contenitori raccoglie le varie metriche delle prestazioni e i vari dati di log da host del contenitore e contenitori. I dati vengono raccolti ogni tre minuti.

### <a name="container-records"></a>Record dei contenitori

La tabella seguente mostra esempi di record raccolti da Monitoraggio di Azure per contenitori e i tipi di dati visualizzati nei risultati della ricerca nei log:

| Tipo di dati | Tipo di dati in Ricerca log | Campi |
| --- | --- | --- |
| Prestazioni per host e contenitori | `Perf` | Computer, ObjectName, CounterName &#40;% tempo processore, MB di letture disco, MB di scritture disco MB utilizzo di memoria, byte di ricezione di rete, byte di invio di rete, utilizzo del processore in secondi, rete&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Inventario contenitori | `ContainerInventory` | TimeGenerated, Computer, container name, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Inventario delle immagini dei contenitori | `ContainerImageInventory` | TimeGenerated, Computer, Image, ImageTag, ImageSize, VirtualSize, Running, Paused, Stopped, Failed, SourceSystem, ImageID, TotalContainer |
| Log contenitori | `ContainerLog` | TimeGenerated, Computer, image ID, container name, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Log servizio contenitori | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Inventario di nodi contenitore | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Processo contenitore | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Inventario dei pod in un cluster Kubernetes | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Inventario dei nodi di un cluster Kubernetes | `KubeNodeInventory` | TimeGenerated, Computer, ClusterName, ClusterId, LastTransitionTimeReady, Labels, Status, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Eventi di Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Message,  SourceSystem | 
| Servizi nel cluster Kubernetes | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| Metriche delle prestazioni per la parte dei nodi del cluster Kubernetes | Perf &#124; where ObjectName == “K8SNode” | Computer, ObjectName, CounterName &#40;cpuUsageNanoCores, , memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Metriche delle prestazioni per la parte dei contenitori del cluster Kubernetes | Perf &#124; where ObjectName == “K8SContainer” | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;,CounterValue, TimeGenerated, CounterPath, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Eseguire ricerche nei log per analizzare i dati
Log Analytics consente di individuare tendenze, diagnosticare i colli di bottiglia, effettuare previsioni o correlare dati che consentono di determinare se la configurazione corrente del cluster è ottimale. Sono disponibili ricerche predefinite nei log che è possibile iniziare a usare immediatamente o personalizzare per restituire le informazioni nel modo che si preferisce. 

È possibile eseguire un'analisi interattiva dei dati nell'area di lavoro selezionando l'opzione **View Kubernetes event logs** (Visualizza registri eventi Kubernetes) o **View container logs** (Visualizza log contenitore) nel riquadro di anteprima. La pagina **Ricerca log** viene visualizzata sulla destra della pagina attiva del portale di Azure.

![Analizzare i dati in Log Analytics](./media/container-insights-analyze/container-health-log-search-example.png)   

L'output dei log dei contenitori inoltrato a Log Analytics è costituito da STDOUT e STDERR. Poiché Monitoraggio di Azure monitora il servizio Kubernetes gestito da Azure, i dati di Kube-system non vengono attualmente raccolti a causa della grande quantità di dati generati. 

### <a name="example-log-search-queries"></a>Esempio di query di ricerca log
Spesso è utile creare una query a partire da qualche esempio e quindi modificarla in base ai propri requisiti. Per creare query più avanzate, è possibile provare a usare le query di esempio seguenti:

| Query | DESCRIZIONE | 
|-------|-------------|
| ContainerInventory<br> &#124; project Computer, Name, Image, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124; render table | Elencare tutte le informazioni sul ciclo di vita di un contenitore| 
| KubeEvents_CL<br> &#124; where not(isempty(Namespace_s))<br> &#124; sort by TimeGenerated desc<br> &#124; render table | Eventi di Kubernetes|
| ContainerImageInventory<br> &#124; summarize AggregatedValue = count() by Image, ImageTag, Running | Inventario delle immagini | 
| **Selezionare l'opzione di visualizzazione corrispondente al grafico a linee**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "cpuUsageNanoCores" &#124; summarize AvgCPUUsageNanoCores = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | CPU del contenitore | 
| **Selezionare l'opzione di visualizzazione corrispondente al grafico a linee**:<br> Perf<br> &#124; where ObjectName == "K8SContainer" and CounterName == "memoryRssBytes" &#124; summarize AvgUsedRssMemoryBytes = avg(CounterValue) by bin(TimeGenerated, 30m), InstanceName | Memoria del contenitore |

## <a name="next-steps"></a>Passaggi successivi
Monitoraggio di Azure per i contenitori non include un set predefinito di avvisi che è possibile copiare e modificare in base ai processi e alle procedure di supporto. Rivedere [Creare avvisi sulle prestazioni con Monitoraggio di Azure per i contenitori](container-insights-alerts.md) per imparare a creare gli avvisi consigliati per un utilizzo elevato di CPU e memoria.  
