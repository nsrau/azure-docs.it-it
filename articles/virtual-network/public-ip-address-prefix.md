---
title: Prefisso dell'indirizzo IP pubblico di Azure
description: Informazioni sul prefisso di un indirizzo IP pubblico di Azure e su come consenta di assegnare indirizzi IP pubblici affidabili alle risorse.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 53dd6d2dda762b3cbf53f4aaec6cd3692a9656e9
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432582"
---
# <a name="public-ip-address-prefix"></a>Prefisso di indirizzo IP pubblico

Un prefisso di indirizzo IP pubblico è un intervallo riservato di indirizzi IP in Azure. Azure fornisce un intervallo contiguo di indirizzi alla sottoscrizione in base al numero specificato. 

Se non si ha familiarità con gli indirizzi pubblici, consultare [Indirizzi IP pubblici.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Gli indirizzi IP pubblici vengono assegnati da un pool di indirizzi assegnati a ogni area di Azure. È possibile [scaricare](https://www.microsoft.com/download/details.aspx?id=56519) l'elenco degli intervalli di Azure usati per ogni area. Ad esempio, 40.121.0.0/16 è uno degli oltre 100 intervalli che usa Azure nell'area degli Stati Uniti orientali. L'intervallo include gli indirizzi utilizzabili dei 40.121.0.1 - 40.121.255.254.

È possibile creare un prefisso di indirizzo IP pubblico in un'area di Azure e una sottoscrizione, specificando un nome e il numero di indirizzi che si desiderano includere nel prefisso. 

Gli intervalli di indirizzi IP pubblici vengono assegnati con un prefisso a scelta. Se si crea un prefisso pari a/28, Azure fornisce 16 indirizzi IP da uno degli intervalli.

Non si conosce l'intervallo che Azure assegnerà fino a quando non lo si crea, ma gli indirizzi sono contigui. 

Per ulteriori informazioni sui prefissi degli indirizzi IP pubblici, vedere prezzi per gli [indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Perché creare un prefisso di indirizzo IP pubblico?

Quando si creano risorse indirizzo IP pubblico, Azure assegna un indirizzo IP pubblico disponibile da qualsiasi intervallo usato in tale area. 

Fino a quando Azure assegna l'indirizzo IP, non si conosce l'indirizzo IP esatto. Questo processo può risultare problematico quando si creano regole del firewall che consentono indirizzi IP specifici. Per ogni indirizzo IP aggiunto è necessario aggiungere una regola del firewall corrispondente.

Quando si assegnano indirizzi alle risorse da un prefisso di indirizzo IP pubblico, gli aggiornamenti delle regole del firewall non sono necessari. L'intero intervallo viene aggiunto alla regola.

## <a name="benefits"></a>Vantaggi

- Creazione di risorse indirizzo IP pubblico da un intervallo noto.
- Configurazione della regola del firewall con intervalli che includono indirizzi IP pubblici attualmente assegnati e indirizzi non ancora assegnati. Questa configurazione Elimina la necessità di modificare le regole del firewall quando si assegnano indirizzi IP a nuove risorse.
- La dimensione predefinita di un intervallo che è possibile creare riguarda indirizzi IP /28 o 16.
- Non sono previsti limiti per il numero di intervalli che è possibile creare. Sono previsti limiti per il numero massimo di indirizzi IP pubblici statici che è possibile avere in una sottoscrizione di Azure. Il numero di intervalli creati non può includere più indirizzi IP pubblici statici rispetto a quelli consentiti nella sottoscrizione. Per altre informazioni, consultare [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Gli indirizzi che si creano usando quelli del prefisso possono essere assegnati a qualsiasi risorsa di Azure alla quale è possibile assegnare un indirizzo IP pubblico.
- È possibile visualizzare facilmente gli indirizzi IP specificati e non specificati nell'intervallo.

## <a name="scenarios"></a>Scenari
È possibile associare le risorse seguenti a un indirizzo IP pubblico statico da un prefisso:

|Risorsa|Scenario|Passaggi|
|---|---|---|
|Macchine virtuali| L'associazione di indirizzi IP pubblici da un prefisso alle macchine virtuali in Azure riduce il sovraccarico di gestione quando si aggiungono indirizzi IP a un elenco Consenti nel firewall. È possibile aggiungere un prefisso intero con una singola regola del firewall. Quando si scala con macchine virtuali in Azure, è possibile associare gli indirizzi IP dallo stesso prefisso risparmiando costi, tempi e spese generali di gestione.| Per associare gli IP da un prefisso alla macchina virtuale: </br> 1. [creare un prefisso.](manage-public-ip-address-prefix.md) </br> 2. [creare un IP dal prefisso.](manage-public-ip-address-prefix.md) </br> 3. [associare l'indirizzo IP all'interfaccia di rete della macchina virtuale.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> È anche possibile [associare gli indirizzi IP a un set di scalabilità di macchine virtuali](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Bilanciamento del carico standard | L'associazione di indirizzi IP pubblici da un prefisso alla configurazione IP front-end o alla regola in uscita di un servizio di bilanciamento del carico garantisce la semplificazione dello spazio degli indirizzi IP pubblici di Azure. Semplifica lo scenario tramite la pulitura delle connessioni in uscita da un intervallo di indirizzi IP contigui. | Per associare gli IP da un prefisso al servizio di bilanciamento del carico: </br> 1. [creare un prefisso.](manage-public-ip-address-prefix.md) </br> 2. [creare un IP dal prefisso.](manage-public-ip-address-prefix.md) </br> 3. quando si crea il servizio di bilanciamento del carico, selezionare o aggiornare l'indirizzo IP creato nel passaggio 2 precedente come IP front-end del servizio di bilanciamento del carico. |
| Firewall di Azure | È possibile usare un indirizzo IP pubblico da un prefisso per lo SNAT in uscita. Tutto il traffico di rete virtuale in uscita viene convertito nell'IP pubblico del [firewall di Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . | Per associare un indirizzo IP da un prefisso al firewall: </br> 1. [creare un prefisso.](manage-public-ip-address-prefix.md) </br> 2. [creare un IP dal prefisso.](manage-public-ip-address-prefix.md) </br> 3. quando si [distribuisce il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), assicurarsi di selezionare l'indirizzo IP assegnato in precedenza dal prefisso.|
| Gateway applicazione V2 | È possibile usare un indirizzo IP pubblico da un prefisso per la scalabilità automatica e il gateway applicazione con ridondanza della zona V2. | Per associare un indirizzo IP da un prefisso al gateway: </br> 1. [creare un prefisso.](manage-public-ip-address-prefix.md) </br> 2. [creare un IP dal prefisso.](manage-public-ip-address-prefix.md) </br> 3. quando si [distribuisce il gateway applicazione](../application-gateway/quick-create-portal.md#create-an-application-gateway), assicurarsi di selezionare l'indirizzo IP assegnato in precedenza dal prefisso.|

## <a name="constraints"></a>Vincoli

- Non è possibile specificare gli indirizzi IP per il prefisso. Azure fornisce gli indirizzi IP per il prefisso, in base alle dimensioni specificate.
- Per impostazione predefinita, è possibile creare un prefisso di un massimo di 16 indirizzi IP o a/28. Per ulteriori informazioni, esaminare [le richieste di aumento dei limiti di rete](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests) e i [limiti di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .
- Una volta creato il prefisso,non è possibile modificare l'intervallo.
- Solo gli indirizzi IP pubblici statici creati con lo SKU Standard possono essere assegnati dall'intervallo del prefisso. Per altre informazioni sugli indirizzi IP pubblici di SKU, consultare [Indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Gli indirizzi dell'intervallo possono essere assegnati solo alle risorse di Azure Resource Manager. Non è possibile assegnare gli indirizzi alle risorse nel modello di distribuzione classica.
- Tutti gli indirizzi IP pubblici creati dal prefisso devono esistere nella stessa area di Azure e nella stessa sottoscrizione del prefisso. Gli indirizzi devono essere assegnati alle risorse nella stessa area e nella stessa sottoscrizione.
- Non è possibile eliminare un prefisso se tutti gli indirizzi in esso contenuti sono assegnati a risorse di indirizzi IP pubblici associate a una risorsa. In primo luogo, annullare l'associazione di tutte le risorse di indirizzi IP pubblici assegnati dal prefisso.


## <a name="next-steps"></a>Passaggi successivi

- [Creare](manage-public-ip-address-prefix.md) un prefisso di indirizzo IP pubblico
