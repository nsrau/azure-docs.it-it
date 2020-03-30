---
title: Informazioni di riferimento sui dati di monitoraggio Documenti Microsoft
titleSuffix: Azure Machine Learning
description: Informazioni sui dati e le risorse raccolti per Azure Machine Learning e disponibili in Monitoraggio di Azure.Learn about the data and resources collected for Azure Machine Learning, and available in Azure Monitor. Monitoraggio di Azure raccoglie e espone i dati sull'area di lavoro di Azure Machine Learning e consente di visualizzare le metriche, impostare avvisi e analizzare i dati registrati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927556"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Informazioni di riferimento sui dati di monitoraggio di Azure machine LearningAzure machine learning monitoring data reference

Informazioni sui dati e le risorse raccolti da Monitoraggio di Azure dall'area di lavoro di Azure Machine Learning.Learn about the data and resources collected by Azure Monitor from your Azure Machine Learning workspace. Per informazioni dettagliate sulla raccolta e l'analisi dei dati di monitoraggio, vedere [Monitoraggio di Azure Machine Learning.See Monitoring Azure Machine Learning](monitor-azure-machine-learning.md) for details on collecting and analyzing monitoring data.

## <a name="resource-logs"></a>Log risorse

The following table lists the properties for Azure Machine Learning resource logs when they're collected in Azure Monitor Logs or Azure Storage.

### <a name="amlcomputejobevents-table"></a>Tabella AmlComputeJobEvents

| Proprietà | Descrizione |
|:--- |:---|
| TimeGenerated | Ora in cui è stata generata la voce di registro |
| OperationName | Nome dell'operazione associata all'evento di registro |
| Category | Nome dell'evento di registro, AmlComputeClusterNodeEvent |
| JobId | ID del lavoro inviato |
| Id Esperimento | ID dell'esperimento |
| Nome Esperimento | Nome dell'esperimento |
| IdSottoscrizione Cliente | SubscriptionId in cui esperimento e processo come inviatoSubscriptionId where Experiment and Job as submitted |
| Nomearea di lavoro | Nome dell'area di lavoro di Machine Learning |
| ClusterName | Nome del cluster |
| ProvisioningState | Stato dell'invio del lavoro |
| ResourceGroupName | Nome del gruppo di risorse |
| JobName | Nome del lavoro |
| ClusterId | ID del cluster |
| EventType | Tipo dell'evento Job, ad esempio JobSubmitted, JobRunning, JobFailed, JobSucceeded e così via. |
| ExecutionState | Stato del processo (Esegui), ad esempio In coda, In esecuzione, Operazione riuscita, Non riuscito |
| ErrorDetails | Dettagli dell'errore di processo |
| CreationApiVersion | Versione api utilizzata per creare il processo |
| Nome ClusterResourceGroup | Nome del gruppo di risorse del cluster |
| TFWorkerCount (conteggio dei conti) | Conteggio dei lavoratori TF |
| TFParameterServerCount (conteggio dei parametri) | Conteggio del server dei parametri TFCount of TF parameter server |
| Tipo utensile | Tipo di utensile utilizzato |
| RunInContainer | Flag che descrive se il processo deve essere eseguito all'interno di un contenitore |
| Messaggio JobErrorMessage | messaggio dettagliato di errore di processo |
| NodeId | ID del nodo creato in cui è in esecuzione il processo |

### <a name="amlcomputeclusterevents-table"></a>AmlComputeClusterEvents (tabella)

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora in cui è stata generata la voce di registro |
| OperationName | Nome dell'operazione associata all'evento di registro |
| Category | Nome dell'evento di registro, AmlComputeClusterNodeEvent |
| ProvisioningState | Stato di provisioning del cluster |
| ClusterName | Nome del cluster |
| ClusterType (Tipo cluster) | Tipo di cluster |
| CreatedBy | Utente che ha creato il cluster |
| CoreCount | Conteggio dei core nel clusterCount of the cores in the cluster |
| VmSize (Dimensioni) | Dimensioni del cluster |
| VmPriority | Priorità dei nodi creati all'interno di un cluster Dedicated/LowPriority |
| ScalingType | Tipo di ridimensionamento manuale/automatico del cluster |
| InitialNodeCount (ConteggioNodo iniziale) | Numero di nodi iniziali del cluster |
| MinimumNodeCount | Numero minimo di nodi del cluster |
| MaximumNodeCount | Numero massimo di nodi del cluster |
| NodeDelocationOption | Modalità di deallocata del nodo |
| Editore | Server di pubblicazione del tipo di cluster |
| Offerta | Offerta con cui viene creato il cluster |
| Sku | Sku del nodo/VM creato all'interno del cluster |
| Versione | Versione dell'immagine usata durante la creazione del nodo/VM |
| SubnetId (ID Subnet) | SubnetId del cluster |
| AllocationState (Stato di allocazione) | Stato di allocazione cluster |
| CurrentNodeCount | Conteggio dei nodi correnti del cluster |
| TargetNodeCount | Conteggio dei nodi di destinazione del cluster durante la scalabilità verticale/inbassoTarget node count of the cluster while scaling up/down |
| EventType | Tipo di evento durante la creazione del cluster. |
| NodoIdleTimeSecondsBeforeScaleDown | Tempo di inattività in secondi prima della scalabilità verticale del cluster |
| PreemptedNodeCount | Conteggio dei nodi con stato non necessario del cluster |
| IsResizeGrow (Informazioni in base ai coi | Flag che indica che il cluster è in scala verticale |
| NomeFamigliaVmFamilyName | Nome della famiglia di macchine virtuali dei nodi che è possibile creare all'interno del cluster |
| LeavingNodeCount | Lasciando il numero di nodi del cluster |
| UnusableNodeCount | Numero di nodi inutilizzabili del cluster |
| IdleNodeCount | Conteggio dei nodi inattivi del cluster |
| RunningNodeCount | Conteggio dei nodi in esecuzione del cluster |
| PreparingNodeCount | Preparazione del numero di nodi del cluster |
| QuotaAllocated QuotaAllocated | Quota allocata al cluster |
| QuotaUtilizzato | Quota utilizzata del cluster |
| AllocazioneStateTransitionTime | Tempo di transizione da uno stato all'altro |
| ClusterErrorCodes | Codice di errore ricevuto durante la creazione o la scalabilità del clusterError code received during cluster creation or scaling |
| CreationApiVersion | Versione api utilizzata durante la creazione del cluster |

### <a name="amlcomputeclusternodeevents-table"></a>AmlComputeClusterNodeEvents (tabella)

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated | Ora in cui è stata generata la voce di registro |
| OperationName | Nome dell'operazione associata all'evento di registro |
| Category | Nome dell'evento di registro, AmlComputeClusterNodeEvent |
| ClusterName | Nome del cluster |
| NodeId | ID del nodo del cluster creato |
| VmSize (Dimensioni) | Dimensioni della macchina virtuale del nodo |
| NomeFamigliaVmFamilyName | Famiglia Vm a cui appartiene il nodo |
| VmPriority | Priorità del nodo creato dedicato/LowPriority |
| Editore | Editore dell'immagine della macchina virtuale, ad esempio microsoft-dsvm |
| Offerta | Offerta associata alla creazione della macchina virtuale |
| Sku | Sku del nodo/VM creato |
| Versione | Versione dell'immagine usata durante la creazione del nodo/VM |
| ClusterCreationTime | Ora di creazione del cluster |
| RidimensionaOra | Ora di inizio della scalabilità verticale/giù del cluster |
| RidimensionaFineOra | Ora di fine della scalabilità verticale/ridotta del cluster |
| NodoAllocazioneTempo | Ora di allocazione del nodo |
| NodeBootTime (Ora di avvio dei nodi | Ora di avvio del nodo |
| StartTaskStartTime (InizioTask) | Ora in cui l'attività è stata assegnata a un nodo e avviata |
| StartTaskEndTime | Ora di fine dell'assegnazione dell'attività a un nodo |
| TotalE2ETimeInSeconds | Nodo temporale totale attivo |

### <a name="metrics"></a>Metriche

Nelle tabelle seguenti sono elencate le metriche della piattaforma raccolte per Azure Machine Learning Tutte le metriche vengono archiviate nello spazio dei nomi Area di lavoro di Azure Machine Learning.The following tables list the platform metrics collected for Azure Machine Learning All metrics are stored in the namespace **Azure Machine Learning Workspace**.

**Modello**

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Distribuzione del modello non riuscita | Conteggio | Numero di distribuzioni di modelli non riuscite. |
| Distribuzione del modello avviata | Conteggio | Numero di distribuzioni di modelli avviate. |
| Distribuzione del modello completata | Conteggio | Numero di distribuzioni di modelli riuscite. |
| Registro modelli non riuscito | Conteggio | Numero di registrazioni del modello non riuscite. |
| Registro modelli riuscito | Conteggio | Numero di registrazioni del modello riuscite. |

**Quota**

Le informazioni sulle quote sono solo per il calcolo di Azure Machine Learning.Quota information is only for Azure Machine Learning compute.

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Nuclei attivi | Conteggio | Numero di core di calcolo attivi. |
| Nodi attivi | Conteggio | Numero di nodi attivi. |
| Core inattivi | Conteggio | Numero di core di calcolo inattivi. |
| Nodi inattivi | Conteggio | Numero di nodi di calcolo inattivi. |
| Lasciare i nuclei | Conteggio | Il numero di nuclei che lasciano. |
| Lasciare i nodi | Conteggio | Numero di nodi in uscita. |
| Nuclei anticipati | Conteggio | Numero di core anticipati. |
| Nodi con preempted | Conteggio | Numero di nodi con preempted. |
| Percentuale di utilizzo della quota | Percentuale | Percentuale di quota utilizzata. |
| Totale core | Conteggio | I nuclei totali. |
| Nodi totali | Conteggio | I nodi totali. |
| Nuclei inutilizzabili | Conteggio | Numero di core inutilizzabili. |
| Nodi inutilizzabili | Conteggio | Numero di nodi inutilizzabili. |

Di seguito sono riportate le dimensioni che possono essere utilizzate per filtrare le metriche delle quote:

| Dimension | Metriche disponibili con | Descrizione |
| ---- | ---- | ---- |
| Cluster Name | Tutte le metriche di quota | Nome dell'istanza di calcolo. |
| Nome famiglia Vm | Percentuale di utilizzo della quota | Nome della famiglia di macchine virtuali usata dal cluster. |
| Priorità Vm | Percentuale di utilizzo della quota | Priorità della macchina virtuale.

**Esegui**

Informazioni sulle prove di formazione.

| Metrica | Unità | Descrizione |
| ----- | ----- | ----- |
| Esecuzioni completate | Conteggio | Numero di esecuzioni completate. |
| Esecuzioni non riuscite | Conteggio | Numero di esecuzioni non riuscite. |
| Esecuzioni avviate | Conteggio | Numero di esecuzioni avviate. |

Di seguito sono riportate le dimensioni che possono essere utilizzate per filtrare le metriche di esecuzione:The following are dimensions that can be used to filter run metrics:

| Dimension | Descrizione |
| ---- | ---- |
| ComputeType | Tipo di calcolo utilizzato dall'esecuzione. |
| PipelineStepTypePipelineStepType | Tipo di [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) utilizzato nell'esecuzione. |
| PublishedPipelineId | ID della pipeline pubblicata utilizzata nell'esecuzione. |
| Tipo Run | Tipo di esecuzione. |

I valori validi per la dimensione RunType sono:

| valore | Descrizione |
| ----- | ----- |
| Esperimento | Esecuzioni non pipeline. |
| PipelineRun | Un'esecuzione della pipeline, che è l'elemento padre di un StepRun. |
| StepRun (Esegui fase) | Un'esecuzione per un passaggio della pipeline. |
| ReusedStepRun | Un'esecuzione per un passaggio della pipeline che riutilizza un'esecuzione precedente. |

## <a name="see-also"></a>Vedere anche

- Vedere Monitoraggio di Azure Machine Learning per una descrizione del monitoraggio di Azure Machine Learning.See [Monitoring Azure Machine Learning](monitor-azure-machine-learning.md) for a description of monitoring Azure Machine Learning.
- Per informazioni dettagliate sul monitoraggio delle risorse di Azure, vedere Monitoraggio delle risorse di [Azure con Monitoraggio di Azure.See Monitoring Azure resources with Azure Monitor](/azure/azure-monitor/insights/monitor-azure-resource) for details on monitoring Azure resources.
