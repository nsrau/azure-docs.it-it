---
title: Che cos'è il controller in ingresso del gateway applicazione di Azure?
description: In questo articolo viene fornita un'introduzione al controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: d6a63b6276c07b1fe6487b97f5c7fc255b6d3411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335810"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Che cos'è il controller di ingresso del gateway applicazione?
Application Gateway Ingress Controller (AGIC) è un'applicazione Kubernetes, che consente ai clienti del [servizio Azure Kubernetes (AKS)](https://azure.microsoft.com/services/kubernetes-service/) di sfruttare il bilanciamento del carico L7 del [gateway](https://azure.microsoft.com/services/application-gateway/) applicazione nativo di Azure per esporre il software cloud a Internet. AGIC monitora il cluster Kubernetes in cui è ospitato e aggiorna continuamente un gateway applicazione, in modo che i servizi selezionati vengano esposti a Internet.

Il controller Ingress viene eseguito nel proprio pod nell'AKS del cliente. AGIC monitora un sottoinsieme di Kubernetes Resources per le modifiche. Lo stato del cluster AKS viene convertito in configurazione specifica del gateway applicazione e applicato a [Azure Resource Manager (ARM).](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="benefits-of-application-gateway-ingress-controller"></a>Vantaggi del controller di ingresso del gateway applicazione
AGIC consente alla distribuzione di controllare più cluster AKS con un singolo controller di ingresso del gateway applicazione. AGIC consente inoltre di eliminare la necessità di disporre di un altro servizio di bilanciamento del carico/IP pubblico davanti al cluster AKS ed evita più hop nel percorso dati prima che le richieste raggiungano il cluster AKS. Il gateway applicazione comunica con i pod utilizzando direttamente l'indirizzo IP privato e non richiede servizi NodePort o KubeProxy. Ciò offre anche prestazioni migliori per le distribuzioni.

Il controller in ingresso è supportato esclusivamente da sKU Standard_v2 e WAF_v2, che offre anche vantaggi di scalabilità automatica. Il gateway applicazione può reagire in risposta a un aumento o alla riduzione del carico di traffico e della scalabilità di conseguenza, senza consumare risorse del cluster AKS.

L'utilizzo del gateway applicazione oltre ad AGIC consente inoltre di proteggere il cluster AKS fornendo criteri TLS e funzionalità WaF (Web Application Firewall).

![Gateway applicazione di Azure - AKSAzure Application Gateway - AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC viene configurato tramite la risorsa Kubernetes [Ingress](https://kubernetes.io/docs/user-guide/ingress/), insieme a Service and Deployments/Pods. Fornisce una serie di funzionalità, sfruttando il servizio di bilanciamento del carico nativo di Azure del gateway applicazione L7.It provides a number of features, leveraging Azure's native Application Gateway L7 load balancer. Ecco alcuni esempi:
  - Routing basato su URL
  - Affinità basata sui cookie
  - Terminazione TLS
  - End-to-end TLS
  - Supporto per siti Web pubblici, privati e ibridi
  - Firewall integrato per le applicazioni Web

AGIC è in grado di gestire più spazi dei nomi e ha ProhibitedTargets, il che significa che AGIC può configurare il gateway applicazione in modo specifico per i cluster AKS senza influire sugli altri back-end esistenti. 

## <a name="next-steps"></a>Passaggi successivi

- [**Greenfield Deployment**](ingress-controller-install-new.md): Istruzioni sull'installazione di AGIC, AKS e gateway applicazione su un'infrastruttura vuota.
- [**Distribuzione Brownfield**](ingress-controller-install-existing.md): installare AGIC in un gateway applicazione e AKS esistente.

