---
title: Che cos'è applicazione Azure controller di ingresso gateway?
description: Questo articolo fornisce un'introduzione al controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: c1bd41587e4f56fb0a7f3eb8285d301751f558d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668101"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Che cos'è il controller di ingresso del gateway applicazione?
Il controller di ingresso del gateway applicazione (AGIC) è un'applicazione Kubernetes, che consente ai clienti di [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) di sfruttare il servizio di bilanciamento del carico L7 del [gateway applicazione](https://azure.microsoft.com/services/application-gateway/) nativo di Azure per esporre il software cloud a Internet. AGIC monitora il cluster Kubernetes in cui è ospitato e aggiorna continuamente un gateway applicazione, in modo che i servizi selezionati siano esposti a Internet.

Il controller di ingresso viene eseguito nel proprio pod sul AKS del cliente. AGIC monitora un subset di risorse Kubernetes per le modifiche. Lo stato del cluster AKS viene convertito in configurazione specifica del gateway applicazione e applicato al [Azure Resource Manager (ARM)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Vantaggi del controller di ingresso del gateway applicazione
AGIC consente di eliminare la necessità di un altro servizio di bilanciamento del carico/IP pubblico davanti al cluster AKS ed evita più hop nel percorso di DataPath prima che le richieste raggiungano il cluster AKS. Il gateway applicazione comunica con i pod usando direttamente il proprio indirizzo IP privato e non richiede i servizi Deport o KubeProxy. Offre inoltre prestazioni migliori per le distribuzioni.

Il controller di ingresso è supportato esclusivamente da SKU Standard_v2 e WAF_v2, che offre anche vantaggi per la scalabilità automatica. Il gateway applicazione può reagire in risposta a un aumento o una riduzione del carico di traffico e della scalabilità di conseguenza, senza l'utilizzo di risorse dal cluster AKS.

L'uso del gateway applicazione oltre a AGIC consente anche di proteggere il cluster AKS fornendo i criteri TLS e la funzionalità Web Application Firewall (WAF).

![Gateway applicazione Azure + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC viene configurato tramite la [risorsa di ingresso](https://kubernetes.io/docs/user-guide/ingress/)Kubernetes, insieme al servizio e alle distribuzioni/pod. Offre una serie di funzionalità, sfruttando il servizio di bilanciamento del carico L7 del gateway applicazione nativo di Azure. Ecco alcuni esempi:
  - Routing basato su URL
  - Affinità basata sui cookie
  - Terminazione TLS
  - TLS end-to-end
  - Supporto per siti Web pubblici, privati e ibridi
  - web application firewall integrata

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Differenza tra la distribuzione Helm e il componente aggiuntivo AKS
Esistono due modi per distribuire AGIC per il cluster AKS. Il primo modo consiste nell'usare Helm; il secondo avviene tramite AKS come componente aggiuntivo. Il vantaggio principale della distribuzione di AGIC come componente aggiuntivo AKS è che è molto più semplice rispetto alla distribuzione tramite Helm. Per una nuova configurazione, è possibile distribuire un nuovo gateway applicazione e un nuovo cluster AKS con AGIC abilitato come componente aggiuntivo in una sola riga nell'interfaccia della riga di comando di Azure. Il componente aggiuntivo è anche un servizio completamente gestito, che offre vantaggi aggiuntivi come gli aggiornamenti automatici e un maggiore supporto. AGIC distribuito tramite Helm non è supportato da AKS, tuttavia il AGIC distribuito come componente aggiuntivo AKS è supportato da AKS. 

Il componente aggiuntivo AGIC è ancora distribuito come Pod nel cluster AKS del cliente. Tuttavia, esistono alcune differenze tra la versione della distribuzione Helm e la versione del componente aggiuntivo di AGIC. Di seguito è riportato un elenco delle differenze tra le due versioni: 
  - Non è possibile modificare i valori di distribuzione Helm nel componente aggiuntivo AKS:
    - `verbosityLevel`verrà impostato su 5 per impostazione predefinita
    - `usePrivateIp`verrà impostato su false per impostazione predefinita. Questa operazione può essere sovrascritta dall' [annotazione use-Private-IP](ingress-controller-annotations.md#use-private-ip)
    - `shared`non è supportato nel componente aggiuntivo 
    - `reconcilePeriodSeconds`non è supportato nel componente aggiuntivo
    - `armAuth.type`non è supportato nel componente aggiuntivo
  - AGIC distribuito tramite Helm supporta ProhibitedTargets, che significa che AGIC può configurare il gateway applicazione in modo specifico per i cluster AKS senza influire su altri backend esistenti. Il componente aggiuntivo AGIC attualmente non supporta questa operazione. 
  - Poiché il componente aggiuntivo AGIC è un servizio gestito, i clienti verranno aggiornati automaticamente alla versione più recente del componente aggiuntivo AGIC, a differenza di AGIC distribuiti tramite Helm dove il cliente deve aggiornare manualmente AGIC. 

> [!NOTE]
> Il metodo di distribuzione del componente aggiuntivo AGIC AKS è attualmente in fase di anteprima. Non è consigliabile eseguire i carichi di lavoro di produzione sulle funzionalità ancora in fase di anteprima, pertanto se si è curiosi di provare, è consigliabile configurare un nuovo cluster per testarlo con. 

Le tabelle seguenti ordinano gli scenari attualmente supportati con la versione di distribuzione Helm e la versione del componente aggiuntivo AKS di AGIC. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>Componente aggiuntivo AKS AGIC (singolo cluster AKS)
|                  |1 gateway applicazione |2 + gateway applicazione |
|------------------|---------|--------|
|**1 AGIC**|Sì, questa operazione è supportata |No, questo è nel backlog |
|**2 + AGICs**|No, solo 1 AGIC supportato/cluster |No, solo 1 AGIC supportato/cluster |

### <a name="helm-deployed-agic-single-aks-cluster"></a>AGIC distribuito da Helm (singolo cluster AKS)
|                  |1 gateway applicazione |2 + gateway applicazione |
|------------------|---------|--------|
|**1 AGIC**|Sì, questa operazione è supportata |No, questo è nel backlog |
|**2 + AGICs**|È necessario usare la funzionalità Shared ProhibitedTarget e controllare gli spazi dei nomi separati |Sì, questa operazione è supportata |

### <a name="helm-deployed-agic-2-aks-clusters"></a>AGIC distribuito da Helm (2 + cluster AKS)
|                  |1 gateway applicazione |2 + gateway applicazione |
|------------------|---------|--------|
|**1 AGIC**|N/D |N/D |
|**2 + AGICs**|È necessario usare la funzionalità Shared ProhibitedTarget |N/D |

## <a name="next-steps"></a>Passaggi successivi
- Installazione del [**componente aggiuntivo di AKS**](tutorial-ingress-controller-add-on-new.md): istruzioni sull'installazione del componente aggiuntivo AGIC, AKS e del gateway applicazione in un'infrastruttura Slate vuota.
- [**Distribuzione del componente aggiuntivo Brownfield di AKS**](tutorial-ingress-controller-add-on-existing.md): installare il componente aggiuntivo AGIC in un cluster AKS con un gateway applicazione esistente.
- [**Distribuzione Helm Greenfield**](ingress-controller-install-new.md): installare AGIC tramite Helm, il nuovo cluster AKS e il nuovo gateway applicazione nell'infrastruttura Slate vuota.
- [**Distribuzione di Helm Brownfield**](ingress-controller-install-existing.md): distribuire AGIC tramite Helm in un cluster AKS esistente e un gateway applicazione.

