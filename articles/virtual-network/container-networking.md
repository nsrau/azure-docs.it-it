---
title: Rete virtuale di Azure per i contenitori | Documenti Microsoft
description: Informazioni su CNI plug-in per i cluster Kubernetes, che consente ai contenitori di comunicare tra loro e altre risorse, in una rete virtuale.
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
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="container-networking"></a>Rete di contenitori

Azure offre un [plug-in interfaccia di rete contenitore (CNI)](https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md) che consente di distribuire e gestire cluster Kubernetes con capacità di rete di Azure nativa. Il plug-in è abilitata per impostazione predefinita, quando la distribuzione Kubernetes cluster con il [motore del servizio contenitore di Azure](https://github.com/Azure/acs-engine) (o il motore di ACS).

## <a name="networking-capabilities"></a>Funzionalità di rete

I contenitori possono utilizzare la vasta gamma di funzionalità che offre una rete virtuale, ad esempio:
-   È possibile creare una rete virtuale distinta per il cluster o distribuire il cluster in una rete virtuale esistente. 
-   Ogni pod nel cluster riceve un indirizzo IP nella rete virtuale e possono comunicare direttamente con altre unità del cluster e qualsiasi macchina virtuale nella rete virtuale. 
-   Un pod possono connettersi ad altri POD e macchine virtuali nel peering reti virtuali e alle reti locali, tramite connessioni VPN site-to-site ed ExpressRoute. Risorse locali possono comunicare con POD. 
-   È possibile esporre un servizio Kubernetes a Internet tramite il bilanciamento del carico di Azure.  
-   Unità in una subnet ha abilitati gli endpoint del servizio in modo sicuro possono connettersi a servizi di Azure (archiviazione e Database SQL, ad esempio).
-   È possibile utilizzare le route definite dall'utente per instradare il traffico dal POD un dispositivo di rete virtuale. 
-   POD possono accedere alle risorse pubbliche su Internet.
-   È possibile assegnare un pod un indirizzo IP pubblico, che può essere associato a un nome DNS.
 
## <a name="limits"></a>Limiti
È possibile distribuire i nodi fino a 4.000 in un cluster Kubernetes e fino a 250 POD per ogni nodo, con un limite generale di 16.000 POD per ogni cluster, quando si usa il plug-in.

## <a name="constraints"></a>Vincoli
- Il plug-in non è abilitato quando si distribuisce un cluster Kubernetes con il [servizio contenitore di Azure (AKS)](../aks/intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [ACS](../container-service/kubernetes/container-service-intro-kubernetes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) cluster con Kubernetes.
- Non è presente alcun supporto nativo per i criteri di rete Kubernetes, inclusi i criteri di accesso o DNS.
- Il plug-in non supporta i criteri di rete per pod.

## <a name="pricing"></a>Prezzi
Non è previsto alcun addebito per mediante il plug-in CNI.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire un cluster Kubernetes](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/deploy.md) nel [propria rete virtuale](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes/features.md#using-azure-integrated-networking-cni).
