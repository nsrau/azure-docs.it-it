---
title: USA nodi virtuali
titleSuffix: Azure Kubernetes Service
description: Panoramica dell'uso di un nodo virtuale con i servizi Kubernetes di Azure (AKS)
services: container-service
ms.topic: conceptual
ms.date: 09/21/2020
ms.custom: references_regions
ms.openlocfilehash: 2dd91e5c506f229d653fdf98bc0549c173cec793
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351888"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Creare e configurare un cluster di servizi Kubernetes di Azure per l'uso di nodi virtuali

Per ridimensionare rapidamente i carichi di lavoro dell'applicazione in un cluster AKS, è possibile usare i nodi virtuali. I nodi virtuali consentono di effettuare rapidamente il provisioning dei pod applicando al tempo di esecuzione una tariffa al secondo. Non è necessario attendere che la funzione di scalabilità automatica del cluster del servizio Azure Kubernetes distribuisca i nodi di calcolo delle macchine virtuali per eseguire pod aggiuntivi. I nodi virtuali sono supportati solo con i pod e i nodi Linux.

Il componente aggiuntivo dei nodi virtuali per AKS si basa sul progetto open source [Virtual Kubelet][virtual-kubelet-repo].

Questo articolo fornisce una panoramica dei requisiti di rete e di disponibilità dell'area per l'uso di nodi virtuali, oltre a limitazioni note.

## <a name="regional-availability"></a>Disponibilità a livello di area

Tutte le aree, dove ACI supporta gli SKU VNET, sono supportate per le distribuzioni di nodi virtuali.

Per gli SKU di CPU e memoria disponibili in ogni area, controllare la [disponibilità delle risorse di istanze di contenitore di Azure per istanze di contenitore di Azure in aree di Azure-gruppi di contenitori Linux](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Requisiti di rete

I nodi virtuali abilitano la comunicazione di rete tra i pod eseguiti in Istanze di Azure Container e nel cluster del servizio Azure Kubernetes. Per consentire la comunicazione viene creata una subnet di rete virtuale e vengono assegnate autorizzazioni delegate. I nodi virtuali funzionano solo con i cluster AKS creati usando *Advanced* Networking (Azure CNI). Per impostazione predefinita, i cluster AKS vengono creati con la rete di *base* (kubenet).

Per configurare la rete, i pod in esecuzione in istanze di contenitore di Azure (ACI) devono accedere all'endpoint del server API AKS.

## <a name="known-limitations"></a>Limitazioni note

La funzionalità dei nodi virtuali dipende molto dal set di funzionalità di Istanze di Azure Container. Oltre alle [quote e ai limiti per le istanze di contenitore di Azure](../container-instances/container-instances-quotas.md), gli scenari seguenti non sono ancora supportati con i nodi virtuali:

* Uso dell'entità servizio per il pull delle immagini di Registro Azure Container. La [soluzione alternativa](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) prevede l'uso di [segreti Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitazioni della rete virtuale](../container-instances/container-instances-vnet.md) inclusi il peering VNet, i criteri di rete Kubernetes e il traffico in uscita verso Internet con gruppi di sicurezza di rete.
* Contenitori init
* [Alias host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argomenti](../container-instances/container-instances-exec.md#restrictions) per exec in Istanze di Azure Container
* [Gli elementi daemonset](concepts-clusters-workloads.md#statefulsets-and-daemonsets) non distribuirà i pod nei nodi virtuali
* I nodi virtuali supportano la pianificazione di pod Linux. È possibile installare manualmente il provider open source [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) per pianificare i contenitori di Windows Server in Istanze di Azure Container.
* I nodi virtuali richiedono cluster AKS con rete CNI di Azure.
* Nodi virtuali con cluster privati.
* Uso degli intervalli IP autorizzati del server API per AKS.
* Montaggio del volume File di Azure condivisione supporto per [utilizzo generico V1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Seguire le istruzioni per montare [un volume con file di Azure condivisione](azure-files-volume.md)

## <a name="next-steps"></a>Passaggi successivi

Configurare i nodi virtuali per i cluster:

- [Creare nodi virtuali usando l'interfaccia della riga di comando di Azure](virtual-nodes-cli.md)
- [Creare nodi virtuali usando il portale nel servizio Azure Kubernetes (AKS)](virtual-nodes-portal.md)

I nodi virtuali sono spesso un componente di una soluzione di scalabilità nel servizio Azure Kubernetes. Per altre informazioni sulle soluzioni di scalabilità, vedere gli articoli seguenti:

- [Usare il ridimensionamento automatico orizzontale dei pod di Kubernetes][aks-hpa]
- [Usare ridimensionamento automatico del cluster di Kubernetes][aks-cluster-autoscaler]
- [Vedere l'esempio di scalabilità automatica per i nodi virtuali][virtual-node-autoscale]
- [Leggere altre informazioni sulla libreria open source Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
