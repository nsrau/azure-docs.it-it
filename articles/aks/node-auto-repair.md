---
title: Ripristino automatico di nodi del servizio Azure Kubernetes (AKS)
description: Informazioni sulle funzionalità di correzione automatica dei nodi e sul modo in cui AKS corregge i nodi di lavoro interrotti.
services: container-service
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 9bf9df69a0a6bfa4d9f4029278d2a146811980c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80284841"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Ripristino automatico del nodo di Azure Kubernetes Service (AKS)

AKS controlla continuamente lo stato di integrità dei nodi del ruolo di lavoro e esegue il ripristino automatico dei nodi se diventano non integri. Questa documentazione descrive il modo in cui Azure Kubernetes Service (AKS) monitora i nodi di lavoro e ripristina i nodi di lavoro non integri.  La documentazione è informare gli operatori AKS sul comportamento della funzionalità di ripristino del nodo. È inoltre importante notare che la piattaforma Azure [esegue la manutenzione sulle macchine virtuali in][vm-updates] cui si verificano problemi. AKS e Azure collaborano per ridurre al minimo le rotture dei servizi per i cluster.

> [!Important]
> La funzionalità di correzione automatica dei nodi non è attualmente supportata per i pool di nodi di Windows Server.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Modalità di verifica dei nodi non integri da AKS

> [!Note]
> AKS esegue un'azione di ripristino sui nodi con l'account utente **AKS-remediator**.

AKS usa le regole per determinare se un nodo è uno stato non integro e deve essere ripristinato. AKS usa le regole seguenti per determinare se è necessaria la riparazione automatica.

* Il nodo segnala lo stato di **nobattistrada** su controlli consecutivi entro un intervallo di tempo di 10 minuti
* Il nodo non segnala uno stato entro 10 minuti

È possibile controllare manualmente lo stato di integrità dei nodi con kubectl. 

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Funzionamento della riparazione automatica

> [!Note]
> AKS esegue un'azione di ripristino sui nodi con l'account utente **AKS-remediator**.

Questo comportamento è per i **set di scalabilità di macchine virtuali**.  Il ripristino automatico richiede diversi passaggi per ripristinare un nodo danneggiato.  Se un nodo è determinato come non integro, AKS tenta diversi passaggi di correzione.  I passaggi vengono eseguiti nell'ordine seguente:

1. Quando il runtime del contenitore smette di rispondere per 10 minuti, i servizi di runtime non riusciti vengono riavviati nel nodo.
2. Se il nodo non è pronto entro 10 minuti, il nodo viene riavviato.
3. Se il nodo non è pronto entro 30 minuti, viene ricreata l'immagine del nodo.

> [!Note]
> Se più nodi sono non integri, vengono ripristinati uno alla volta

## <a name="next-steps"></a>Passaggi successivi

Usare [zone di disponibilità][availability-zones] per aumentare la disponibilità elevata con i carichi di lavoro del cluster AKS.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
