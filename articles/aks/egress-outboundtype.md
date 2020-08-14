---
title: Personalizzare le route definite dall'utente nel servizio Azure Kubernetes
description: Informazioni su come definire una route in uscita personalizzata nel servizio Azure Kubernetes
services: container-service
ms.topic: article
ms.author: juluk
ms.date: 06/29/2020
author: jluk
ms.openlocfilehash: 2ffe9d525e92fa2154889cea43f681a0f31a18ab
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214227"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Personalizzare l'uscita del cluster con una route definita dall'utente

È possibile personalizzare l'uscita da un cluster del servizio Azure Kubernetes per adattarla a scenari specifici. Per impostazione predefinita, AKS effettuerà il provisioning di uno SKU standard Load Balancer essere configurato e usato per l'uscita. La configurazione predefinita potrebbe, però, non soddisfare i requisiti di tutti gli scenari se gli indirizzi IP pubblici non sono consentiti o se per l'uscita sono necessari hop aggiuntivi.

Questo articolo illustra in dettaglio come personalizzare la route in uscita di un cluster in modo da supportare scenari di rete personalizzati, ad esempio quelli che non consentono indirizzi IP pubblici e in cui il cluster deve trovarsi dietro un'appliance virtuale di rete.

## <a name="prerequisites"></a>Prerequisiti
* Interfaccia della riga di comando di Azure 2.0.81 o versione successiva
* Versione API `2020-01-01` o successiva


## <a name="limitations"></a>Limitazioni
* OutboundType può essere definito solo in fase di creazione del cluster e non può essere aggiornato in seguito.
* Per impostare `outboundType`, sono necessari cluster del servizio Azure Kubernetes con `vm-set-type` impostato su `VirtualMachineScaleSets` e `load-balancer-sku` impostato su `Standard`.
* Per impostare `outboundType` sul valore `UDR`, è necessaria una route definita dall'utente con connettività in uscita valida per il cluster.
* L'impostazione di `outboundType` sul valore `UDR` implica che l'indirizzo IP di origine in ingresso indirizzato al servizio di bilanciamento del carico potrebbe **non corrispondere** all'indirizzo di destinazione in uscita del cluster.

## <a name="overview-of-outbound-types-in-aks"></a>Panoramica dei tipi in uscita nel servizio Azure Kubernetes

Un cluster del servizio Azure Kubernetes può essere personalizzato con un `outboundType` univoco di tipo bilanciamento del carico o un routing definito dall'utente.

> [!IMPORTANT]
> Il tipo in uscita influisce solo sul traffico in uscita del cluster. Per altre informazioni, vedere [configurazione dei controller di ingresso](ingress-basic.md).

> [!NOTE]
> È possibile usare la propria [tabella di route][byo-route-table] con la rete UdR e kubenet. Assicurarsi che l'identità del cluster (entità servizio o identità gestita) disponga di autorizzazioni di collaboratore per la tabella di route personalizzata.

### <a name="outbound-type-of-loadbalancer"></a>Tipo in uscita loadBalancer

Se `loadBalancer` è impostato, AKS completa automaticamente la configurazione seguente. Il di bilanciamento del carico viene usato per l'uscita tramite un indirizzo IP pubblico assegnato dal servizio Azure Kubernetes. Il tipo in uscita `loadBalancer` supporta i servizi Kubernetes di tipo `loadBalancer`, che prevedono l'uscita dal bilanciamento del carico creato dal provider di risorse del servizio Azure Kubernetes.

La configurazione seguente viene eseguita da AKS.
   * Viene effettuato il provisioning di un indirizzo IP pubblico per l'uscita del cluster.
   * L'indirizzo IP pubblico viene assegnato alla risorsa del bilanciamento del carico.
   * I pool back-end per il servizio di bilanciamento del carico sono configurati per i nodi agente nel cluster.

Di seguito è illustrata una topologia di rete distribuita nei cluster del servizio Azure Kubernetes per impostazione predefinita, in cui `outboundType` è impostato su `loadBalancer`.

![outboundtype-lb](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Tipo in uscita userDefinedRouting

> [!NOTE]
> L'uso di un tipo in uscita rappresenta uno scenario di rete avanzato e richiede una configurazione di rete appropriata.

Se `userDefinedRouting` è impostato, AKS non configurerà automaticamente i percorsi in uscita. La configurazione in uscita deve essere eseguita dall'utente.

Il cluster AKS deve essere distribuito in una rete virtuale esistente con una subnet configurata in precedenza perché, quando non si usa l'architettura di Load Balancer standard (SLB), è necessario stabilire una uscita esplicita. Per questo tipo di architettura è necessario inviare in modo esplicito il traffico in uscita a un dispositivo, ad esempio un firewall, un gateway, un proxy o consentire a Network Address Translation (NAT) di essere eseguito da un IP pubblico assegnato al servizio di bilanciamento del carico standard o al dispositivo.

Il provider di risorse del servizio Azure Kubernetes distribuirà un'istanza di Load Balancer Standard. Il servizio di bilanciamento del carico non è configurato con alcuna regola e non comporta alcun [addebito fino a quando non viene inserita una regola](https://azure.microsoft.com/pricing/details/load-balancer/). AKS **non** esegue automaticamente il provisioning di un indirizzo IP pubblico per il front-end SLB né configura automaticamente il pool back-end di bilanciamento del carico.

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Distribuire un cluster con tipo di uscita con route definita dall'utente e Firewall di Azure

Per illustrare l'applicazione di un cluster con tipo in uscita usando una route definita dall'utente, è possibile configurare un cluster in una rete virtuale con un firewall di Azure nella propria subnet. Vedere questo esempio sull' [esempio di limitazione del traffico in uscita con il firewall di Azure](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall).

> [!IMPORTANT]
> Il tipo di UDR in uscita richiede la presenza di una route per 0.0.0.0/0 e della destinazione hop successiva di appliance virtuale di rete nella tabella di route.
> La tabella di route dispone già di un 0.0.0.0/0 predefinito a Internet, senza un indirizzo IP pubblico per SNAT. l'aggiunta di questa route non fornirà l'uscita. AKS convaliderà la mancata creazione di una route 0.0.0.0/0 che punta a Internet, bensì a appliance virtuale di rete o gateway e così via.
> 
> Quando si usa un tipo di UDR in uscita, non viene creato un indirizzo IP pubblico di bilanciamento del carico a meno che non sia configurato un servizio di tipo *LoadBalancer* .

## <a name="next-steps"></a>Passaggi successivi

Vedere la [panoramica sulle route definite dall'utente per le reti Azure](../virtual-network/virtual-networks-udr-overview.md).

Vedere le informazioni su [come creare, modificare o eliminare una tabella di route](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
