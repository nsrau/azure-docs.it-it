---
title: Rete virtuale di Azure per contenitori | Documentazione Microsoft
description: Informazioni sul plug-in CNI per i cluster Kubernetes, che consente ai contenitori di comunicare tra loro e con altre risorse in una rete virtuale.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/18/2017
ms.author: jdial
ms.openlocfilehash: f70afa8ff69b6c79363313c0f2df3b6785da8d81
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Rete del contenitore

Azure offre un [plug-in CNI (Container Networking Interface)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) che consente di distribuire e gestire il proprio cluster Kubernetes con funzionalità di rete nativa di Azure. Il plug-in è abilitato per impostazione predefinita quando si distribuiscono cluster Kubernetes con il [motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine) (o motore ACS).

## <a name="networking-capabilities"></a>Funzionalità di rete

I contenitori possono utilizzare la vasta gamma di funzionalità offerte da una rete virtuale, ad esempio:
-   È possibile creare una rete virtuale distinta per il cluster o distribuire il cluster in una rete virtuale esistente. 
-   Ogni pod nel cluster riceve un indirizzo IP nella rete virtuale e può comunicare direttamente con altri pod del cluster e qualsiasi macchina virtuale nella rete virtuale. 
-   Un pod può connettersi ad altri pod e macchine virtuali in reti virtuali con peering e a reti locali, tramite connessioni VPN da sito a sito ed ExpressRoute. Le risorse locali possono comunicare ai pod. 
-   È possibile esporre un servizio Kubernetes a Internet tramite Azure Load Balancer.  
-   I pod in una subnet con endpoint di servizio abilitati possono connettersi in modo sicuro ai servizi di Azure (Archiviazione e Database SQL, ad esempio).
-   È possibile utilizzare route definite dall'utente per instradare il traffico dai pod a un'appliance virtuale di rete. 
-   I pod possono accedere alle risorse pubbliche su Internet.
-   È possibile assegnare a un pod un indirizzo IP pubblico, che può essere associato a un nome DNS.
 
## <a name="limits"></a>Limiti
È possibile distribuire fino a 4.000 nodi in un cluster Kubernetes e fino a 250 pod per ogni nodo, con un limite generale di 16.000 pod per cluster, quando si usa il plug-in.

## <a name="constraints"></a>Vincoli
- Il plug-in non è abilitato quando si distribuisce un cluster Kubernetes con il [servizio contenitore di Azure (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un cluster [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con Kubernetes.
- Non è presente alcun supporto nativo per i criteri di rete Kubernetes, tra cui criteri di accesso o DNS.
- Il plug-in non supporta criteri di rete per pod.

## <a name="pricing"></a>Prezzi
Non sono previsti addebiti per l'uso del plug-in CNI.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire un cluster Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) in una [rete virtuale](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
