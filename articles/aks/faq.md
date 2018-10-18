---
title: Domande frequenti relative al servizio Kubernetes di Azure
description: Risposte ad alcune domande comuni sul servizio Kubernetes di Azure.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: 1e101e308ec350e9900c1347da730ca02b16c7bb
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49377467"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative ad Azure Kubernetes Service (AKS)

Questo articolo tratta alcune domande frequenti relative ad Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quali aree di Azure forniscono Azure Kubernetes Service (AKS) oggi?

Per un elenco completo delle aree disponibili, vedere [Aree del servizio Kubernetes di Azure e disponibilità][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>AKS supporta la scalabilità automatica dei nodi?

Sì, la scalabilità automatica è disponibile tramite il [Ridimensionamento automatico di Kubernetes][auto-scaler] a partire da Kubernetes 1.10. Per altre informazioni su come configurare e usare la scalabilità automatica del cluster, vedere [Scalabilità automatica del cluster nel servizio Kubernetes di Azure][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS supporta il controllo degli accessi in base al ruolo di Kubernetes?

Sì, Kubernetes RBAC è abilitata per impostazione predefinita quando i cluster vengono creati con l'interfaccia della riga di comando di Azure. RBAC può essere abilitata per i cluster creati usando il portale o i modelli di Azure.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>È possibile distribuire AKS nella rete virtuale esistente?

Sì, è possibile distribuire un cluster AKS in una rete virtuale esistente tramite la [funzione di retee virtuale][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>È possibile limitare il server API Kubernetes in modo che sia accessibile solo all'interno della rete virtuale?

Attualmente non è possibile. Il server API Kubernetes viene esposto come nome di dominio pubblico completo (FQDN). È possibile controllare l'accesso al cluster tramite il [controllo degli accessi in base al ruolo (RBAC) di Kubernetes e Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente AKS?

Sì, Azure applica automaticamente le patch di sicurezza ai nodi del cluster con una pianificazione notturna. Tuttavia l'utente è responsabile di garantire che i nodi vengano riavviati come richiesto. Sono disponibili diverse opzioni per eseguire il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster AKS. Gli aggiornamenti del cluster [bloccano e svuotano automaticamente i nodi][cordon-drain], quindi eseguono il backup di ogni nodo con l'immagine Ubuntu più recente e una nuova versione della patch o una versione precedente di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Kubernetes di Azure][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. Kured viene eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora ogni nodo per verificare se è presente un file che indichi che è necessario un riavvio. I riavvii del sistema operativo sono gestiti all'interno del cluster usando lo stesso [processo di blocco e svuotamento][cordon-drain] come aggiornamento del cluster.

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con AKS?

Ogni distribuzione AKS si estende a due gruppi di risorse:

- Il primo gruppo di risorse viene creato dall'utente e contiene solo la risorsa del servizio Kubernetes. Il provider di risorse AKS crea automaticamente il secondo durante la distribuzione, come ad esempio *MC_myResourceGroup_myAKSCluster_eastus*.
- Il secondo gruppo di risorse, come ad esempio *MC_myResourceGroup_myAKSCluster_eastus*, contiene tutte le risorse di infrastruttura associate al cluster, come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Questo gruppo di risorse separato viene creato per semplificare la pulizia delle risorse.

Se si creano risorse che verranno usate con il cluster AKS, ad esempio account di archiviazione o indirizzi IP pubblici riservati, è necessario inserirle nel gruppo di risorse generato automaticamente.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>È possibile modificare i tag e le altre proprietà delle risorse del servizio Kubernetes di Azure nel gruppo di risorse MC_*?

La modifica e l'eliminazione di tag creati da Azure e altre proprietà delle risorse nel gruppo di risorse *MC_** può causare risultati imprevisti, ad esempio un ridimensionamento o errori di aggiornamento. È supportata la creazione e la modifica di tag personalizzati aggiuntivi, ad esempio per assegnare una Business Unit o un centro di costo. La modifica delle risorse nel gruppo *MC_** del cluster del servizio Kubernetes di Azure è una violazione dell'obiettivo del livello di servizio (SLO).

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta AKS? È possibile aggiungere o rimuovere i controller di ammissione?

AKS supporta i seguenti [controller di ammissione][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

Non è attualmente possibile modificare l'elenco di controller di ammissione in AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in AKS?

AKS non è attualmente integrato nell'insieme di credenziali delle chiavi di Azure in modo nativo. Tuttavia, il progetto [Azure Key Vault FlexVolume for Kubernetes][keyvault-flexvolume] consente l'integrazione diretta dai pod di Kubernetes ai segreti di KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in AKS?

Per eseguire i contenitori di Windows Server, è necessario eseguire nodi basati su Windows Server. I nodi basati su server di Windows non sono attualmente disponibili in AKS. È tuttavia possibile, usare Virtual Kubelet per pianificare i contenitori di Windows per le istanze di contenitore di Azure e gestirli come parte del cluster AKS. Per altre informazioni, vedere [Usare Virtual Kubelet con AKS][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre un contratto di servizio?

In un contratto di servizio il provider si impegna a rimborsare il cliente per il costo del servizio, se il livello di servizio pubblicato non viene soddisfatto. Poiché AKS è gratuito, non è esiste un costo da rimborsare e quindi non esiste un contratto di servizio formale. Tuttavia l'obiettivo è quello di mantenere una disponibilità almeno del 99,5% per il server API Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-advanced-networking.md
[aks-rbac-aad]: ./aad-integration.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
