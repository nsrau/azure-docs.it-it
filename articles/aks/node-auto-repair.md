---
title: Ripristino automatico dei nodi del servizio Azure Kubernetes (AKS)
description: Informazioni sulla funzionalità di ripristino automatico dei nodi e su come AKS corregge i nodi di lavoro interrotti.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284841"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Correzione automatica del nodo del servizio Azure Kubernetes (AKS)Azure Kubernetes Service (AKS) node auto-repair

AKS controlla continuamente lo stato di integrità dei nodi di lavoro ed esegue il ripristino automatico dei nodi se diventano non integri. Questa documentazione descrive come il servizio Azure Kubernetes Service (AKS) monitora i nodi di lavoro e ripristina i nodi di lavoro non integri.  La documentazione è per informare gli operatori AKS sul comportamento della funzionalità di ripristino dei nodi. È inoltre importante notare che la piattaforma Azure [esegue la manutenzione su macchine virtuali][vm-updates] che si verificano problemi. AKS e Azure collaborano per ridurre al minimo le interruzioni del servizio per i cluster.

> [!Important]
> La funzionalità di ripristino automatico dei nodi non è attualmente supportata per i pool di nodi di Windows Server.Node auto-repair functionality isn't currently supported for Windows Server node pools.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Come AKS controlla i nodi non integriHow AKS checks for unhealthy nodes

> [!Note]
> AKS esegue un'azione di riparazione sui nodi con l'account utente **aks-remediator**.

AKS utilizza le regole per determinare se un nodo è uno stato non integro e deve essere ripristinato. AKS utilizza le regole seguenti per determinare se è necessario il ripristino automatico.

* Il nodo segnala lo stato di **NotReady** su controlli consecutivi entro un intervallo di tempo di 10 minuti
* Il nodo non segnala uno stato entro 10 minuti

È possibile controllare manualmente lo stato di integrità dei nodi con kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Come funziona la riparazione automatica

> [!Note]
> AKS esegue un'azione di riparazione sui nodi con l'account utente **aks-remediator**.

Questo comportamento è per i set di **scalabilità di macchine virtuali**.  La riparazione automatica richiede diversi passaggi per riparare un nodo rotto.  Se un nodo viene determinato come non integro, AKS tenta diversi passaggi di correzione.  I passaggi vengono eseguiti nell'ordine seguente:

1. Dopo che il runtime del contenitore non risponde per 10 minuti, i servizi di runtime con errori vengono riavviati nel nodo.
2. Se il nodo non è pronto entro 10 minuti, il nodo viene riavviato.
3. Se il nodo non è pronto entro 30 minuti, viene rieseguita un'immagine del nodo.

> [!Note]
> Se più nodi non sono integri, vengono riparati uno per uno

## <a name="next-steps"></a>Passaggi successivi

Usare [le zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
