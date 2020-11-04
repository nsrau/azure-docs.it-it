---
title: Riferimento ai dati di monitoraggio Azure Machine Learning | Microsoft Docs
description: Materiale di riferimento importante necessario per il monitoraggio Azure Machine Learning. Informazioni sui dati e sulle risorse raccolti per Azure Machine Learning e disponibili in monitoraggio di Azure. Monitoraggio di Azure raccoglie e Visualizza i dati relativi all'area di lavoro Azure Machine Learning e consente di visualizzare le metriche, impostare gli avvisi e analizzare i dati registrati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/02/2020
ms.openlocfilehash: be8d6ca79a43ddd2bd709390dd476cb9dcfa7b29
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323938"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Monitoraggio del riferimento ai dati di Azure Machine Learning

Informazioni sui dati e sulle risorse raccolti da monitoraggio di Azure dall'area di lavoro Azure Machine Learning. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Metriche

Questa sezione elenca tutte le metriche della piattaforma raccolte automaticamente raccolte per Azure Machine Learning. Il provider di risorse per queste metriche è [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/platform/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modello**

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Distribuzione del modello non riuscita | Conteggio | Numero di distribuzioni di modelli non riuscite. |
| Distribuzione modello avviata | Conteggio | Numero di distribuzioni di modelli avviate. |
| Distribuzione del modello completata | Conteggio | Numero di distribuzioni di modelli completate correttamente. |
| Registrazione modello non riuscita | Conteggio | Numero di registrazioni del modello non riuscite. |
| Registrazione del modello completata | Conteggio | Numero di registrazioni del modello completate correttamente. |

**Quota**

Le informazioni sulle quote sono destinate solo ai Azure Machine Learning di calcolo.

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Core attivi | Conteggio | Numero di core di calcolo attivi. |
| Nodi attivi | Conteggio | Numero di nodi attivi. |
| Core inattivi | Conteggio | Numero di core di calcolo inattivi. |
| Nodi inattivi | Conteggio | Numero di nodi di calcolo inattivi. |
| Uscita da Core | Conteggio | Numero di core in uscita. |
| Uscita da nodi | Conteggio | Numero di nodi in uscita. |
| Core con precedenza | Conteggio | Numero di core con precedenza. |
| Nodi interrotti | Conteggio | Numero di nodi con precedenza. |
| Percentuale di utilizzo della quota | Percentuale | Percentuale di quota utilizzata. |
| Totale core | Conteggio | Core totali. |
| Totale nodi | Conteggio | Nodi totali. |
| Core inutilizzabili | Conteggio | Numero di core inutilizzabili. |
| Nodi inutilizzabili | Conteggio | Numero di nodi inutilizzabili. |

**Risorsa**

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| CpuUtilization | Percentuale | Percentuale di utilizzo della CPU per un determinato nodo durante un'esecuzione o un processo. Questa metrica viene pubblicata solo quando un processo è in esecuzione in un nodo. Un processo può usare uno o più nodi. Questa metrica è pubblicata per ogni nodo. |
| GpuUtilization | Percentuale | Percentuale di utilizzo della GPU per un determinato nodo durante un'esecuzione o un processo. Un nodo può avere una o più GPU. Questa metrica è pubblicata per ogni GPU per ogni nodo. |

**Esegui**

Informazioni sulle esecuzioni di training.

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Esecuzioni completate | Conteggio | Numero di esecuzioni completate. |
| Esecuzioni non riuscite | Conteggio | Numero di esecuzioni non riuscite. |
| Esecuzioni avviate | Conteggio | Numero di esecuzioni avviate. |

## <a name="metric-dimensions"></a>Dimensioni metrica

Per altre informazioni sulle dimensioni della metrica, vedere [metriche multidimensionali](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning presenta le dimensioni seguenti associate alle metriche.

| Dimension | Descrizione |
| ---- | ---- |
| Cluster Name | Nome dell'istanza di calcolo. Disponibile per tutte le metriche delle quote. |
| Nome della famiglia di macchine virtuali | Nome della famiglia di macchine virtuali usata dal cluster. Disponibile per la percentuale di utilizzo della quota. |
| Priorità VM | Priorità della macchina virtuale. Disponibile per la percentuale di utilizzo della quota.
| CreatedTime | Disponibile solo per CpuUtilization e GpuUtilization. |
| DeviceId | ID del dispositivo (GPU). Disponibile solo per GpuUtilization. |
| NodeId | ID del nodo creato in cui è in esecuzione il processo. Disponibile solo per CpuUtilization e GpuUtilization. |
| RunId | ID dell'esecuzione/processo. Disponibile solo per CpuUtilization e GpuUtilization. |
| ComputeType | Tipo di calcolo utilizzato dall'esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |
| PipelineStepType | Tipo di [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?preserve-view=true&view=azure-ml-py) utilizzato nell'esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |
| PublishedPipelineId | ID della pipeline pubblicata utilizzata nell'esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |
| RunType | Tipo di esecuzione. Disponibile solo per esecuzioni completate, esecuzioni non riuscite e Esecuzioni avviate. |

I valori validi per la dimensione RunType sono i seguenti:

| valore | Descrizione |
| ----- | ----- |
| Esperimento | Esecuzioni non pipeline. |
| PipelineRun | Esecuzione di una pipeline, che è l'elemento padre di un StepRun. |
| StepRun | Esecuzione di un passaggio della pipeline. |
| ReusedStepRun | Esecuzione di un passaggio della pipeline che riutilizza un'esecuzione precedente. |

## <a name="activity-log"></a>Log attività

Nella tabella seguente sono elencate le operazioni correlate a Azure Machine Learning che possono essere create nel log attività.

| Operazione | Descrizione |
|:---|:---|
| Crea o aggiorna un'area di lavoro Machine Learning | Un'area di lavoro è stata creata o aggiornata |
| CheckComputeNameAvailability | Controllare se un nome di calcolo è già in uso |
| Crea o aggiorna le risorse di calcolo | Una risorsa di calcolo è stata creata o aggiornata |
| Elimina le risorse di calcolo | Una risorsa di calcolo è stata eliminata |
| List secrets | Sui segreti dell'operazione elencati per un'area di lavoro Machine Learning |

## <a name="resource-logs"></a>Log risorse

Questa sezione elenca i tipi di log delle risorse che è possibile raccogliere per Azure Machine Learning area di lavoro.

Provider di risorse e tipo: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/platform/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Category | Nome visualizzato |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schemi

Gli schemi seguenti sono utilizzati da Azure Machine Learning

### <a name="amlcomputejobevents-table"></a>Tabella AmlComputeJobEvents

| Proprietà | Descrizione |
|:--- |:---|
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Category | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| JobId | ID del processo inviato |
| ExperimentId | ID dell'esperimento |
| Experimentname | Nome dell'esperimento |
| CustomerSubscriptionId | SubscriptionId in cui l'esperimento e il processo sono stati inviati |
| WorkspaceName | Nome dell'area di lavoro di Machine Learning |
| ClusterName | Nome del cluster |
| ProvisioningState | Stato dell'invio del processo |
| ResourceGroupName | Nome del gruppo di risorse |
| JobName | Nome del processo |
| ClusterId | ID del cluster |
| EventType | Tipo di evento del processo. Ad esempio, JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Stato del processo (esecuzione). Ad esempio, in coda, in esecuzione, riuscita, non riuscita |
| ErrorDetails | Dettagli dell'errore del processo |
| CreationApiVersion | Versione dell'API usata per creare il processo |
| ClusterResourceGroupName | Nome del gruppo di risorse del cluster |
| TFWorkerCount | Numero di processi di lavoro TF |
| TFParameterServerCount | Conteggio del server dei parametri TF |
| ToolType | Tipo di strumento utilizzato |
| RunInContainer | Flag che descrive se il processo deve essere eseguito all'interno di un contenitore |
| JobErrorMessage | messaggio dettagliato di errore del processo |
| NodeId | ID del nodo creato in cui è in esecuzione il processo |

### <a name="amlcomputeclusterevents-table"></a>Tabella AmlComputeClusterEvents

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Category | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| ProvisioningState | Stato del provisioning del cluster |
| ClusterName | Nome del cluster |
| ClusterType | Tipo di cluster |
| CreatedBy | Utente che ha creato il cluster |
| CoreCount | Conteggio dei core nel cluster |
| VmSize | Dimensioni della macchina virtuale del cluster |
| VmPriority | Priorità dei nodi creati all'interno di un cluster dedicato/LowPriority |
| ScalingType | Tipo di ridimensionamento manuale/automatico del cluster |
| InitialNodeCount | Numero iniziale di nodi del cluster |
| MinimumNodeCount | Numero minimo di nodi del cluster |
| MaximumNodeCount | Numero massimo di nodi del cluster |
| NodeDeallocationOption | Come deallocare il nodo |
| Editore | Server di pubblicazione del tipo di cluster |
| Offerta | Offerta con cui viene creato il cluster |
| Sku | SKU del nodo/VM creato nel cluster |
| Versione | Versione dell'immagine usata durante la creazione del nodo o della VM |
| SubnetId | SubnetId del cluster |
| AllocationState | Stato di allocazione cluster |
| CurrentNodeCount | Numero corrente di nodi del cluster |
| TargetNodeCount | Numero di nodi di destinazione del cluster durante la scalabilità verso l'alto o verso il basso |
| EventType | Tipo di evento durante la creazione del cluster. |
| NodeIdleTimeSecondsBeforeScaleDown | Tempo di inattività in secondi prima che il cluster venga ridimensionato |
| PreemptedNodeCount | Numero di nodi di cui è stato interrotto il cluster |
| IsResizeGrow | Flag che indica la scalabilità verticale del cluster |
| VmFamilyName | Nome della famiglia di macchine virtuali dei nodi che possono essere creati nel cluster |
| LeavingNodeCount | Uscita dal numero di nodi del cluster |
| UnusableNodeCount | Numero di nodi non utilizzabili del cluster |
| IdleNodeCount | Numero di nodi inattivi del cluster |
| RunningNodeCount | Conteggio dei nodi in esecuzione del cluster |
| PreparingNodeCount | Preparazione del conteggio dei nodi del cluster |
| QuotaAllocated | Quota allocata al cluster |
| QuotaUtilized | Quota utilizzata del cluster |
| AllocationStateTransitionTime | Tempo di transizione da uno stato a un altro |
| ClusterErrorCodes | Codice di errore ricevuto durante la creazione o il ridimensionamento del cluster |
| CreationApiVersion | Versione API usata durante la creazione del cluster |

### <a name="amlcomputeclusternodeevents-table"></a>Tabella AmlComputeClusterNodeEvents

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora di generazione della voce di log |
| OperationName | Nome dell'operazione associata all'evento del log |
| Category | Nome dell'evento del log, AmlComputeClusterNodeEvent |
| ClusterName | Nome del cluster |
| NodeId | ID del nodo del cluster creato |
| VmSize | Dimensioni della macchina virtuale del nodo |
| VmFamilyName | Famiglia di macchine virtuali a cui appartiene il nodo |
| VmPriority | Priorità del nodo creato/LowPriority dedicato |
| Editore | Autore dell'immagine della macchina virtuale. Ad esempio, Microsoft-dsvm |
| Offerta | Offerta associata alla creazione della macchina virtuale |
| Sku | SKU del nodo o della macchina virtuale creata |
| Versione | Versione dell'immagine usata durante la creazione del nodo o della VM |
| ClusterCreationTime | Ora di creazione del cluster |
| ResizeStartTime | Ora di inizio/discesa della scalabilità del cluster |
| ResizeEndTime | Ora di fine della scalabilità verticale del cluster |
| NodeAllocationTime | Ora di allocazione del nodo |
| NodeBootTime | Ora di avvio del nodo |
| StartTaskStartTime | Ora in cui l'attività è stata assegnata a un nodo e avviata |
| StartTaskEndTime | Ora di fine dell'attività assegnata a un nodo |
| TotalE2ETimeInSeconds | Tempo totale nodo attivo |


## <a name="see-also"></a>Vedere anche

- Per una descrizione del monitoraggio Azure Machine Learning, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md) .
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere [Monitoraggio delle risorse di Azure con Monitoraggio di Azure](../azure-monitor/insights/monitor-azure-resource.md).