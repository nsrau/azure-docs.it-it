---
title: Domande frequenti relative ad Azure Kubernetes Service
description: Risposte ad alcune domande comuni su Azure Kubernetes Service.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/27/2018
ms.author: iainfou
ms.openlocfilehash: b64c770bca84fba8cbed98e420abf649897f7a17
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39345855"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative ad Azure Kubernetes Service (AKS)

Questo articolo tratta alcune domande frequenti relative ad Azure Kubernetes Service (AKS).

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quali aree di Azure forniscono Azure Kubernetes Service (AKS) oggi?

Vedere la documentazione di Azure Kubernetes Service relativa ad [aree e disponibilità][aks-regions] per un elenco completo.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente AKS?

Azure applica automaticamente le patch di sicurezza ai nodi del cluster con una pianificazione notturna. Tuttavia l'utente è responsabile di garantire che i nodi vengano riavviati come richiesto. Sono disponibili diverse opzioni per eseguire il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster AKS. Gli aggiornamenti del cluster [bloccano e svuotano i nodi](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) automaticamente, quindi li riattivano con l'immagine Ubuntu più recente. Aggiornare l'immagine del sistema operativo nei nodi senza modificare le versioni Kubernetes specificando la versione corrente del cluster in `az aks upgrade`.
- Usando [Kured](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. Kured viene eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora ogni nodo per verificare se è presente un file che indichi che è necessario un riavvio. Quindi gestisce i riavvii del sistema operativo in tutto il cluster, seguendo lo stesso processo di blocco e svuotamento descritto in precedenza.

## <a name="does-aks-support-node-autoscaling"></a>AKS supporta la scalabilità automatica dei nodi?

Sì, la scalabilità automatica è disponibile tramite il [Ridimensionamento automatico di Kubernetes][auto-scaler] a partire da Kubernetes 1.10.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS supporta il controllo degli accessi in base al ruolo di Kubernetes?

Sì, RBAC può essere abilitato durante la [distribuzione di un cluster AKS dall'interfaccia della riga di comando di Azure o dal modello di Azure Resource Manager](https://docs.microsoft.com/en-us/azure/aks/aad-integration). Questa funzionalità sarà a breve disponibile sul portale di Azure.

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quali controller di ammissione Kubernetes supporta AKS? È possibile aggiungere o rimuovere i controller di ammissione?

AKS supporta i seguenti [controller di ammissione][admission-controllers]:

* NamespaceLifecycle
* LimitRanger
* ServiceAccount
* DefaultStorageClass
* DefaultTolerationSeconds
* MutatingAdmissionWebhook
* ValidatingAdmissionWebhook
* ResourceQuota
* DenyEscalatingExec
* AlwaysPullImages

Non è attualmente possibile modificare l'elenco di controller di ammissione in AKS.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>È possibile distribuire AKS nella rete virtuale esistente?

Sì, è possibile distribuire un cluster AKS in una rete virtuale esistente tramite la [funzione di retee virtuale](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/aks/networking-overview.md).

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>È possibile limitare il server API Kubernetes in modo che sia accessibile solo all'interno della rete virtuale?

Attualmente non è possibile. Il server API Kubernetes viene esposto come nome di dominio pubblico completo (FQDN). È consigliabile controllare l'accesso al cluster tramite il [controllo degli accessi in base al ruolo (RBAC) di Kubernetes e Azure Active Directory (AAD)](https://docs.microsoft.com/en-us/azure/aks/aad-integration).

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in AKS?

AKS non è attualmente integrato nell'insieme di credenziali delle chiavi di Azure in modo nativo. Tuttavia, il progetto [KeyVault Flex Volume](https://github.com/Azure/kubernetes-keyvault-flexvol) consente l'integrazione diretta dai pod di Kubernetes ai segreti di KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in AKS?

Per eseguire i contenitori di Windows Server, è necessario eseguire nodi basati su Windows Server. I nodi basati su server di Windows non sono attualmente disponibili in AKS. È tuttavia possibile, usare Virtual Kubelet per pianificare i contenitori di Windows per le istanze di contenitore di Azure e gestirli come parte del cluster AKS. Per altre informazioni, vedere [Usare Virtual Kubelet con AKS][virtual-kubelet].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con AKS?

Ogni distribuzione AKS si estende a due gruppi di risorse. Il primo viene creato dall'utente e contiene solo la risorsa del servizio Kubernetes. Il provider di risorse AKS crea automaticamente il secondo durante la distribuzione con un nome come *MC_myResourceGroup_myAKSCluster_eastus*. Il secondo gruppo di risorse contiene tutte le risorse di infrastruttura associate al cluster, ad esempio le VM, le risorse di rete e di archiviazione. Viene creato per semplificare la pulitura delle risorse.

Se si creano risorse che verranno usate con il cluster AKS, ad esempio account di archiviazione o gli indirizzi IP pubblici riservati, è necessario inserirle nel gruppo di risorse generato automaticamente.

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS offre un contratto di servizio?

In un contratto di servizio il provider si impegna a rimborsare il cliente per il costo del servizio, se il livello di servizio pubblicato non viene soddisfatto. Poiché AKS è gratuito, non è esiste un costo da rimborsare e quindi non esiste un contratto di servizio formale. Tuttavia l'obiettivo è quello di mantenere una disponibilità almeno del 99,5% per il server API Kubernetes.

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md
[virtual-kubelet]: virtual-kubelet.md

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
