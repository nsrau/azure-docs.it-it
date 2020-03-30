---
title: Monitoraggio di Kubernetes con Monitor di Azure per i contenitori . Documenti Microsoft
description: Questo articolo descrive come visualizzare e analizzare le prestazioni di un cluster Kubernetes con Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 227fe70512536790d179797394b6fba22e7eb50e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298382"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitorare le prestazioni del cluster Kubernetes con Monitoraggio di Azure per i contenitoriMonitor your Kubernetes cluster performance with Azure Monitor for containers

Con Monitor di Azure per i contenitori, è possibile usare i grafici delle prestazioni e lo stato di integrità per monitorare il carico di lavoro dei cluster Kubernetes ospitati nel servizio Azure Kubernetes (AKS), Azure Stack o altro ambiente da due prospettive. You can monitor directly from the cluster, or you can view all clusters in a subscription from Azure Monitor. La visualizzazione delle istanze del contenitore di Azure è possibile anche quando si monitora un cluster AKS specifico.

Questo articolo consente di comprendere le due prospettive e il modo in cui Monitoraggio di Azure consente di valutare, analizzare e risolvere rapidamente i problemi rilevati.

Per informazioni su come abilitare Monitoraggio di Azure per i contenitori, vedere [Onboard Azure Monitor per i contenitori.](container-insights-onboard.md)

Monitoraggio di Azure offre una visualizzazione multi-cluster che mostra lo stato di integrità di tutti i cluster Kubernetes monitorati che eseguono Linux e Windows Server 2019 distribuiti tra i gruppi di risorse nelle sottoscrizioni. Mostra i cluster individuati in tutti gli ambienti che non vengono monitorati dalla soluzione. È possibile comprendere immediatamente l'integrità del cluster e, da qui, è possibile eseguire il drill-down alla pagina delle prestazioni del nodo e del controller o passare ai grafici delle prestazioni per il cluster. Per i cluster AKS individuati e identificati come non monitorati, è possibile abilitare il monitoraggio per tali cluster in qualsiasi momento. 

Le principali differenze nel monitoraggio di un cluster di Windows Server con Monitoraggio di Azure per i contenitori rispetto a un cluster Linux sono descritte [di seguito](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) nell'articolo Panoramica.The main differences in monitoring a Windows Server cluster with Azure Monitor for containers compared to a Linux cluster are described here in the overview article.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com)di Azure . 

## <a name="multi-cluster-view-from-azure-monitor"></a>Visualizzazione multicluster da Monitoraggio di Azure

Per visualizzare lo stato di integrità di tutti i cluster Kubernetes distribuiti, selezionare Monitoraggio nel riquadro sinistro del portale di Azure.To view the health status of all Kubernetes clusters deployed, select **Monitor** from the left pane in the Azure portal. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**. 

![Esempio di dashboard multicluster di Monitoraggio di Azure](./media/container-insights-analyze/azmon-containers-multiview.png)

È possibile definire l'ambito dei risultati presentati nella griglia per visualizzare i cluster:You can scope the results presented in the grid to show clusters that are:

* **Azure** - Cluster AKS e AKS-Engine ospitati nel servizio Azure KubernetesAzure - AKS and AKS-Engine clusters hosted in Azure Kubernetes Service
* **Azure Stack (anteprima)-** Cluster AKS-Engine ospitati in Azure StackAzure Stack (Preview) - AKS-Engine clusters hosted on Azure Stack
* **Non-Azure (anteprima)-** Cluster Kubernetes ospitati in locale
* **Tutti:** visualizzare tutti i cluster Kubernetes ospitati in ambienti Azure, Azure Stack e locali che sono stati riconnessi a Monitoraggio di Azure per i contenitori

Per visualizzare i cluster da un ambiente specifico, selezionarlo dalla pillola **Ambienti** nell'angolo superiore sinistro della pagina.

![Esempio di selettore di pillola dell'ambiente](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Nella scheda **Cluster monitorati** è possibile apprendere quanto segue:

- Quanti cluster sono in uno stato critico o non integro, rispetto al numero di cluster integri o meno segnalati (definito stato sconosciuto).
- Se tutte le distribuzioni di [Azure Kubernetes Engine (motore AKS)](https://github.com/Azure/aks-engine) sono integre.
- Numero di nodi e pod utente e sistema distribuiti per cluster.
- Quantità di spazio su disco disponibile e se si verifica un problema di capacità.

Gli stati di integrità inclusi sono: 

* **Integro:** non vengono rilevati problemi per la macchina virtuale e funziona in base alle esigenze. 
* **Critico**: Vengono rilevati uno o più problemi critici che devono essere risolti per ripristinare il normale stato operativo come previsto.
* **Avviso:** vengono rilevati uno o più problemi che devono essere risolti o la condizione di integrità potrebbe diventare critica.
* **Sconosciuto:** se il servizio non è stato in grado di effettuare una connessione con il nodo o il pod, lo stato diventa Sconosciuto.
* **Non trovato:** l'area di lavoro, il gruppo di risorse o la sottoscrizione che contiene l'area di lavoro per questa soluzione è stata eliminata.
* **Non autorizzato:** l'utente non dispone delle autorizzazioni necessarie per leggere i dati nell'area di lavoro.
* **Errore:** si è verificato un errore durante il tentativo di leggere i dati dall'area di lavoro.
* **Non configurato correttamente:** Monitoraggio di Azure per i contenitori non è stato configurato correttamente nell'area di lavoro specificata.
* **Nessun dato**: I dati non sono stati segnalati nell'area di lavoro negli ultimi 30 minuti.

Lo stato di integrità calcola lo stato complessivo del cluster come il *peggiore dei* tre stati con un'eccezione. Se uno dei tre stati è Sconosciuto, lo stato complessivo del cluster è **Sconosciuto**. 

Nella tabella seguente viene fornita una suddivisione del calcolo che controlla gli stati di integrità per un cluster monitorato nella visualizzazione a più cluster.

| |Stato |Disponibilità |  
|-------|-------|-----------------|  
|**Pod utente**| | |  
| |Healthy |100% |  
| |Avviso |90 - 99% |  
| |Critico |<90% |  
| |Unknown |Se non è stato segnalato negli ultimi 30 minuti |  
|**Pod di sistema**| | |  
| |Healthy |100% |
| |Avviso |N/D |
| |Critico |<100% |
| |Unknown |Se non è stato segnalato negli ultimi 30 minuti |
|**Nodo** | | |
| |Healthy |>85% |
| |Avviso |60 - 84% |
| |Critico |<60% |
| |Unknown |Se non è stato segnalato negli ultimi 30 minuti |

Dall'elenco dei cluster, è possibile eseguire il drill-down alla pagina **Cluster** selezionando il nome del cluster. Passare quindi alla pagina delle prestazioni **Nodi** selezionando il rollup dei nodi nella colonna **Nodi** per il cluster specifico. In alternativa, potete eseguire il drill-down fino alla pagina delle prestazioni **Controller** selezionando il rollup della colonna **Pod utente** o Pod di **sistema.**

## <a name="view-performance-directly-from-a-cluster"></a>Visualizzare le prestazioni direttamente da un clusterView performance directly from a cluster

L'accesso a Monitoraggio di Azure per i contenitori è disponibile direttamente da un cluster AKS selezionando**Cluster** **di informazioni dettagliate** > nel riquadro sinistro o quando è stato selezionato un cluster dalla visualizzazione multi-cluster. Le informazioni sul cluster sono organizzate in quattro prospettive:

- Cluster
- Nodi 
- Controller 
- Contenitori

>[!NOTE]
>L'esperienza descritta nella parte restante di questo articolo è applicabile anche per la visualizzazione dello stato di prestazioni e integrità dei cluster Kubernetes ospitati in Azure Stack o in un altro ambiente quando viene selezionato dalla visualizzazione multi-cluster. 

Verrà aperta la pagina predefinita e vengono visualizzati quattro grafici delle prestazioni delle linee che mostrano le metriche chiave delle prestazioni del cluster. 

![Grafici delle prestazioni di esempio nella scheda Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

I grafici delle prestazioni mostrano quattro metriche delle prestazioni:The performance charts display four performance metrics:

- **Utilizzo&nbsp;CPU nodo**: una prospettiva aggregata dell'utilizzo della CPU per l'intero cluster. Per filtrare i risultati per l'intervallo di tempo, selezionare **Avg**, **Min**, **50th**, **90th**, **95th**o **Max** nel selettore percentili sopra il grafico. I filtri possono essere utilizzati singolarmente o combinati. 
- **Utilizzo&nbsp;della memoria dei nodi:** una prospettiva aggregata dell'utilizzo della memoria per l'intero cluster. Per filtrare i risultati per l'intervallo di tempo, selezionare **Avg**, **Min**, **50th**, **90th**, **95th**o **Max** nel selettore percentili sopra il grafico. I filtri possono essere utilizzati singolarmente o combinati. 
- **Node count** (Numero di nodi): il numero e lo stato dei nodi in Kubernetes. Gli stati dei nodi del cluster rappresentati sono Totale, Pronto e Non pronto. Possono essere filtrati singolarmente o combinati nel selettore sopra il grafico. 
- **Conteggio pod attivo**: Un numero di pod e lo stato di Kubernetes. Gli stati dei pod rappresentati sono Totale, In sospeso, In esecuzione, Sconosciuto, Completato o Non riuscito. Possono essere filtrati singolarmente o combinati nel selettore sopra il grafico. 

Utilizzare i tasti freccia sinistra e destra per scorrere ogni punto dati del grafico. Utilizzare i tasti freccia Su e Giù per scorrere le linee percentili. Selezionare l'icona a forma di puntina nell'angolo superiore destro di uno dei grafici per aggiungere il grafico selezionato all'ultimo dashboard di Azure visualizzato. Dal dashboard, è possibile ridimensionare e riposizionare il grafico. Selezionando il grafico dal dashboard si reindirizza ad Azure Monitor per i contenitori e viene caricata l'ambito e la visualizzazione corretti.

Monitoraggio di Azure per i contenitori supporta anche Esplora [metriche](../platform/metrics-getting-started.md)di Monitoraggio di Azure, in cui è possibile creare grafici di stampa personalizzati, correlare e analizzare le tendenze e aggiungere ai dashboard. Da Esplora metriche è inoltre possibile utilizzare i criteri impostati per visualizzare le metriche come base di una regola di [avviso basata su metriche.](../platform/alerts-metric.md) 

## <a name="view-container-metrics-in-metrics-explorer"></a>Visualizzare le metriche del contenitore in Esplora metricheView container metrics in metrics explorer

In Esplora metriche è possibile visualizzare le metriche aggregate di nodo e utilizzo dei pod da Monitoraggio di Azure per i contenitori. Nella tabella seguente sono riepilogati i dettagli per comprendere come usare i grafici delle metriche per visualizzare le metriche dei contenitori.

|Spazio dei nomi | Metrica | Descrizione | 
|----------|--------|-------------|
| insights.container/nodes | |
| | cpuUsageMillicores | Misurazione aggregata dell'utilizzo della CPU nel cluster. Si tratta di un nucleo della CPU diviso in 1000 unità (milli - 1000). Utilizzato per determinare l'utilizzo dei core in un contenitore in cui molte applicazioni potrebbero usare un core.| 
| | percentuale di utilizzo di cpu | Utilizzo medio aggregato della CPU misurato in percentuale in tutto il cluster.|
| | memoryRsBytes | Memoria RSS contenitore utilizzata in byte.| 
| | memoryRssPercentage (percentuale) | Memoria RSS contenitore utilizzata in percentuale.|
| | memoryWorkingSetBytes | Memoria del working set del contenitore utilizzata.| 
| | memoryWorkingSetPercentage (memoriaWorkingSetPercentage) | Memoria del working set contenitore utilizzata in percentuale. | 
| | nodesCount | Un conteggio di nodi da Kubernetes.|
| insights.container/pods | |
| | Conteggio Pod | Un numero di baccelli di Kubernetes.|

È possibile [dividere](../platform/metrics-charts.md#apply-splitting-to-a-chart) una metrica per visualizzarla per dimensione e visualizzare il confronto tra i diversi segmenti. Per un nodo, è possibile segmentare il grafico in base alla dimensione *host.* Da un contenitore, potete selarvi in base alle seguenti dimensioni:

* Controller
* Spazio dei nomi Kubernetes
* Nodo
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizzare nodi, controller e integrità dei contenitoriAnalyze nodes, controllers, and container health

Quando si passa alle schede **Nodi**, **Controller**e **Contenitori** , viene visualizzato automaticamente un riquadro delle proprietà sul lato destro della pagina. Mostra le proprietà dell'elemento selezionato, che include le etichette definite per organizzare gli oggetti Kubernetes. Quando viene selezionato un nodo Linux, la sezione **Capacità disco locale** mostra anche lo spazio disponibile su disco e la percentuale utilizzata per ogni disco presentato al nodo. Selezionare **>>** il collegamento nel riquadro per visualizzare o nascondere il riquadro.

Quando si espandono gli oggetti nella gerarchia, il riquadro delle proprietà viene aggiornato in base all'oggetto selezionato. Dal riquadro, è anche possibile visualizzare i log del contenitore Kubernetes (stdout/stderror), gli eventi e le metriche dei pod selezionando il collegamento **Visualizza dati dinamici (anteprima)** nella parte superiore del riquadro. Per ulteriori informazioni sulla configurazione necessaria per concedere e controllare l'accesso per visualizzare questi dati, consultate [Impostare i Dati dinamici (anteprima)](container-insights-livedata-setup.md). Mentre si esaminano le risorse del cluster, è possibile visualizzare questi dati dal contenitore in tempo reale. Per ulteriori informazioni su questa funzionalità, consultate Come visualizzare i log, gli [eventi e le metriche dei pod di Kubernetes in tempo reale.](container-insights-livedata-overview.md) Per visualizzare i dati di log DiKubernetes archiviati nell'area di lavoro in base a ricerche di log predefinite, selezionare **Visualizza log contenitore** dall'elenco a discesa **Visualizza nell'analisi.** Per ulteriori informazioni su questo argomento, vedere [Log di ricerca per analizzare i dati](container-insights-log-search.md#search-logs-to-analyze-data).

Utilizzare l'opzione **Aggiungi filtro** nella parte superiore della pagina per filtrare i risultati per la visualizzazione in base a **Servizio**, **Nodo**, Spazio **dei nomi**o Pool **di nodi**. Dopo aver selezionato l'ambito del filtro, selezionare uno dei valori visualizzati nel campo **Seleziona valori.** Dopo aver configurato, il filtro viene applicato globalmente durante la visualizzazione di qualsiasi prospettiva del cluster AKS. La formula supporta solo il segno di uguale. È possibile aggiungere altri filtri sopra il primo per limitare ulteriormente i risultati. Ad esempio, se si specifica un filtro per **Nodo**, è possibile selezionare solo **Servizio** o Spazio **dei nomi** per il secondo filtro.

La specifica di un filtro in una scheda continua a essere applicata quando si seleziona un altro. Viene eliminato dopo aver selezionato il simbolo **x** accanto al filtro specificato. 

Passare alla scheda **Nodi** e la gerarchia di righe segue il modello a oggetti Kubernetes, che inizia con un nodo nel cluster. Espandere il nodo per visualizzare uno o più pod in esecuzione sul nodo. Se più di un contenitore è raggruppato in un contenitore, vengono visualizzati come ultima riga nella gerarchia. È inoltre possibile visualizzare il numero di carichi di lavoro non correlati ai pod in esecuzione nell'host se l'host presenta un utilizzo eccessivo del processore o della memoria.

![Esempio della gerarchia dei nodi di Kubernetes nella visualizzazione delle prestazioni](./media/container-insights-analyze/containers-nodes-view.png)

Contenitori di Windows Server che eseguono il sistema operativo Windows Server 2019 vengono visualizzati dopo tutti i nodi basati su Linux nell'elenco. Quando si espande un nodo di Windows Server, è possibile visualizzare uno o più pod e contenitori in esecuzione sul nodo. Dopo aver selezionato un nodo, nel riquadro delle proprietà vengono visualizzate le informazioni sulla versione. Le informazioni sull'agente vengono escluse perché nei nodi di Windows Server non è installato un agente. 

![Esempio di gerarchia dei nodi con i nodi di Windows Server elencati](./media/container-insights-analyze/nodes-view-windows.png) 

I nodi virtuali delle istanze del contenitore di Azure che eseguono il sistema operativo Linux vengono visualizzati dopo l'ultimo nodo del cluster AKS nell'elenco. Quando si espande un nodo virtuale Istanze contenitore, è possibile visualizzare uno o più contenitori Istanze contenitore e contenitori eseguiti sul nodo. Le metriche non vengono raccolte e segnalate per i nodi, ma solo per i pod.

![Gerarchia di nodi di esempio con Istanze di Container elencate](./media/container-insights-analyze/nodes-view-aci.png)

Da un nodo espanso, è possibile eseguire il drill-down dal pod o dal contenitore in esecuzione sul nodo al controller per visualizzare i dati sulle prestazioni filtrati per tale controller. Selezionare il valore nella colonna **Controller** per il nodo specifico.
 
![Esempio di drill-down dal nodo al controller nella vista prestazioni](./media/container-insights-analyze/drill-down-node-controller.png)

Selezionare i controller o i contenitori nella parte superiore della pagina per esaminare lo stato e l'utilizzo delle risorse per tali oggetti. Per esaminare l'utilizzo della memoria, nell'elenco a discesa **Metrica** selezionare **Memoria RSS** o **Working Set memoria**. L'opzione **Memoria RSS** è supportata solo per Kubernetes versione 1.8 e successive. In caso contrario, i valori di **Min&nbsp; ** vengono visualizzati come *NaN&nbsp;*, ovvero un valore di tipo di dati numerico che rappresenta un valore non definito o non rappresentabile.

![Visualizzazione delle prestazioni dei nodi del contenitore](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Il working set di memoria** mostra sia la memoria residente che la memoria virtuale (cache) incluse ed è un totale di ciò che l'applicazione sta utilizzando. **Memoria RSS** mostra solo la memoria principale (che non è altro che la memoria residente in altre parole). Questa metrica mostra la capacità effettiva della memoria disponibile. Qual è la differenza tra memoria residente e memoria virtuale?

- Memoria residente o memoria principale, è la quantità effettiva di memoria del computer disponibile per i nodi del cluster.

- La memoria virtuale è riservata spazio su disco rigido (cache) utilizzato dal sistema operativo per scambiare i dati dalla memoria al disco quando sotto la pressione della memoria e quindi recuperarlo in memoria quando necessario.

Per impostazione predefinita, i dati sulle prestazioni si basano sulle ultime sei ore, ma è possibile modificare la finestra utilizzando l'opzione **TimeRange** in alto a sinistra. È inoltre possibile filtrare i risultati nell'intervallo di tempo selezionando **Min**, **Avg**, **50th**, **90th**, **95th**e **Max** nel selettore percentile. 

![Selezione del percentile per filtrare i dati](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando si passa il mouse sul grafico a barre sotto la colonna **Tendenza,** ogni barra mostra l'utilizzo della CPU o della memoria, a seconda della metrica selezionata, entro un periodo di esempio di 15 minuti. Dopo aver selezionato il grafico di tendenza tramite una tastiera, utilizzare il tasto Alt , Pgsu o Alt , PGSU per scorrere singolarmente ogni barra. Si ottengono gli stessi dettagli che si otterrebbe se si passa sopra la barra.

![Esempio di grafico a barre di tendenza al passaggio del mouse](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

Nell'esempio successivo, per il primo nodo dell'elenco, *aks-nodepool1-*, il valore di **Containers** è 9. Questo valore è un rollup del numero totale di contenitori distribuiti.

![Esempio di rollup dei contenitori per nodo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Queste informazioni consentono di identificare rapidamente se si dispone di un corretto equilibrio dei contenitori tra i nodi del cluster. 

Le informazioni presentate quando si visualizza la scheda **Nodi** sono descritte nella tabella seguente.

| Colonna | Descrizione | 
|--------|-------------|
| Nome | Nome dell'host. |
| Stato | Visualizzazione Kubernetes dello stato del nodo. |
| Min&nbsp;%,&nbsp;Media %,&nbsp;50 %,&nbsp;90 %, 95 %,&nbsp;Max&nbsp;%  | Percentuale media dei nodi in base al percentile durante l'intervallo di tempo selezionato. |
| Min, Media, 50a, 90a, 95a, Max | Valore effettivo dei nodi medio in base al percentile durante la durata selezionata. Il valore medio viene misurato dal limite di CPU/memoria impostato per un nodo. Per i contenitori e i contenitori, è il valore medio riportato dall'host. |
| Contenitori | Numero di contenitori. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un nodo. |
| Controller | Solo per i contenitori e i pod. Mostra in quale controller risiede. Non tutti i pod sono presenti in un controller. È quindi possibile che per alcuni sia visualizzato **N/A** per indicare che non sono disponibili. | 
| Tendenza&nbsp;Min %,&nbsp;Media %,&nbsp;50 %,&nbsp;90th&nbsp;%, 95th %, Max&nbsp;% | Grafico a barre della tendenza che presenta il valore percentuale medio delle metriche di percentile del controller. |

È possibile notare un carico di lavoro dopo l'espansione di un nodo denominato **Altro processo**. Rappresenta i processi non containerizzati eseguiti nel nodo e include:It represents non-containerized processes that run on your node, and includes:

* Processi Kubernete autogestiti o gestiti

* Processi di runtime del contenitoreContainer run-time processes  

* Kubelet  

* Processi di sistema in esecuzione sul nodo

* Altri carichi di lavoro non Kubernetes in esecuzione sull'hardware o sulla macchina virtuale del nodoOther non-Kubernetes workloads running on node hardware or VM

Viene calcolato per: *Utilizzo totale da CAdvisor* - *Usage dal processo containerizzato*.  

Nel selettore selezionare **Controller**.

![Selezionare la visualizzazione Controller](./media/container-insights-analyze/containers-controllers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei controller e dei controller del nodo virtuale delle istanze del contenitore o dei pod dei nodi virtuali non connessi a un controller.

![\<Visualizzazione delle prestazioni dei controller di> dei nomi](./media/container-insights-analyze/containers-controllers-view.png)

La gerarchia di righe inizia con un controller. Quando si espande un controller, si visualizzano uno o più pod. Espandendo un pod, nell'ultima riga viene mostrato il contenitore raggruppato nel pod. Da un controller espanso, è possibile eseguire il drill-down al nodo in cui è in esecuzione per visualizzare i dati sulle prestazioni filtrati per tale nodo. I pod Istanze contenitore non collegati a un controller sono elencati per ultimi nell'elenco.

![Gerarchia di controller di esempio con pod di Istanze di Container elencati](./media/container-insights-analyze/controllers-view-aci.png)

Selezionare il valore nella colonna **Nodo** per il controller specifico.

![Esempio di drill-down dal nodo al controller nella vista prestazioni](./media/container-insights-analyze/drill-down-controller-node.png)

Le informazioni visualizzate quando si visualizzano i controller è descritto nella tabella seguente.

| Colonna | Descrizione | 
|--------|-------------|
| Nome | Nome del controller.|
| Stato | Stato di rollup dei contenitori al termine dell'esecuzione con stato, ad esempio *OK*, *Terminated*, *Failed*, *Stopped*o *Paused*. Se il contenitore è in esecuzione ma lo stato non è stato visualizzato correttamente o non è stato rilevato dall'agente e non ha risposto per più di 30 minuti, lo stato è *Sconosciuto*. Ulteriori dettagli dell'icona di stato sono riportati nella tabella seguente.|
| Min&nbsp;%,&nbsp;Media %,&nbsp;50 %,&nbsp;90 %, 95 %,&nbsp;Max&nbsp;%| Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Min, Media, 50a, 90a, 95a, Max  | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Contenitori | Numero totale di contenitori per il controller o il pod. |
| Riavvii | Rollup del numero di riavvii dai contenitori. |
| Uptime | Rappresenta il tempo dall'avvio di un contenitore. |
| Nodo | Solo per i contenitori e i pod. Mostra in quale controller risiede. | 
| Tendenza&nbsp;Min %,&nbsp;Media %,&nbsp;50 %,&nbsp;90th&nbsp;%, 95th %, Max&nbsp;% | Grafico a barre della tendenza che presenta il valore percentile medio del controller. |

Le icone nel campo dello stato indicano lo stato online dei contenitori.
 
| Icona | Stato | 
|--------|-------------|
| ![Icona di stato In esecuzione (Pronto)](./media/container-insights-analyze/containers-ready-icon.png) | In esecuzione (Pronto)|
| ![Icona di stato In attesa o In pausa](./media/container-insights-analyze/containers-waiting-icon.png) | In attesa o In pausa|
| ![Icona di stato Ultima esecuzione segnalata](./media/container-insights-analyze/containers-grey-icon.png) | Ultima segnalazione di esecuzione ma non ha risposto per più di 30 minuti|
| ![Icona di stato Operazione riuscita](./media/container-insights-analyze/containers-green-icon.png) | Arresto completato o arresto non riuscito|

L'icona dello stato mostra un numero in base ai dati forniti dal pod. Vengono visualizzati i due stati peggiori. Quando si passa il puntatore del mouse sullo stato, viene visualizzato uno stato di rollup di tutti i pod nel contenitore. Se non è presente alcuno stato Pronto, come stato viene visualizzato **(0)**.

Nel selettore selezionare **Contenitori**.

![Selezionare la visualizzazione Contenitori](./media/container-insights-analyze/containers-containers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei contenitori di Istanze di Azure Container e del servizio Azure Kubernetes. 

![\<Nome> visualizzazione delle prestazioni dei contenitori](./media/container-insights-analyze/containers-containers-view.png)

Da un contenitore è possibile eseguire il drill-down in un pod o nodo per visualizzare i dati sulle prestazioni filtrati per l'oggetto. Selezionare il valore nella colonna **Pod** o **Node** per il contenitore specifico.

![Esempio di drill-down da nodo a contenitori nella visualizzazione prestazioniExample drill-down from node to containers in the performance view](./media/container-insights-analyze/drill-down-controller-node.png)

Le informazioni visualizzate quando si visualizzano i contenitori sono descritte nella tabella seguente.

| Colonna | Descrizione | 
|--------|-------------|
| Nome | Nome del controller.|
| Stato | Stato dei contenitori, se presente. La tabella seguente contiene dettagli aggiuntivi sull'icona dello stato.|
| Min&nbsp;%,&nbsp;Media %,&nbsp;50 %,&nbsp;90 %, 95 %,&nbsp;Max&nbsp;% | Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Min, Media, 50a, 90a, 95a, Max | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Pod | Contenitore in cui è presente il pod.| 
| Nodo |  Nodo in cui è presente il contenitore. | 
| Riavvii | Rappresenta il tempo dall'avvio di un contenitore. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un contenitore. |
| Tendenza&nbsp;Min %,&nbsp;Media %,&nbsp;50 %,&nbsp;90th&nbsp;%, 95th %, Max&nbsp;% | Grafico a barre della tendenza che presenta il valore percentuale medio delle metriche di percentile del contenitore. |

Le icone nel campo status indicano lo stato online dei pod, come descritto nella tabella seguente.
 
| Icona | Stato |  
|--------|-------------|  
| ![Icona di stato In esecuzione (Pronto)](./media/container-insights-analyze/containers-ready-icon.png) | In esecuzione (Pronto)|  
| ![Icona di stato In attesa o In pausa](./media/container-insights-analyze/containers-waiting-icon.png) | In attesa o In pausa|  
| ![Icona di stato Ultima esecuzione segnalata](./media/container-insights-analyze/containers-grey-icon.png) | Ultima esecuzione segnalata ma senza risposta da oltre 30 minuti|  
| ![Icona di stato Terminato](./media/container-insights-analyze/containers-terminated-icon.png) | Arresto completato o arresto non riuscito|  
| ![Icona di stato Non riuscito](./media/container-insights-analyze/containers-failed-icon.png) | Stato Non riuscito |  

## <a name="workbooks"></a>Workbooks

Le cartelle di lavoro combinano testo, [query di log,](../log-query/query-language.md) [metriche](../platform/data-platform-metrics.md)e parametri in report interattivi avanzati. Le cartelle di lavoro possono essere modificate da tutti gli altri membri del team che possono accedere alla stessa risorsa di Azure.

Monitoraggio di Azure per i contenitori include quattro cartelle di lavoro per iniziare:Azure Monitor for containers includes four workbooks to get you started:

- **Capacità disco**: Presenta i grafici interattivi sull'utilizzo del disco per ogni disco presentato al nodo all'interno di un contenitore in base alle prospettive seguenti:

    - Utilizzo percentuale disco per tutti i dischi.
    - Spazio libero su disco per tutti i dischi.
    - Griglia che mostra il disco di ogni nodo, la percentuale di spazio utilizzato, la tendenza della percentuale di spazio utilizzato, lo spazio libero su disco (GiB) e la tendenza dello spazio libero su disco (GiB). Quando si seleziona una riga nella tabella, la percentuale di spazio utilizzato e spazio libero su disco (GiB) viene visualizzata sotto la riga. 

- **Disk IO**: Presenta i grafici interattivi sull'utilizzo del disco per ogni disco presentato al nodo all'interno di un contenitore in base alle prospettive seguenti:

    - I/O disco riepilogati su tutti i dischi in byte di lettura/sec, byte/sec e tendenze di lettura e scrittura byte/sec.
    - Otto grafici delle prestazioni mostrano indicatori di prestazioni chiave per misurare e identificare i colli di bottiglia di I/O del disco.

- **Kubelet**: Include due griglie che mostrano le statistiche operative dei nodi chiave:

    - La panoramica per griglia nodo riepiloga le operazioni totali, gli errori totali e le operazioni riuscite in base alla percentuale e alla tendenza per ogni nodo.
    - Panoramica per tipo di operazione riepiloga per ogni operazione l'operazione totale, gli errori totali e le operazioni riuscite in percentuale e tendenza.

- **Rete**: presenta grafici interattivi sull'utilizzo della rete per la scheda di rete di ogni nodo e una griglia presenta gli indicatori di prestazioni chiave per misurare le prestazioni delle schede di rete.

È possibile accedere a queste cartelle di lavoro selezionandole dall'elenco a discesa **Visualizza cartelle di lavoro.**

![Visualizza cartella di lavoro elenco a discesa](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Passaggi successivi

- Vedere Creare avvisi sulle prestazioni con Monitoraggio di Azure per i [contenitori per](container-insights-alerts.md) informazioni su come creare avvisi per un utilizzo elevato della CPU e della memoria per supportare i processi e le procedure devOps o operativi.

- Visualizzare esempi di query di [log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query predefinite ed esempi per valutare o personalizzare l'avviso, la visualizzazione o l'analisi dei cluster.

- Visualizzare [l'integrità](container-insights-health.md) del cluster di monitoraggio per informazioni sulla visualizzazione dello stato di integrità del cluster Kubernetes.View monitor cluster health to learn about viewing the health status your Kubernetes cluster.
