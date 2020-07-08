---
title: Costi di monitoraggio per il monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive il costo di monitoraggio per le metriche & i dati di inventario raccolti da monitoraggio di Azure per i contenitori per consentire ai clienti di gestire l'utilizzo e i costi associati.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: a03e94fa7650c56a4d3b3beda3c27283329aebbe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84204651"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>Informazioni sui costi di monitoraggio per il monitoraggio di Azure per i contenitori

Questo articolo fornisce indicazioni sui prezzi di monitoraggio di Azure per i contenitori che consentono di comprendere quanto segue:

* Come stimare i costi in anticipo prima di abilitare questa analisi

* Come misurare i costi dopo l'abilitazione di monitoraggio di Azure per i contenitori per uno o più contenitori

* Come controllare la raccolta di dati e ridurre i costi

Log di monitoraggio di Azure raccoglie, indicizza e archivia i dati generati dal cluster Kubernetes. 

Il modello di determinazione dei prezzi di monitoraggio di Azure si basa principalmente sulla quantità di dati inseriti in gigabyte al giorno nell'area di lavoro Log Analytics. Il costo di un'area di lavoro Log Analytics non è basato solo sul volume dei dati raccolti, ma dipende anche dal piano selezionato e dal periodo di tempo in cui si è scelto di archiviare i dati generati dai cluster.

>[!NOTE]
>Tutte le dimensioni e i prezzi sono solo per la stima di esempio. Vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/monitor/) di monitoraggio di Azure per i prezzi più recenti in base al modello di prezzi log Analytics di monitoraggio di Azure e all'area di Azure.

Di seguito è riportato un riepilogo dei tipi di dati raccolti da un cluster Kubernetes con monitoraggio di Azure per i contenitori che influenzano i costi e possono essere personalizzati in base all'utilizzo:

- Stdout, log del contenitore stderr da ogni contenitore monitorato in ogni spazio dei nomi Kubernetes nel cluster

- Variabili di ambiente contenitore da ogni contenitore monitorato nel cluster

- Processi Kubernetes/Pod completati nel cluster che non richiedono il monitoraggio

- Scraping attivo della metrica Prometheus

- [Raccolta dei log di diagnostica](../../aks/view-master-logs.md) dei log del nodo master Kubernetes nel cluster AKS per analizzare i dati di log generati dai componenti Master, ad esempio *Kube-apiserver* e *Kube-Controller-Manager*.

## <a name="what-is-collected-from-kubernetes-clusters"></a>Cosa viene raccolto dai cluster Kubernetes

Il monitoraggio di Azure per i contenitori include un set predefinito di metriche e di elementi di inventario raccolti che vengono scritti come dati di log nell'area di lavoro Log Analytics. Tutte le metriche elencate di seguito vengono raccolte per impostazione predefinita ogni minuto.

### <a name="node-metrics-collected"></a>Metriche del nodo raccolte

L'elenco seguente indica le 24 metriche per nodo raccolte:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- usato (disco)
- disponibile (disco)
- used_percent (disco)
- io_time (diskio)
- Scritture (diskio)
- letture (diskio)
- write_bytes (diskio)
- write_time (diskio)
- iops_in_progress (diskio)
- read_bytes (diskio)
- read_time (diskio)
- err_in (NET)
- err_out (NET)
- bytes_recv (NET)
- bytes_sent (NET)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Metriche del contenitore

L'elenco seguente indica le otto metriche per ogni contenitore raccolto:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Inventario cluster

L'elenco seguente include i dati di inventario del cluster raccolti per impostazione predefinita:

- KubePodInventory-1 al minuto per contenitore
- KubeNodeInventory-1 per nodo al minuto
- KubeServices-1 per servizio al minuto
- ContainerInventory-1 per contenitore al minuto

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Stima dei costi per il monitoraggio del cluster AKS

La stima seguente si basa su un cluster di Azure Kubernetes Service (AKS) con il seguente esempio di ridimensionamento. Inoltre, la stima è valida solo per le metriche e i dati di inventario raccolti. Per i log dei contenitori (stdout, stderr e le variabili di ambiente), varia in base alle dimensioni del log generate dal carico di lavoro e sono escluse dalla stima.

Se è stato abilitato il monitoraggio di un cluster AKS configurato come segue,

- Tre nodi
- Due dischi per nodo
- Un'interfaccia di rete per nodo
- 20 pod (un contenitore in ogni pod = 20 contenitori in totale)
- Due spazi dei nomi Kubernetes
- Cinque servizi Kubernetes (inclusi i pod, i servizi e lo spazio dei nomi di sistema Kube)
- Frequenza di raccolta = 60 sec (impostazione predefinita)

È possibile visualizzare le tabelle e il volume dei dati generati all'ora nell'area di lavoro Log Analytics assegnata. Per ulteriori informazioni su ognuna di queste tabelle, vedere [record di contenitori](container-insights-log-search.md#container-records).

|Tabella | Dimensioni stimate (MB/ora) |
|------|---------------|
|Perf | 12,9 |
|InsightsMetrics | 11.3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3.6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0,005 |

Totale = 31 MB/ora = 23,1 GB/mese (un mese = 31 giorni)

Usando i [prezzi](https://azure.microsoft.com/pricing/details/monitor/) predefiniti per log Analytics, ovvero un modello con pagamento in base al consumo, è possibile stimare i costi di monitoraggio di Azure al mese. Dopo aver incluso una prenotazione di capacità, il prezzo sarà superiore al mese, a seconda della prenotazione selezionata.

## <a name="controlling-ingestion-to-reduce-cost"></a>Controllo dell'inserimento per ridurre i costi

Si consideri uno scenario in cui le diverse unità aziendali dell'organizzazione condividono l'infrastruttura Kubernetes e un'area di lavoro Log Analytics. Ogni business unit è separato da uno spazio dei nomi Kubernetes. È possibile visualizzare la quantità di dati da inserire in ogni area di lavoro utilizzando una cartella di lavoro rilasciata di recente. La cartella di lavoro di **utilizzo di container Insights** , disponibile nella [raccolta di cartelle di lavoro](../platform/workbooks-overview.md#getting-started), consente di visualizzare l'origine dei dati senza dover creare una libreria di query personalizzata da quanto condiviso nella documentazione. In questa cartella di lavoro sono disponibili grafici con i quali è possibile visualizzare i dati fatturabili da tali prospettive come:

- Totale dei dati fatturabili inseriti in GB per soluzione

- Dati fatturabili inseriti da log del contenitore (registri applicazioni)

- Contenitori fatturabili i dati inseriti in base allo spazio dei nomi Kubernetes

- Dati dei log del contenitore fatturabili inseriti isolati dal nome del cluster

- Dati del log del contenitore fatturabili inseriti dalla voce LogSource

- Dati di diagnostica fatturabili inseriti da log del nodo master di diagnostica

Per informazioni sulla gestione dei diritti e delle autorizzazioni per la cartella di lavoro, vedere [controllo di accesso](../platform/workbooks-access-control.md).

Dopo aver completato l'analisi per determinare l'origine o le origini che generano la maggior parte dei dati o più dati che superano i requisiti, è possibile riconfigurare la raccolta dei dati. Informazioni dettagliate sulla configurazione della raccolta di stdout, stderr e variabili di ambiente sono descritte nell'articolo [configurare le impostazioni di raccolta dati agenti](container-insights-agent-config.md) .

Di seguito sono riportati alcuni esempi di modifiche che è possibile applicare al cluster modificando il file ConfigMap per consentire il controllo dei costi.

1. Disabilitare i log stdout in tutti gli spazi dei nomi nel cluster modificando quanto segue nel file ConfigMap:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Disabilitare la raccolta di log stderr dallo spazio dei nomi di sviluppo (ad esempio, **dev-test**) e continuare a raccogliere i log stderr da altri spazi dei nomi (ad esempio, **Prod** e **default**) modificando quanto segue nel file ConfigMap:

    >[!NOTE]
    >Per impostazione predefinita, la raccolta dei log di sistema KUBE è disabilitata. L'impostazione predefinita viene mantenuta. l'aggiunta dello spazio dei nomi **dev-test** all'elenco degli spazi dei nomi di esclusione viene applicata alla raccolta di log stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Disabilitare la raccolta delle variabili di ambiente nell'intero cluster modificando il codice seguente nel file ConfigMap. Questa operazione è applicabile a tutti i contenitori in ogni spazio dei nomi Kubernetes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. Per pulire i processi completati, specificare i criteri di pulizia nella definizione del processo modificando quanto segue nel file ConfigMap:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Dopo aver applicato una o più di queste modifiche alla ConfigMaps, vedere [applicazione di ConfigMap aggiornati](container-insights-prometheus-integration.md#applying-updated-configmap) per applicarla al cluster.

### <a name="prometheus-metrics-scraping"></a>Frammentazione metrica Prometeo

Se si usa il [raschietto della metrica Prometheus](container-insights-prometheus-integration.md), assicurarsi di prendere in considerazione quanto segue per limitare il numero di metriche raccolte dal cluster:

- Verificare che la frequenza di scarto sia impostata in modo ottimale (il valore predefinito è 60 secondi). Sebbene sia possibile aumentare la frequenza a 15 secondi, è necessario assicurarsi che le metriche da eliminare vengano pubblicate con tale frequenza. In caso contrario, verranno ricavate molte metriche duplicate e inviate all'area di lavoro Log Analytics a intervalli che si aggiungono ai costi di inserimento e conservazione dei dati, ma sono meno di valore. 

- Il monitoraggio di Azure per i contenitori supporta l'esclusione & gli elenchi di inclusione per nome della metrica. Se, ad esempio, si eliminano le metriche **kubedns** nel cluster, potrebbero essere presenti centinaia di elementi che vengono ripartiti per impostazione predefinita, ma è molto probabile che si interessi solo a un subset. Confermare di aver specificato un elenco di metriche da ritirare oppure escluderne altre tranne alcune per salvare il volume di inserimento dati. È facile abilitare la raschiatura e non usare molte di queste metriche, che aggiungono costi aggiuntivi alla fattura del Log Analytics.

- Quando si esegue il frammento delle annotazioni Pod, assicurarsi di filtrare in base allo spazio dei nomi in modo da escludere il frammento di metriche Pod dagli spazi dei nomi che non vengono usati, ad esempio lo spazio dei nomi **dev-test** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come comprendere i costi che potrebbero essere basati sui modelli di utilizzo recenti dei dati raccolti con monitoraggio di Azure per i contenitori, vedere [gestire l'utilizzo e stimare i costi](../platform/manage-cost-storage.md).