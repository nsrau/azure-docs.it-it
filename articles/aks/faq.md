---
title: Domande frequenti relative al servizio Azure Kubernetes
description: Risposte ad alcune domande comuni sul servizio Azure Kubernetes.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: ae92a5c894b186a1c8b471c1b446a88299742aec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60466376"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative al servizio Azure Kubernetes

Questo articolo tratta alcune domande frequenti relative al servizio Azure Kubernetes.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quali aree di Azure forniscono il servizio Azure Kubernetes oggi?

Per un elenco completo delle aree disponibili, vedere [Aree del servizio Azure Kubernetes e disponibilità][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>servizio Azure Kubernetes supporta la scalabilità automatica dei nodi?

Sì, la scalabilità automatica è disponibile tramite il [Ridimensionamento automatico di Kubernetes][auto-scaler] a partire da Kubernetes 1.10. Per altre informazioni su come configurare e usare la scalabilità automatica del cluster, vedere [Scalabilità automatica del cluster nel servizio Azure Kubernetes][aks-cluster-autoscale].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>servizio Azure Kubernetes supporta il controllo degli accessi in base al ruolo di Kubernetes?

Sì, Kubernetes RBAC è abilitata per impostazione predefinita quando i cluster vengono creati con l'interfaccia della riga di comando di Azure. RBAC può essere abilitata per i cluster creati usando il portale o i modelli di Azure.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>È possibile distribuire servizio Azure Kubernetes nella rete virtuale esistente?

Sì, è possibile distribuire un cluster servizio Azure Kubernetes in una rete virtuale esistente tramite la [funzione di retee virtuale][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>È possibile limitare il server API Kubernetes in modo che sia accessibile solo all'interno della rete virtuale?

Attualmente non è possibile. Il server API Kubernetes viene esposto come nome di dominio pubblico completo (FQDN). È possibile controllare l'accesso al cluster tramite il [controllo degli accessi in base al ruolo (RBAC) di Kubernetes e Azure Active Directory (AAD)][aks-rbac-aad]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente servizio Azure Kubernetes?

Sì, Azure applica automaticamente le patch di sicurezza ai nodi del cluster con una pianificazione notturna. Tuttavia l'utente è responsabile di garantire che i nodi vengano riavviati come richiesto. Sono disponibili diverse opzioni per eseguire il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster servizio Azure Kubernetes. Gli aggiornamenti del cluster [bloccano e svuotano automaticamente i nodi][cordon-drain], quindi eseguono il backup di ogni nodo con l'immagine Ubuntu più recente e una nuova versione della patch o una versione precedente di Kubernetes. Per altre informazioni, vedere [Aggiornare un cluster del servizio Azure Kubernetes][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. Kured viene eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora ogni nodo per verificare se è presente un file che indichi che è necessario un riavvio. I riavvii del sistema operativo sono gestiti all'interno del cluster usando lo stesso [processo di blocco e svuotamento][cordon-drain] come aggiornamento del cluster.

Per altre informazioni sull'utilizzo di Kured, vedere [Apply security and kernel updates to nodes in AKS][node-updates-kured] (Applicare aggiornamenti di sicurezza e del kernel ai nodi di ASK).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con servizio Azure Kubernetes?

Ogni distribuzione servizio Azure Kubernetes si estende a due gruppi di risorse:

- Il primo gruppo di risorse viene creato dall'utente e contiene solo la risorsa del servizio Kubernetes. Il provider di risorse servizio Azure Kubernetes crea automaticamente il secondo durante la distribuzione, come ad esempio *MC_myResourceGroup_myservizio Azure KubernetesCluster_eastus*.
- Il secondo gruppo di risorse, come ad esempio *MC_myResourceGroup_myAKSCluster_eastus*, contiene tutte le risorse di infrastruttura associate al cluster, come ad esempio le macchine virtuali dei nodi Kubernetes, le risorse della rete virtuale e di archiviazione. Questo gruppo di risorse separato viene creato per semplificare la pulizia delle risorse.

Se si creano risorse che verranno usate con il cluster servizio Azure Kubernetes, ad esempio account di archiviazione o indirizzi IP pubblici riservati, è necessario inserirle nel gruppo di risorse generato automaticamente.

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>È possibile modificare i tag e le altre proprietà delle risorse del servizio Azure Kubernetes nel gruppo di risorse MC_*?

La modifica e l'eliminazione di tag creati da Azure e altre proprietà delle risorse nel gruppo di risorse *MC_** può causare risultati imprevisti, ad esempio un ridimensionamento o errori di aggiornamento. È supportata la creazione e la modifica di tag personalizzati aggiuntivi, ad esempio per assegnare una Business Unit o un centro di costo. La modifica delle risorse nel gruppo *MC_** del cluster del servizio Azure Kubernetes è una violazione dell'obiettivo del livello di servizio (SLO). Per altre informazioni, vedere [Servizio Azure Kubernetes offre un contratto di servizio?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta servizio Azure Kubernetes? È possibile aggiungere o rimuovere i controller di ammissione?

servizio Azure Kubernetes supporta i seguenti [controller di ammissione][admission-controllers]:

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

Non è attualmente possibile modificare l'elenco di controller di ammissione in servizio Azure Kubernetes.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in servizio Azure Kubernetes?

servizio Azure Kubernetes non è attualmente integrato nell'insieme di credenziali delle chiavi di Azure in modo nativo. Tuttavia, il progetto [Azure Key Vault FlexVolume for Kubernetes][keyvault-flexvolume] consente l'integrazione diretta dai pod di Kubernetes ai segreti di KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in servizio Azure Kubernetes?

Per eseguire i contenitori di Windows Server, è necessario eseguire nodi basati su Windows Server. I nodi basati su server di Windows non sono attualmente disponibili in servizio Azure Kubernetes. È tuttavia possibile usare Virtual Kubelet per pianificare i contenitori di Windows per Istanze di Azure Container e gestirli come parte del cluster del servizio Azure Kubernetes. Per altre informazioni, vedere [Usare Virtual Kubelet con servizio Azure Kubernetes][virtual-kubelet].

## <a name="does-aks-offer-a-service-level-agreement"></a>servizio Azure Kubernetes offre un contratto di servizio?

In un contratto di servizio il provider si impegna a rimborsare il cliente per il costo del servizio, se il livello di servizio pubblicato non viene soddisfatto. Poiché servizio Azure Kubernetes è gratuito, non è esiste un costo da rimborsare e quindi non esiste un contratto di servizio formale. Tuttavia l'obiettivo è quello di mantenere una disponibilità almeno del 99,5% per il server API Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol

