---
title: 'Gruppo di sicurezza di rete: come funziona'
titlesuffix: Azure Virtual Network
description: Informazioni su come i gruppi di sicurezza di rete consentono di filtrare il traffico di rete tra le risorse di Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: e60cdfb00d0dc9d446bd52a72e9fd15676acd285
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89458196"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Filtrare il traffico di rete con i gruppi di sicurezza di rete
<a name="network-security-groups"></a>

È possibile usare un gruppo di sicurezza di rete di Azure per filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure. Un gruppo di sicurezza di rete contiene [regole di sicurezza](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) che consentono o negano il traffico di rete in ingresso o il traffico di rete in uscita da, diversi tipi di risorse di Azure. Per ogni regola è possibile specificare l'origine e la destinazione, la porta e il protocollo.

È possibile distribuire le risorse di diversi servizi di Azure in una rete virtuale di Azure. Per un elenco completo, vedere [Servizi distribuibili in una rete virtuale](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). È possibile associare zero o un gruppo di sicurezza di rete a ogni [subnet](virtual-network-manage-subnet.md#change-subnet-settings) e [interfaccia di rete](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) della rete virtuale in una macchina virtuale. Lo stesso gruppo di sicurezza di rete può essere associato a un numero qualsiasi di subnet e interfacce di rete.

L'immagine seguente illustra diversi scenari per l'implementazione dei gruppi di sicurezza di rete per consentire il traffico di rete da e verso Internet tramite la porta TCP 80:

![Elaborazione dei gruppi di sicurezza di rete](./media/network-security-group-how-it-works/network-security-group-interaction.png)

Vedere l'immagine precedente e il testo seguente per comprendere come Azure elabora le regole in ingresso e in uscita per i gruppi di sicurezza di rete:

## <a name="inbound-traffic"></a>Traffico in ingresso

Per il traffico in ingresso, Azure elabora prima le regole di un gruppo di sicurezza di rete associato a una subnet, se presente, quindi le regole di un gruppo di sicurezza di rete associato all'interfaccia di rete, se presente.

- **VM1**: le regole di sicurezza in *NSG1* vengono elaborate perché associate a *Subnet1* e *VM1* si trova in *Subnet1*. A meno che non sia stata creata una regola che consenta l'ingresso alla porta 80, il traffico viene negato dalla regola di sicurezza predefinita [DenyAllInbound](https://docs.microsoft.com/azure/virtual-network/security-overview#denyallinbound) e non viene mai valutato da *NSG2*, perché *NSG2* è associato all'interfaccia di rete. Se *NSG1* ha una regola di sicurezza che consente la porta 80, il traffico verrà quindi elaborato da *NSG2*. Per consentire la porta 80 per la macchina virtuale, sia *NSG1* che *NSG2* devono avere una regola che consenta la porta 80 da Internet.
- **VM2**: le regole in *NSG1* vengono elaborate perché *VM2* si trova anche in *Subnet1*. Poiché *VM2* non ha un gruppo di sicurezza di rete associato alla propria interfaccia di rete, riceve tutto il traffico consentito attraverso *NSG1* oppure tutto il traffico viene negato da *NSG1*. Quando un gruppo di sicurezza di rete è associato a una subnet, il traffico è consentito o negato a tutte le risorse della stessa subnet.
- **VM3**: poiché a *Subnet2* non è associato alcun gruppo di sicurezza di rete, il traffico è consentito nella subnet ed elaborato da *NSG2*, perché *NSG2* è associato all'interfaccia di rete collegata a *VM3*.
- **VM4**: il traffico è consentito in *VM4,* perché a *Subnet3*o all'interfaccia di rete della macchina virtuale non è associato un gruppo di sicurezza di rete. È consentito tutto il traffico di rete attraverso una subnet e un'interfaccia di rete se questi componenti non hanno un gruppo di sicurezza di rete associato.

## <a name="outbound-traffic"></a>Traffico in uscita

Per il traffico in uscita, Azure elabora prima le regole di un gruppo di sicurezza di rete associato a un'interfaccia di rete, se presente, quindi le regole di un gruppo di sicurezza di rete associato alla subnet, se presente.

- **VM1**: vengono elaborate le regole di sicurezza presenti in *NSG2*. A meno che non si crei una regola di sicurezza che nega la porta 80 in uscita verso Internet, il traffico è consentito dalla regola di sicurezza predefinita [AlllowInternetOutbound](https://docs.microsoft.com/azure/virtual-network/security-overview#allowinternetoutbound) in *NSG1* e *NSG2*. Se *NSG2* ha una regola di sicurezza che nega la porta 80, il traffico viene negato e mai valutato da *NSG1*. Per negare la porta 80 dalla macchina virtuale, uno o entrambi i gruppi di sicurezza di rete devono avere una regola che nega la porta 80 verso Internet.
- **VM2**: tutto il traffico viene inviato attraverso l'interfaccia di rete alla subnet perché l'interfaccia di rete collegata a *VM2* non ha un gruppo di sicurezza di rete associato. Vengono elaborate le regole in *NSG1*.
- **VM3**: se *NSG2* ha una regola di sicurezza che nega la porta 80, il traffico viene negato. Se *NSG2* ha una regola di sicurezza che consente la porta 80, la porta 80 è consentita in uscita verso Internet perché a *Subnet2* non è associato un gruppo di sicurezza di rete.
- **VM4**: tutto il traffico di rete è consentito da *VM4* perché a *Subnet3* o all'interfaccia di rete collegata alla macchina virtuale non è associato un gruppo di sicurezza di rete.


## <a name="intra-subnet-traffic"></a>Traffico all'interno della subnet

È importante notare che le regole di sicurezza in un NSG associato a una subnet possono influire sulla connettività tra le macchine virtuali. Se, ad esempio, viene aggiunta una regola a *NSG1* che nega tutto il traffico in ingresso e in uscita, *VM1* e *VM2* non saranno più in grado di comunicare tra loro. È necessario aggiungere un'altra regola in modo specifico per consentire questa operazione. 

Le regole di aggregazione applicate a un'interfaccia di rete possono essere verificate facilmente visualizzando le [regole di sicurezza effettive](virtual-network-network-interface.md#view-effective-security-rules) per un'interfaccia di rete. È anche possibile usare la funzionalità di [verifica del flusso IP](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) in Azure Network Watcher per determinare se è consentita la comunicazione da o verso un'interfaccia di rete. La verifica del flusso IP indica se una comunicazione è consentita o negata e quale regola di sicurezza di rete consente o nega il traffico.

> [!NOTE]
> I gruppi di sicurezza di rete sono associati a subnet o a macchine virtuali e servizi cloud distribuiti nel modello di distribuzione classica e a subnet o interfacce di rete nel modello di distribuzione Gestione risorse. Per altre informazioni in proposito, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> A meno che non esista un motivo specifico per, è consigliabile associare un gruppo di sicurezza di rete a una subnet o un'interfaccia di rete, ma non entrambi. Dato che le regole di un gruppo di sicurezza di rete associato a una subnet possono entrare in conflitto con quelle di un gruppo di sicurezza di rete associato a un'interfaccia di rete, è possibile che si verifichino problemi di comunicazione imprevisti che devono essere risolti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle risorse di Azure che possono essere distribuite in una rete virtuale e sui gruppi di sicurezza di rete ad esse associati, vedere [Integrazione della rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md).
* Se non è mai stato creato un gruppo di sicurezza di rete, è possibile completare una breve [esercitazione](tutorial-filter-network-traffic.md) per acquisire esperienza nella creazione di tale gruppo.
* Se si ha familiarità con i gruppi di sicurezza di rete e si ha la necessità di gestirli, vedere [Gestire un gruppo di sicurezza di rete](manage-network-security-group.md). 
* Se si verificano problemi di comunicazione ed è necessario risolvere i problemi dei gruppi di sicurezza di rete, vedere [Diagnosticare problemi di filtro del traffico di rete di una macchina virtuale](diagnose-network-traffic-filter-problem.md). 
* Informazioni su come abilitare i [log di flusso dei gruppi di sicurezza di rete](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) per analizzare il traffico di rete da e verso le risorse a cui è associato un gruppo di sicurezza di rete.
