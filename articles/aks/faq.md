---
title: Domande frequenti relative ad Azure Kubernetes Service
description: Risposte ad alcune domande comuni su Azure Kubernetes Service.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/14/2018
ms.author: nepeters
ms.openlocfilehash: 55006a3f0193c96849c52f87ab01dc13ac0c7a16
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2018
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Domande frequenti relative ad Azure Kubernetes Service (AKS)

Questo articolo tratta alcune domande frequenti relative ad Azure Kubernetes Service (AKS).

> [!IMPORTANT]
> Azure Kubernetes Service (AKS) è attualmente disponibile in **anteprima**. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>Quali aree di Azure forniscono Azure Kubernetes Service (AKS) oggi?

- Canada centrale
- Canada orientale
- Stati Uniti centrali
- Stati Uniti orientali
- Asia sudorientale
- Europa occidentale
- Stati Uniti occidentali 2

## <a name="when-will-additional-regions-be-added"></a>Quando verranno aggiunte altre aree?

Vengono aggiunte altre aree man mano che la richiesta aumenta.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi agente AKS?

Azure applica automaticamente le patch di sicurezza ai nodi del cluster con una pianificazione notturna. Tuttavia l'utente è responsabile di garantire che i nodi vengano riavviati come richiesto. Sono disponibili diverse opzioni per eseguire il riavvio dei nodi:

- Manualmente tramite il portale di Azure o l'interfaccia della riga di comando di Azure.
- Aggiornando il cluster AKS. Gli aggiornamenti del cluster [bloccano e svuotano i nodi](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) automaticamente, quindi li riattivano con l'immagine Ubuntu più recente. Aggiornare l'immagine del sistema operativo nei nodi senza modificare le versioni Kubernetes specificando la versione corrente del cluster in `az aks upgrade`.
- Usando [Kured](https://github.com/weaveworks/kured), un daemon di riavvio open source per Kubernetes. Kured viene eseguito come [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora ogni nodo per verificare se è presente un file che indichi che è necessario un riavvio. Quindi orchestra i riavvii in tutto il cluster, seguendo lo stesso processo di blocco e svuotamento descritto in precedenza.

## <a name="do-you-recommend-customers-use-acs-or-aks"></a>È preferibile usare ACS o AKS?

Mentre AKS è in fase di anteprima, si consiglia di creare i cluster di produzione con ACS-Kubernetes o [acs-engine](https://github.com/azure/acs-engine). Usare AKS per le distribuzioni di modelli di verifica e gli ambienti di sviluppo e test.

## <a name="when-will-acs-be-deprecated"></a>Da quando ACS sarà deprecato?

ACS diventerà deprecato più o meno in corrispondenza della disponibilità generale di AKS. Dopo tale data saranno disponibili 12 mesi per eseguire la migrazione dei cluster ad AKS. Durante il periodo di 12 mesi, è possibile eseguire tutte le operazioni di ACS.

## <a name="does-aks-support-node-autoscaling"></a>AKS supporta la scalabilità automatica dei nodi?

La scalabilità automatica dei nodi non è supportata, ma lo sarà in futuro. È possibile esaminare questa [implementazione della scalabilità automatica][auto-scaler] open source.

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS supporta il controllo degli accessi in base al ruolo di Kubernetes?

No, il controllo degli accessi in base al ruolo non è attualmente supportato in AKS, ma sarà presto disponibile.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>È possibile distribuire AKS nella rete virtuale esistente?

No, questa possibilità non è ancora disponibile ma lo sarà presto.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in AKS?

No, non lo è, ma l'integrazione è pianificata per il futuro. Nel frattempo, provare la soluzione seguente di [Hexadite][hexadite].

## <a name="can-i-run-windows-server-containers-on-aks"></a>È possibile eseguire contenitori Windows Server in AKS?

No, AKS non fornisce attualmente nodi agente basati su Windows Server, pertanto non è possibile eseguire contenitori Windows Server. Se è necessario eseguire contenitori Windows Server in Kubernetes in Azure, vedere la [documentazione per acs-engine](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/windows.md).

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con AKS?

Ogni distribuzione AKS si estende a due gruppi di risorse. Il primo viene creato dall'utente e contiene solo la risorsa AKS. Il provider di risorse AKS crea automaticamente il secondo durante la distribuzione con un nome come *MC_myResourceGRoup_myAKSCluster_eastus*. Il secondo gruppo di risorse contiene tutte le risorse di infrastruttura associate al cluster, ad esempio le VM, le risorse di rete e di archiviazione. Viene creato per semplificare la pulitura delle risorse.

Se si creano risorse che verranno usate con il cluster AKS, ad esempio account di archiviazione o un indirizzo IP pubblico riservato, è necessario inserirle nel gruppo di risorse generato automaticamente.

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent