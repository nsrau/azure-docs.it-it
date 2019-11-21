---
title: Domande frequenti su DNS privato di Azure
description: In this article, learn frequently asked questions about Azure Private DNS
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: de63799400a10afc1930cd373df0c8dd86320f78
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212002"
---
# <a name="azure-private-dns-faq"></a>Domande frequenti su DNS privato di Azure

The following are frequently asked questions about Azure private DNS.

## <a name="does-azure-dns-support-private-domains"></a>DNS di Azure supporta domini privati?

Private domains are supported using the Azure Private DNS zones feature. Private DNS zones are resolvable only from within specified virtual networks. Per altre informazioni, vedere l'articolo sulla [panoramica](private-dns-overview.md).

Per informazioni sulle altre opzioni DNS in Azure, vedere [Risoluzione dei nomi per le risorse in reti virtuali di Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="will-azure-private-dns-zones-work-across-azure-regions"></a>Will Azure Private DNS zones work across Azure regions?

Sì. Il servizio Zone private è supportato per la risoluzione DNS tra reti virtuali nelle aree di Azure. Il servizio funziona anche senza peering esplicito delle reti virtuali, All the virtual networks must be linked to the private DNS zone.

## <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Per le zone private è necessario che le reti virtuali siano connesse a Internet?

No. Le zone private interagiscono con le reti virtuali You use them to manage domains for virtual machines or other resources within and across virtual networks. La connettività a Internet non è necessaria per la risoluzione dei nomi.

## <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>È possibile usare la stessa zona privata per più reti virtuali di risoluzione?

Sì. You can link a private DNS zone with thousands of virtual networks. For more information, see [Azure DNS Limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits)

## <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-linked-to-a-private-zone"></a>Can a virtual network that belongs to a different subscription be linked to a private zone?

Sì. L'utente deve disporre dell'autorizzazione per le operazioni di scrittura sia nelle reti virtuali che nella zona DNS privata. L'autorizzazione di scrittura può essere assegnata a più ruoli Controllo degli accessi in base al ruolo. For example, the Classic Network Contributor RBAC role has write permissions to virtual networks and Private DNS zones Contributor role has write permissions on the private DNS zones. Per altre informazioni sui ruoli Controllo degli accessi in base al ruolo, vedere [Che cos'è il controllo degli accessi in base al ruolo?](../role-based-access-control/overview.md).

## <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-you-delete-the-virtual-machine"></a>Will the automatically registered virtual machine DNS records in a private zone be automatically deleted when you delete the virtual machine?

Sì. If you delete a virtual machine within a linked virtual network with autoregistration enabled, the registered records are automatically deleted.

## <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-linked-virtual-network-be-deleted-manually"></a>Can an automatically registered virtual machine record in a private zone from a linked virtual network be deleted manually?

Sì. È tuttavia possibile sovrascrivere i record DNS registrati automaticamente con un record DNS creato manualmente nella zona. La domanda e la risposta seguenti sono correlate a questa operazione.

## <a name="what-happens-when-i-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-linked-virtual-network"></a>What happens when I try to manually create a new DNS record into a private zone that has the same hostname as an automatically registered existing virtual machine in a linked virtual network?

You try to manually create a new DNS record into a private zone that has the same hostname as an existing, automatically registered virtual machine in a linked virtual network. il nuovo record DNS sovrascrive il record di macchina virtuale registrato automaticamente. Se si prova a eliminare dalla zona il record DNS creato manualmente, quest'ultimo verrà eliminato e la registrazione automatica viene ripetuta purché la macchina virtuale esista ancora e disponga di un indirizzo IP privato. A questo punto il record DNS viene creato nuovamente nella zona.

## <a name="what-happens-when-we-unlink-a-linked-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>What happens when we unlink a linked virtual network from a private zone? I record di macchina virtuale registrati automaticamente nella rete virtuale vengono rimossi anche dalla zona?

Sì. To unlink a linked virtual network from a private zone, you update the DNS zone to remove the associated virtual network link. Durante questo processo i record di macchina virtuale registrati automaticamente vengono rimossi dalla zona.

## <a name="what-happens-when-we-delete-a-linked-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-linked-virtual-network-from-the-zone"></a>What happens when we delete a linked virtual network that's linked to a private zone? Do we have to manually update the private zone to unlink the virtual network as a linked virtual network from the zone?

No. When you delete a linked virtual network without unlinking it from a private zone first, your deletion operation succeeds and the links to the DNS zone are automatically cleared.

## <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Will DNS resolution by using the default FQDN (internal.cloudapp.net) still work even when a private zone (for example, private.contoso.com) is linked to a virtual network?

Sì. Private Zones don't replace the default Azure-provided internal.cloudapp.net zone. Indipendentemente dal fatto che si usi la zona internal.cloudapp.net di Azure o la propria zona privata, è consigliabile usare il nome FQDN della zona rispetto alla quale si vuole eseguire la risoluzione.

## <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Il suffisso DNS sulle macchine virtuali all'interno di una rete virtuale collegata deve essere sostituito con quello della zona privata?

No. Il suffisso DNS sulle macchine virtuali nella rete virtuale collegata continua a essere quello predefinito di Azure ("*.internal.cloudapp.net"). È possibile sostituire manualmente questo suffisso nelle macchine virtuali con quello della zona privata.
For guidance on how to change this suffix refer to [Use dynamic DNS to register hostnames in your own DNS server](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-ddns#windows-clients)

## <a name="what-are-the-usage-limits-for-azure-dns-private-zones"></a>What are the usage limits for Azure DNS Private zones?

Refer to [Azure DNS limits](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-dns-limits) for details on the usage limits for Azure DNS private zones.

## <a name="why-dont-my-existing-private-dns-zones-show-up-in-new-portal-experience"></a>Why don’t my existing private DNS zones show up in new portal experience?

If your existing private DNS zone were created using preview API, you must migrate these zones to new resource model. Private DNS zones created using preview API will not show up in new portal experience. See below for instructions on how to migrate to new resource model.

## <a name="how-do-i-migrate-my-existing-private-dns-zones-to-the-new-model"></a>How do I migrate my existing private DNS zones to the new model?

We strongly recommend that you migrate to the new resource model as soon as possible. Legacy resource model will be supported, however, further features will not be developed on top of this model. In future, we intend to deprecate it in favor of new resource model. For guidance on how to migrate your existing private DNS zones to new resource model see[migration guide for Azure DNS private zones](private-dns-migration-guide.md).

## <a name="next-steps"></a>Passaggi successivi

- [Learn more about Azure Private DNS](private-dns-overview.md)
