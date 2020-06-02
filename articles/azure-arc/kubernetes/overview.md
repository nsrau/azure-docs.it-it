---
title: Panoramica di Kubernetes con abilitazione di Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: overview
author: mlearned
ms.author: mlearned
description: ''
keywords: Kubernetes, Arc, Azure, contenitori
ms.openlocfilehash: 3b3b184ff7b18e05dfeb1052c0d6b4aa406a8787
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665276"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Che cos'è Kubernetes con abilitazione di Azure Arc (anteprima)?

È possibile collegare e configurare cluster Kubernetes all'interno o all'esterno di Azure con Kubernetes con abilitazione di Azure Arc (anteprima). Quando un cluster Kubernetes viene collegato ad Azure Arc, verrà visualizzato nel portale di Azure, avrà un ID Azure Resource Manager e un'identità gestita. I cluster sono collegati alle sottoscrizioni standard di Azure, si trovano in un gruppo di risorse e possono ricevere tag esattamente come qualsiasi altra risorsa di Azure. 


Per la connessione di un cluster Kubernetes ad Azure è necessario che un amministratore del cluster distribuisca agenti. Questi agenti vengono eseguiti in uno spazio dei nomi Kubernetes denominato `azure-arc` e sono distribuzioni Kubernetes standard. Gli agenti sono responsabili della connettività ad Azure, della raccolta di metriche e dei log di Azure Arc e del controllo delle richieste di configurazione.  
 
 > [!NOTE]
> Kubernetes con abilitazione di Azure Arc è disponibile in anteprima e non è consigliato per i carichi di lavoro di produzione. 


## <a name="supported-scenarios"></a>Scenari Supportati 

Kubernetes con abilitazione di Azure Arc supporta gli scenari seguenti: 

* Connessione di Kubernetes in esecuzione all'esterno di Azure per l'inventario, il raggruppamento e l'assegnazione di tag 

* Distribuzione di applicazioni e applicazione della configurazione tramite la gestione della configurazione basata su GitOps 

* Uso di Monitoraggio di Azure per i contenitori per visualizzare e monitorare i cluster 

* Applicazione dei criteri tramite Criteri di Azure per Kubernetes 

 
## <a name="supported-regions"></a>Aree supportate 

Kubernetes con abilitazione di Azure Arc è attualmente supportato nelle aree seguenti: 

* Stati Uniti orientali 
* Europa occidentale 


## <a name="next-steps"></a>Passaggi successivi

* [Connettersi a un cluster](./connect-cluster.md)
