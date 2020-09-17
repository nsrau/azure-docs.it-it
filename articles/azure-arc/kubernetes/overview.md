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
ms.openlocfilehash: fb8a7b7c2e1e5b3de7d1ccdb4054e44825231458
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604802"
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


## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster](./connect-cluster.md)
