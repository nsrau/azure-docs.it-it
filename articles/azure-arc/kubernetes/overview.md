---
title: Panoramica di Kubernetes abilitato per Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Questo articolo offre una panoramica di Kubernetes abilitato per Azure Arc.
keywords: Kubernetes, Arc, Azure, contenitori
ms.custom: references_regions
ms.openlocfilehash: 4f07c0e5e01648984514701cd4838f85478d86af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87050063"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Che cos'è Kubernetes abilitato per Azure Arc (anteprima)?

È possibile collegare e configurare cluster Kubernetes all'interno o all'esterno di Azure con Kubernetes abilitato per Azure Arc (anteprima). Quando un cluster Kubernetes viene collegato ad Azure Arc, viene visualizzato nel portale di Azure. Avrà un ID Azure Resource Manager e un'identità gestita. I cluster sono collegati alle sottoscrizioni standard di Azure, si trovano in un gruppo di risorse e possono ricevere tag esattamente come qualsiasi altra risorsa di Azure. 

Per connettere un cluster Kubernetes ad Azure, l'amministratore del cluster deve distribuire gli agenti. Questi agenti vengono eseguiti in uno spazio dei nomi Kubernetes denominato `azure-arc` e sono distribuzioni Kubernetes standard. Gli agenti sono responsabili della connettività ad Azure, della raccolta di metriche e dei log di Azure Arc e del controllo delle richieste di configurazione. 

Kubernetes abilitato per Azure Arc supporta il protocollo SSL standard di settore per proteggere i dati in transito. Inoltre, i dati vengono archiviati crittografati quando sono inattivi in un database di Azure Cosmos DB per garantirne la riservatezza.
 
> [!NOTE]
> Kubernetes con abilitazione di Azure Arc è in versione di anteprima. Non è consigliabile usarlo per carichi di lavoro di produzione.

## <a name="supported-scenarios"></a>Scenari supportati 

Kubernetes abilitato per Azure Arc supporta questi scenari: 

* Connessione di Kubernetes in esecuzione all'esterno di Azure per l'inventario, il raggruppamento e l'assegnazione di tag.

* Distribuzione di applicazioni e applicazione della configurazione tramite la gestione della configurazione basata su GitOps. 

* Uso di Monitoraggio di Azure per i contenitori per visualizzare e monitorare i cluster. 

* Applicazione dei criteri tramite Criteri di Azure per Kubernetes. 

## <a name="supported-regions"></a>Aree supportate 

Kubernetes abilitato per Azure Arc è attualmente supportato in queste aree: 

* Stati Uniti orientali 
* Europa occidentale 

## <a name="next-steps"></a>Passaggi successivi

* [Connettere un cluster](./connect-cluster.md)
