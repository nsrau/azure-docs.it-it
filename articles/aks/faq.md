---
title: Domande frequenti relative al servizio contenitore di Azure
description: Risposte ad alcune domande comuni sul servizio contenitore di Azure.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/01/2018
ms.author: nepeters
ms.openlocfilehash: 2b78479c257930669729a7781b3893b3e2064bab
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="frequently-asked-questions-about-azure-container-service-aks"></a>Domande frequenti relative al servizio contenitore di Azure (AKS)

Questo articolo tratta alcune domande frequenti relative al servizio contenitore di Azure (AKS).

## <a name="which-azure-regions-will-have-azure-container-service-aks"></a>In quali aree sarà disponibile il servizio contenitore di Azure (AKS)? 

- Canada centrale 
- Canada orientale 
- Stati Uniti centrali 
- Stati Uniti orientali 
- Asia sudorientale 
- Europa occidentale 
- Stati Uniti occidentali 2 

## <a name="when-will-additional-regions-be-added"></a>Quando verranno aggiunte altre aree? 

Vengono aggiunte altre aree man mano che la richiesta aumenta.

## <a name="are-security-updates-applied-to-aks-nodes"></a>Gli aggiornamenti della sicurezza vengono applicati ai nodi AKS? 

Le patch di protezione del sistema operativo vengono applicate ai nodi del cluster in base a una pianificazione notturna, tuttavia non viene eseguito un riavvio. Se necessario, i nodi possono essere riavviati tramite il portale o l'interfaccia della riga di comando di Azure. Quando si aggiorna un cluster, viene usata l'immagine Ubuntu più recente e vengono applicate tutte le patch di protezione (con un riavvio).

## <a name="do-you-recommend-customers-use-acs-or-akss"></a>È preferibile usare ACS o AKS? 

Poiché il servizio contenitore di Azure (AKS) sarà disponibile a livello generale solo in un secondo momento, è consigliabile creare i cluster di sviluppo e test e PoC in AKS, mentre i cluster di produzione in ACS-Kubernetes.  

## <a name="when-will-acs-be-deprecated"></a>Da quando ACS sarà deprecato? 

ACS diventerà deprecato più o meno in corrispondenza della disponibilità generale di AKS. Dopo tale data saranno disponibili 12 mesi per eseguire la migrazione dei cluster ad AKS. Durante il periodo di 12 mesi, è possibile eseguire tutte le operazioni di ACS.

## <a name="does-aks-support-node-autoscaling"></a>AKS supporta la scalabilità automatica dei nodi? 

La scalabilità automatica dei nodi non è supportata, ma lo sarà in futuro. È possibile esaminare questa [implementazione della scalabilità automatica][auto-scaler] open source.

## <a name="why-are-two-resource-groups-created-with-aks"></a>Perché vengono creati due gruppi di risorse con AKS? 

Il secondo gruppo di risorse viene creato automaticamente per semplificare l'eliminazione di tutte le risorse associate a una distribuzione AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Azure Key Vault è integrato in AKS? 

No, non lo è, ma l'integrazione è pianificata per il futuro. Nel frattempo, è possibile provare la soluzione seguente di [Hexadite][hexadite]. 

<!-- LINKS - external -->
[auto-scaler]: https://github.com/kubernetes/autoscaler
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent  