---
title: Configurare il monitoraggio GPU con Monitoraggio di Azure per i contenitori. Documenti Microsoft
description: Questo articolo descrive come configurare il monitoraggio dei cluster Kubernetes con i nodi abilitati per NVIDIA e AMD GPU con Monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 958f5ab33edcd280f5673391eba907728f1153c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373310"
---
# <a name="configure-gpu-monitoring-with-azure-monitor-for-containers"></a>Configurare il monitoraggio GPU con Monitoraggio di Azure per i contenitoriConfigure GPU monitoring with Azure Monitor for containers

A partire dalla versione dell'agente *ciprod03022019*, il monitoraggio di Azure per gli agenti integrati nei contenitori supporta ora il monitoraggio dell'utilizzo della GPU (unità di elaborazione grafica) nei nodi del cluster in grado di riconoscere GPU e monitorare pod/contenitori che richiedono e usano le risorse GPU.

## <a name="supported-gpu-vendors"></a>Fornitori GPU supportati

Monitoraggio di Azure per contenitori supporta il monitoraggio dei cluster GPU dai fornitori GPU seguenti:Azure Monitor for Containers supports monitoring GPU clusters from following GPU vendors:

- [Nvidia](https://developer.nvidia.com/kubernetes-gpu)

- [Amd](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Monitor di Azure per i contenitori avvia automaticamente il monitoraggio dell'utilizzo della GPU nei nodi e la GPU richiede pod e carichi di lavoro raccogliendo le metriche seguenti a intervalli di 60 secondi e archiviandole nella tabella **InsightMetrics:**

|Nome metrica |Dimensione metrica (tag) |Descrizione |
|------------|------------------------|------------|
|ContainerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Percentuale di tempo nell'ultimo periodo di campionamento (60 secondi) durante il quale la GPU è stata occupata/elaborazione attiva per un contenitore. Il ciclo di lavoro è un numero compreso tra 1 e 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, nomecontenitore |Ogni contenitore può specificare i limiti come una o più GPU. Non è possibile richiedere o limitare una frazione di una GPU. |
|containerGpuRequests (Richieste containerGpu |container.azm.ms/clusterId, container.azm.ms/clusterName, nomecontenitore |Ogni contenitore può richiedere una o più GPU. Non è possibile richiedere o limitare una frazione di una GPU.|
|ContainerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantità di memoria GPU in byte disponibili per l'utilizzo per un contenitore specifico. |
|Byte containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Quantità di memoria GPU in byte utilizzata da un contenitore specifico. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Numero di GPU in un nodo che possono essere utilizzate da Kubernetes. |
|nodeGpuCapacity (Server di nodo) |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Numero totale di GPU in un nodo. |

## <a name="gpu-performance-charts"></a>Grafici delle prestazioni GPU 

Monitoraggio di Azure per i contenitori include grafici preconfigurati per le metriche elencate in precedenza nella tabella come cartella di lavoro GPU per ogni cluster. È possibile trovare la **GPU nodo** della cartella di lavoro GPU direttamente da un cluster AKS selezionando **Cartelle di** lavoro dal riquadro di sinistra e dall'elenco a discesa Visualizza cartelle **di lavoro** in Informazioni dettagliate.

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Usare GPU per carichi di lavoro ad alta intensità di calcolo nel servizio Azure Kubernetes](../../aks/gpu-cluster.md) (AKS) per informazioni su come distribuire un cluster AKS che include nodi abilitati per GPU.

- Altre informazioni sugli [SKU delle macchine virtuali ottimizzate GPU in Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Consulta il [supporto GPU in Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) per ulteriori informazioni sul supporto sperimentale di Kubernetes per la gestione delle GPU in uno o più nodi in un cluster.