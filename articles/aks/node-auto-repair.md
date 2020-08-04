---
title: Ripristino automatico di nodi del servizio Azure Kubernetes (AKS)
description: Informazioni sulle funzionalità di correzione automatica dei nodi e sul modo in cui AKS corregge i nodi di lavoro interrotti.
services: container-service
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 7fcb7b380f3694aaf34328019c3e09f5157c9e64
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542043"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Ripristino automatico del nodo di Azure Kubernetes Service (AKS)

AKS controlla continuamente lo stato di integrità dei nodi del ruolo di lavoro e esegue il ripristino automatico dei nodi se diventano non integri. Questo documento informa gli operatori sul comportamento della funzionalità di correzione automatica dei nodi. Oltre alle riparazioni di AKS, la piattaforma VM di Azure [esegue la manutenzione sulle macchine virtuali][vm-updates] in cui si verificano problemi. AKS e le VM di Azure interagiscono per ridurre al minimo le rotture dei servizi per i cluster.

## <a name="limitations"></a>Limitazioni

* I pool di nodi di Windows non sono attualmente supportati.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Modalità di verifica dei nodi non integri da AKS

AKS usa le regole per determinare se un nodo non è integro e deve essere ripristinato. AKS usa le regole seguenti per determinare se è necessaria la riparazione automatica.

* Il nodo segnala lo stato di **nobattistrada** su controlli consecutivi entro un intervallo di tempo di 10 minuti
* Il nodo non segnala uno stato entro 10 minuti

È possibile controllare manualmente lo stato di integrità dei nodi con kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Funzionamento della riparazione automatica

> [!Note]
> AKS avvia le operazioni di ripristino con l'account utente **AKS-remediator**.

Se un nodo è determinato come non integro in base alle regole precedenti e rimane non integro per 10 minuti consecutivi, AKS riavvia il nodo. Se i nodi rimangono non integri dopo l'operazione di ripristino iniziale, le correzioni aggiuntive vengono esaminate dai tecnici di AKS.
  
Se durante un controllo di integrità sono presenti più nodi non integri, ogni nodo viene ripristinato singolarmente prima dell'avvio di un'altra riparazione.

## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster del servizio Azure Kubernetes.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
