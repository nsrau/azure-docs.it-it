---
title: Domande frequenti per Azure Red Hat OpenShift | Microsoft Docs
description: Di seguito vengono fornite le risposte alle domande frequenti su Microsoft Azure Red Hat OpenShift
services: container-service
author: tylermsft
ms.author: twhitney
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 77e0e11582808901b10877d0d9284637145aa6f2
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65078671"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift, domande frequenti

Questo articolo risponde alle domande frequenti (FAQ) su Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Quali aree di Azure sono supportati?

Visualizzare [risorse supportate](supported-resources.md#azure-regions) per un elenco di aree globali in cui è supportato Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>È possibile distribuire un cluster in una rete virtuale esistente?

Sì. Quando si crea un cluster, è possibile distribuire un cluster Azure Red Hat OpenShift in una rete virtuale esistente. Vedere le [connettere la rete virtuale di un cluster a una rete virtuale esistente ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) per informazioni dettagliate.

## <a name="what-cluster-operations-are-available"></a>Le operazioni di cluster sono disponibili?

È possibile solo aumentare o ridurre il numero di nodi di calcolo. Nessuna altra modifica è autorizzata al `Microsoft.ContainerService/openShiftManagedClusters` risorsa dopo la creazione. Il numero massimo di nodi di calcolo è limitato a 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Le dimensioni delle macchine virtuali è possibile usare?

Visualizzare [dimensioni delle macchine virtuali di Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) per un elenco delle dimensioni delle macchine virtuali è possibile usare con un cluster Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Sia i dati sul cluster crittografati?

Per impostazione predefinita, è presente crittografia dei dati inattivi. La piattaforma di archiviazione di Azure crittografa automaticamente i dati prima di renderli persistenti e consente di decrittografare i dati prima del recupero. Visualizzare [la crittografia del servizio di archiviazione di Azure per dati inattivi](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) per informazioni dettagliate.

## <a name="can-i-use-prometheusgrafana-to-monitor-containers-and-manage-capacity"></a>È possibile usare Prometheus/Grafana per monitorare i contenitori e gestire la capacità?

No, non l'ora corrente.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>È il registro Docker disponibili esternamente in modo che è possibile usare strumenti come Jenkins?

È disponibile il registro Docker `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` , tuttavia, una garanzia di durabilità di archiviazione sicuro non viene specificata. È anche possibile usare [registro contenitori di Azure](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Rete cross-spazio dei nomi è supportata?

Clienti e gli amministratori di singolo progetto personalizzabili rete cross-spazio dei nomi (tra cui viene negato) in base al progetto usando `NetworkPolicy` oggetti.

## <a name="can-an-admin-manage-users-and-quotas"></a>Un amministratore può gestire quote e gli utenti?

Sì. Un amministratore di Azure Red Hat OpenShift può gestire utenti e le quote oltre all'accesso a tutti gli utenti creati progetti.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>È possibile limitare un cluster solo determinati utenti di Azure AD?

Sì. È possibile limitare quali Azure AD gli utenti possono accedere a un cluster configurando l'applicazione Azure AD. Per informazioni dettagliate, vedere [come: Limitare l'app a un set di utenti](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Un cluster possono avere nodi di calcolo in più aree di Azure?

 No. Tutti i nodi in un cluster Azure Red Hat OpenShift devono provenire dalla stessa area di Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Sono i nodi master e l'infrastruttura speditamente così come sono con Azure Kubernetes Service (AKS)?

 No. Tutte le risorse, incluso il database master del cluster, eseguire nella propria sottoscrizione dei clienti. Questi tipi di risorse vengono inseriti in un gruppo di risorse di sola lettura.
