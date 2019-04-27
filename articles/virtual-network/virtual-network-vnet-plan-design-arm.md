---
title: Pianificare le reti virtuali di Azure | Microsoft Docs
description: Informazioni su come pianificare le reti virtuali in base ai requisiti di isolamento, connettività e località.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: acd7a88acb31b9d3bd3ba714387561e91b3524a6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034740"
---
# <a name="plan-virtual-networks"></a>Pianificare le reti virtuali

Creare una rete virtuale per sperimentare è abbastanza semplice, ma è probabile che nel corso del tempo si distribuiscano più reti virtuali per supportare le esigenze di produzione dell'organizzazione. Con una pianificazione è possibile distribuire reti virtuali e connettere le risorse necessarie in modo più efficace. Le informazioni contenute in questo articolo sono particolarmente utili se si ha già familiarità con le reti virtuali e si ha esperienza con il loro utilizzo. Se non si ha familiarità con le reti virtuali, è consigliabile leggere [Panoramica sulle reti virtuali](virtual-networks-overview.md).

## <a name="naming"></a>Denominazione

Tutte le risorse di Azure hanno un nome. Il nome deve essere univoco all'interno di un ambito, che può variare per ogni tipo di risorsa. Ad esempio, il nome di una rete virtuale deve essere univoco all'interno di un [gruppo di risorse](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), ma può essere duplicato all'interno di una [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) o di un'[area](https://azure.microsoft.com/regions/#services) di Azure. Definire una convenzione di denominazione che è possibile usare in modo coerente quando si denominano le risorse risulta utile quando si gestiscono diverse risorse di rete nel corso del tempo. Per alcuni suggerimenti, vedere [Naming conventions](/azure/architecture/best-practices/naming-conventions?toc=%2fazure%2fvirtual-network%2ftoc.json#networking) (Convenzioni di denominazione).

## <a name="regions"></a>Regioni

Tutte le risorse di Azure vengono create in un'area e in una sottoscrizione di Azure. Una risorsa può essere creata solo in una rete virtuale presente nella stessa area e nella stessa sottoscrizione della risorsa. È tuttavia possibile connettere reti virtuali esistenti in sottoscrizioni e aree diverse. Per altre informazioni, vedere [Problemi di connettività](#connectivity). Quando si decidono le aree in cui distribuire le risorse, prendere in considerazione dove si trovano fisicamente i consumer delle risorse:

- I consumer delle risorse in genere richiedono la latenza di rete più bassa per le loro risorse. Per determinare le latenze relative tra una posizione specificata e le aree di Azure, vedere [Visualizzare le latenze relative](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Sono presenti requisiti di residenza, sovranità , conformità  o resilienza dei dati? In tal caso, è fondamentale scegliere l'area adeguata ai requisiti. Per altre informazioni, vedere [Aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/geographies/).
- È richiesta la resilienza tra le zone di disponibilità di Azure nella stessa area di Azure per le risorse distribuite? È possibile distribuire le risorse, ad esempio le macchine virtuali (VM) in zone di disponibilità diversa nella stessa rete virtuale. Tuttavia, non tutte le aree di Azure supportano le zone di disponibilità. Per altre informazioni sulle zone di disponibilità e sulle aree che le supportano, vedere [Zone di  disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="subscriptions"></a>Sottoscrizioni

È possibile distribuire le reti virtuali in base alle esigenze all'interno di ogni sottoscrizione, fino ad arrivare al [limite](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Alcune organizzazioni hanno diverse sottoscrizioni per reparti diversi, ad esempio. Per altre informazioni e considerazioni riguardanti le sottoscrizioni, vedere [Governance sottoscrizione](/azure/architecture/cloud-adoption-guide/subscription-governance#define-your-hierarchy).

## <a name="segmentation"></a>Segmentazione

È possibile creare più reti virtuali per ogni sottoscrizione e per ogni area. È possibile creare più subnet all'interno di ogni rete virtuale. Le considerazioni che seguono consentono di determinare il numero di reti virtuali e le subnet necessarie:

### <a name="virtual-networks"></a>Reti virtuali

Una rete virtuale è una parte virtuale, isolata della rete pubblica di Azure. Ogni rete virtuale è dedicata alla sottoscrizione. Aspetti da considerare quando si decide se creare una rete virtuale o più reti virtuali in una sottoscrizione:

- Sono presenti requisiti di sicurezza aziendali per isolare il traffico in reti virtuali separate? È possibile scegliere di connettere o meno le reti virtuali. Se si connettono le reti virtuali, è possibile implementare un'appliance virtuale di rete, ad esempio un firewall, per controllare il flusso del traffico tra le reti virtuali. Per altre informazioni, vedere [sicurezza](#security) e [connettività](#connectivity).
- Sono presenti requisiti aziendali per isolare le reti virtuali in [sottoscrizioni](#subscriptions) o [aree](#regions) separate?
- Un'[interfaccia di rete](virtual-network-network-interface.md) consente a una macchina virtuale di comunicare con altre risorse. Ogni interfaccia di rete ha uno o più indirizzi IP privati associati. Quante interfacce di rete e [indirizzi IP privati](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) sono necessari in una rete virtuale? Sono previsti dei [limiti](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) al numero di interfacce di rete e indirizzi IP privati che è possibile avere all'interno di una rete virtuale.
- La rete virtuale deve essere connessa a un'altra rete virtuale o alla rete locale? È possibile scegliere di connettere alcune reti virtuali tra loro o alle reti locali, ma non ad altre. Per altre informazioni, vedere [Problemi di connettività](#connectivity). Ogni rete virtuale che si connette a un'altra rete virtuale o a una rete locale, deve includere uno spazio di indirizzo univoco. Ogni rete virtuale ha uno o più intervalli di indirizzi pubblici o privati assegnati al proprio spazio degli indirizzi. Viene specificato un intervallo di indirizzi in un formato routing di dominio classless internet (CIDR), ad esempio 10.0.0.0/16. Altre informazioni sugli [intervalli di indirizzi](manage-virtual-network.md#add-or-remove-an-address-range) per le reti virtuali.
- Si dispone di tutti i requisiti aziendali di amministrazione per le risorse in reti virtuali diverse? Se è così, è possibile separare le risorse in una rete virtuale distinta per semplificare l'[assegnazione dell'autorizzazione](#permissions) a utenti singoli nell'organizzazione o per assegnare diversi criteri a diverse reti virtuali.
- Quando si distribuiscono alcune risorse del servizio di Azure in una rete virtuale, essere creano la propria rete virtuale. Per determinare se un servizio di Azure crea una propria rete virtuale, vedere le informazioni per ogni [servizio di Azure che può essere distribuito in una rete virtuale](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network).

### <a name="subnets"></a>Subnet

Una rete virtuale può essere segmentata in una o più subnet fino a raggiungere i [limiti](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits). Aspetti da considerare quando si decide se creare una subnet o più reti virtuali in una sottoscrizione:

- Ogni subnet deve disporre di un intervallo di indirizzi univoci, specificato nel formato CIDR all'interno dello spazio di indirizzi della rete virtuale. L'intervallo di indirizzi non può sovrapporsi ad altre subnet all'interno della rete virtuale.
- Se si prevede di distribuire alcune risorse del servizio di Azure in una rete virtuale, potrebbero richiedere, o creare, le proprie subnet, in tal caso ci deve essere abbastanza spazio non allocato per consentire loro di farlo. Per determinare se un servizio di Azure crea una propria subnet, vedere le informazioni per ogni [servizio di Azure che può essere distribuito in una rete virtuale](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network). Ad esempio, se si connette una rete virtuale a una rete locale tramite un Gateway VPN di Azure, la rete virtuale deve avere una subnet dedicata per il gateway. Altre informazioni sulle [subnet del gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Per impostazione predefinita, Azure indirizza il traffico di rete tra tutte le subnet in una rete virtuale. È possibile eseguire l'override di routing per evitare il routing di Azure tra le subnet, oppure per instradare il traffico tra subnet attraverso un' appliance virtuale di rete, ad esempio. Se è necessario che il traffico tra le risorse nella stessa rete virtuale fluisca attraverso un'appliance virtuale di rete (vulnerabilità), distribuire le risorse in subnet diverse. Altre informazioni in [sicurezza](#security).
- È possibile limitare l'accesso alle risorse di Azure, come ad esempio un account di archiviazione di Azure o un database SQL di Azure, per le subnet specifiche con un endpoint servizio di rete virtuale. Inoltre, è possibile negare l'accesso alle risorse da internet. È possibile creare più subnet e abilitare un endpoint del servizio per alcune subnet, ma non altro. Altre informazioni sugli [endpoint del servizio](virtual-network-service-endpoints-overview.md) e sulle risorse di Azure per cui è possibile abilitarli.
- È possibile associare zero o un gruppo di sicurezza di rete ad ogni subnet in una rete virtuale. È possibile associare lo stesso gruppo di protezione o un altro per ogni subnet di rete. Ogni gruppo di sicurezza di rete contiene regole che consentono o negano il traffico da e verso le origini e le destinazioni. Vedere altre informazioni sui [gruppi di sicurezza di rete](#traffic-filtering).

## <a name="security"></a>Sicurezza

È possibile filtrare il traffico di rete da e verso le risorse in una rete virtuale tramite i gruppi di sicurezza di rete e i dispositivi di rete virtuale. È possibile controllare come Azure instrada il traffico proveniente da subnet. È inoltre possibile limitare gli utenti dell'organizzazione che possono usare le risorse nelle reti virtuali.

### <a name="traffic-filtering"></a>Filtro del traffico

- È possibile filtrare il traffico di rete tra le risorse in una rete virtuale tramite un gruppo di sicurezza di rete, una NVA che consente di filtrare il traffico di rete o entrambi. Per distribuire una NVA, ad esempio un firewall, per filtrare il traffico di rete, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1). Quando si usa una vulnerabilità, è anche possibile creare route personalizzate per instradare il traffico dalla subnet alla NVA. Altre informazioni su [routing del traffico](#traffic-routing).
- Un gruppo di sicurezza di rete contiene diverse regole di sicurezza predefinite che consentono o negano il traffico verso o dalle risorse. Un gruppo di sicurezza di rete può essere associato a un'interfaccia di rete, alla subnet in cui si trova l'interfaccia di rete, o ad entrambe. Per semplificare la gestione delle regole di sicurezza, è consigliabile associare un gruppo di sicurezza di rete a subnet singole, anziché le interfacce di rete singole all'interno della subnet, laddove possibile.
- Se diverse macchine virtuali all'interno di una subnet hanno bisogno che ad esse si applichino regole di sicurezza diverse, è possibile associare l'interfaccia di rete nella macchina virtuale a uno o più gruppi di sicurezza dell'applicazione. Una regola di sicurezza può specificare un gruppo di sicurezza delle applicazioni nella propria, nella destinazione, o in entrambe. Tale regola quindi si applica solo alle interfacce di rete che sono membri del gruppo di sicurezza dell'applicazione. Altre informazioni sui [gruppi di sicurezza di rete](security-overview.md) e sui [gruppi di sicurezza dell'applicazione](security-overview.md#application-security-groups).
- Azure crea diverse regole di sicurezza predefinite all'interno di ogni gruppo di sicurezza di rete. Una regola predefinita consente a tutto il traffico di fluire tra tutte le risorse in una rete virtuale. Per eseguire l'override di questo comportamento, usare i gruppi di sicurezza di rete, il routing personalizzato per instradare il traffico a una NVA, o entrambi. È consigliabile acquisire familiarità con tutte le [regole di sicurezza predefinite](security-overview.md#default-security-rules) di Azure e comprendere come vengono applicate le regole del gruppo di sicurezza di rete a una risorsa.

È possibile visualizzare le progettazioni di esempio per l'implementazione di una rete Perimetrale tra Azure e internet tramite una [NVA](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json) oppure dei [gruppi di sicurezza di rete](virtual-networks-dmz-nsg.md).

### <a name="traffic-routing"></a>instradamento del traffico

Azure crea diverse route predefinite per il traffico in uscita da una subnet. È possibile eseguire l'override del routing predefinito di Azure creando una tabella di routing e associandola a una subnet. Dei motivi comuni per eseguire l'override del routing predefinito di Azure sono:
- Perché si vuole che il traffico tra subnet passi attraverso una NVA. Altre informazioni su come [configurare le tabelle di route per forzare il traffico attraverso una NVA](tutorial-create-route-table-portal.md).
- Poiché si desidera forzare tutto il traffico associato a internet tramite un NVA, o in locale, tramite un gateway VPN di Azure. Forzare il traffico associato a internet in locale per l'ispezione e la registrazione è spesso definito come tunneling forzato. Altre informazioni su come configurare il [tunneling forzato](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Se è necessario implementare il routing personalizzato, è consigliabile avere familiarità con [routing in Azure](virtual-networks-udr-overview.md).

## <a name="connectivity"></a>Connettività

È possibile connettere una rete virtuale ad altre reti virtuali tramite il peering di rete virtuale, o alla rete locale, tramite un gateway VPN di Azure.

### <a name="peering"></a>Peering

Quando si usa [peering della rete virtuale](virtual-network-peering-overview.md), le reti virtuali possono trovarsi nella stessa, o in diverse, aree di Azure supportate. Le reti virtuali possono essere nella stessa sottoscrizione di Azure o in una sottoscrizione diversa (persino in sottoscrizioni associate a diversi tenant di Azure Active Directory). Prima di creare un peering, è consigliabile acquisire familiarità con [i requisiti e i vincoli](virtual-network-manage-peering.md#requirements-and-constraints) del peering. La larghezza di banda tra le risorse nelle reti virtuali con peering nella stessa area rimane uguale a quella tra le risorse nella stessa rete virtuale.

### <a name="vpn-gateway"></a>gateway VPN

È possibile usare un [Gateway VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) di Azure per connettere una rete virtuale alla rete locale tramite un [VPN site-to-site](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json), o tramite una connessione dedicata con Azure [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

È possibile combinare il peering e un gateway VPN per creare [reti hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json), in cui le reti virtuali spoke si connettono a una rete virtuale hub e l'hub si connette a una rete locale, ad esempio.

### <a name="name-resolution"></a>Risoluzione dei nomi

Le risorse in una rete virtuale non possono risolvere i nomi delle risorse nella rete virtuale con peering tramite il [DNS predefinito](virtual-networks-name-resolution-for-vms-and-role-instances.md) di Azure. Per risolvere i nomi nelle reti virtuali con peering, [distribuire un server DNS personalizzato](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) oppure usare [domini privati di DNS di Azure](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). La risoluzione dei nomi tra le reti locali e le risorse in una rete virtuale richiede anche di distribuire il proprio server DNS.

## <a name="permissions"></a>Autorizzazioni

Azure usa il [Controllo degli accessi basato sui ruoli](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (RBAC) per le risorse. Le autorizzazioni vengono assegnate a un [ambito](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) nella gerarchia seguente: Sottoscrizione, gruppo di gestione, gruppo di risorse e risorse singole. Per altre informazioni sulla gerarchia, vedere [organizzare le risorse](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Per lavorare con reti virtuali di Azure e con tutte le funzionalità correlate, come ad esempio il peering, i gruppi di sicurezza di rete, gli endpoint del servizio e le tabelle di route, è possibile assegnare i membri dell'organizzazione ai ruoli incorporati [Proprietario](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner), [Collaboratore](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor), o [Collaboratore di rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) e assegnare il ruolo all'ambito appropriato. Se si desidera assegnare autorizzazioni specifiche per un subset delle funzionalità di rete virtuale, creare un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e assegnare le autorizzazioni specifiche necessarie per le [reti virtuali](manage-virtual-network.md#permissions), le [subnet e gli endpoint del servizio](virtual-network-manage-subnet.md#permissions), le [interfacce di rete](virtual-network-network-interface.md#permissions), il [peering](virtual-network-manage-peering.md#permissions), i [gruppi di sicurezza di rete e delle applicazioni](manage-network-security-group.md#permissions), o le [tabelle route](manage-route-table.md#permissions) al ruolo.

## <a name="policy"></a>Criterio

Criteri di Azure consente di creare, assegnare e gestire le definizioni dei criteri. Le definizioni dei criteri applicano regole diverse alle risorse, in modo che le risorse rimangano conformi ai contratti di servizio e agli standard dell'organizzazione. Criteri di Azure esegue una valutazione delle risorse, alla ricerca delle risorse che non sono conformi alle definizioni di criteri specificate. Ad esempio, è possibile definire e applicare un criterio che consente la creazione di reti virtuali solo in un gruppo di risorse o in un'area specifici. Un altro criterio potrebbe richiedere che a tutte le subnet sia associato un gruppo di sicurezza di rete. I criteri vengono quindi valutati durante la creazione e l'aggiornamento delle risorse.

I criteri vengono applicati alla gerarchia seguente: Sottoscrizione, gruppo di gestione e gruppo di risorse. Per altre informazioni su [Criterio di Azure](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o distribuire alcuni esempi di reti virtuali [modello di criteri](policy-samples.md).

## <a name="next-steps"></a>Passaggi successivi

Informazioni su tutte le attività, le impostazioni e le opzioni per una [rete virtuale](manage-virtual-network.md), una [subnet e il servizio endpoint](virtual-network-manage-subnet.md), un'[interfaccia di rete](virtual-network-network-interface.md), il [peering](virtual-network-manage-peering.md), il [gruppo di sicurezza di rete e dell'applicazione](manage-network-security-group.md) o la [tabella di route](manage-route-table.md).
