---
title: Panoramica di Kubernetes con abilitazione di Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Questo articolo offre una panoramica di Kubernetes con abilitazione di per Azure Arc.
keywords: Kubernetes, Arc, Azure, contenitori
ms.custom: references_regions
ms.openlocfilehash: 601fcb096bf2bfeb1a6120015690c9aff4b6177b
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216080"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Che cos'è Kubernetes con abilitazione di Azure Arc (anteprima)?

È possibile collegare e configurare cluster Kubernetes all'interno o all'esterno di Azure con Kubernetes con abilitazione di Azure Arc (anteprima). Quando un cluster Kubernetes viene collegato ad Azure Arc, viene visualizzato nel portale di Azure. Avrà un ID Azure Resource Manager e un'identità gestita. I cluster sono collegati alle sottoscrizioni standard di Azure, si trovano in un gruppo di risorse e possono ricevere tag esattamente come qualsiasi altra risorsa di Azure. 

Per connettere un cluster Kubernetes ad Azure, l'amministratore del cluster deve distribuire gli agenti. Questi agenti vengono eseguiti in uno spazio dei nomi Kubernetes denominato `azure-arc` e sono distribuzioni Kubernetes standard. Gli agenti sono responsabili della connettività ad Azure, della raccolta di metriche e dei log di Azure Arc e del controllo delle richieste di configurazione. 

Kubernetes con abilitazione di Azure Arc supporta il protocollo SSL standard di settore per proteggere i dati in transito. Inoltre, i dati vengono archiviati crittografati quando sono inattivi in un database di Azure Cosmos DB per garantirne la riservatezza.
 
> [!NOTE]
> Kubernetes con abilitazione di Azure Arc è in versione di anteprima. Non è consigliabile usarlo per carichi di lavoro di produzione.

## <a name="supported-kubernetes-distributions"></a>Distribuzioni di Kubernetes supportate

Kubernetes con abilitazione di Azure Arc è compatibile con qualsiasi cluster Kubernetes con certificazione CNCF (Cloud Native Computing Foundation), ad esempio il motore del servizio Azure Kubernetes in Azure, il motore del servizio Azure Kubernetes nell'hub di Azure Stack, GKE, EKS e il cluster VMware vSphere.

Le funzionalità di Kubernetes con abilitazione di Azure Arc sono state testate dal team Arc sulle distribuzioni seguenti:
* RedHat OpenShift 4.3
* Rancher RKE 1.0.8
* Canonical Charmed Kubernetes 1.18
* Azure Kubernetes Engine
* Motore del servizio Azure Kubernetes nell'hub di Azure Stack
* Azure del provider API Cluster

## <a name="supported-scenarios"></a>Scenari supportati 

Kubernetes con abilitazione di Azure Arc supporta questi scenari: 

* Connessione di Kubernetes in esecuzione all'esterno di Azure per l'inventario, il raggruppamento e l'assegnazione di tag.

* Distribuzione di applicazioni e applicazione della configurazione tramite la gestione della configurazione basata su GitOps. 

* Uso di Monitoraggio di Azure per i contenitori per visualizzare e monitorare i cluster. 

* Applicazione dei criteri tramite Criteri di Azure per Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Aree supportate 

Kubernetes con abilitazione di Azure Arc è attualmente supportato in queste aree: 

* Stati Uniti orientali 
* Europa occidentale

## <a name="frequently-asked-questions"></a>Domande frequenti

* Qual è la differenza tra Kubernetes con abilitazione di Azure Arc e il servizio Azure Kubernetes?

    Il servizio Azure Kubernetes è l'offerta di ambiente Kubernetes gestito di Azure. Semplifica la distribuzione di un cluster Kubernetes gestito in Azure. servizio Azure Kubernetes riduce la complessità e i costi operativi di gestione di Kubernetes, affidando la maggior parte di queste attività ad Azure. I master di Kubernetes sono gestiti da Azure. È possibile gestire solo i nodi agente.

    Kubernetes con abilitazione di Azure Arc consente di connettere i cluster Kubernetes ad Azure per estendere funzionalità di gestione di Azure come Monitoraggio di Azure e Criteri di Azure. La manutenzione del cluster Kubernetes sottostante è a carico dell'utente.

* È necessario connettere ad Azure Arc i cluster del servizio Azure Kubernetes in esecuzione in Azure?

    No. Tutte le funzionalità di Kubernetes con abilitazione di Azure Arc, come Monitoraggio di Azure e Criteri di Azure (Gatekeeper) sono disponibili in modalità nativa nel servizio Azure Kubernetes, che include già una rappresentazione di risorse in Azure.
    
* È necessario connettere ad Azure Arc il cluster del servizio Azure Kubernetes in Azure Stack HCI? E lo stesso vale per i cluster Kubernetes in esecuzione nell'hub di Azure Stack o in Azure Stack Edge?

    Sì, la connessione di questi cluster ad Azure Arc presenta dei vantaggi. Fornisce una rappresentazione delle risorse per questi cluster Kubernetes in Azure Resource Manager. Usando questa rappresentazione delle risorse, è possibile estendere a questi cluster funzionalità come la configurazione del cluster, Monitoraggio di Azure, Criteri di Azure (Gatekeeper)

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster](./connect-cluster.md)
