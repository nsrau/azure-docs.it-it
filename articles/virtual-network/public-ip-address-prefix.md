---
title: Prefisso dell'indirizzo IP pubblico di Azure | Microsoft Docs
description: Informazioni sul prefisso di un indirizzo IP pubblico di Azure e su come consenta di assegnare indirizzi IP pubblici affidabili alle risorse.
services: virtual-network
documentationcenter: na
author: anavinahar
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: anavin
ms.openlocfilehash: 23cd77d4a2d0c8203670039dd44c878bf7217fd3
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65799117"
---
# <a name="public-ip-address-prefix"></a>Prefisso dell'indirizzo IP pubblico

Un prefisso di indirizzo IP pubblico è un intervallo riservato di indirizzi IP per gli endpoint pubblici in Azure. Azure consente di allocare un intervallo contiguo di indirizzi alla sottoscrizione in base al numero specificato. Se non si ha familiarità con gli indirizzi pubblici, consultare [Indirizzi IP pubblici.](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)

Gli indirizzi IP pubblici vengono assegnati da un pool di indirizzi assegnati a ogni area di Azure. È possibile [scaricare](https://www.microsoft.com/download/details.aspx?id=56519) l'elenco degli intervalli di Azure usati per ogni area. Ad esempio, 40.121.0.0/16 è uno degli oltre 100 intervalli che usa Azure nell'area degli Stati Uniti orientali. L'intervallo include gli indirizzi utilizzabili dei 40.121.0.1 - 40.121.255.254.

È possibile creare un prefisso di indirizzo IP pubblico in un'area di Azure e una sottoscrizione, specificando un nome e il numero di indirizzi che si desiderano includere nel prefisso. Ad esempio, se si crea un prefisso di indirizzo IP pubblico di /28, Azure alloca 16 indirizzi da uno dei relativi intervalli per l'utente. Non si conosce l'intervallo che Azure assegnerà fino a quando non lo si crea, ma gli indirizzi sono contigui. I prefissi di indirizzi IP pubblici prevedono il pagamento di un corrispettivo. Per informazioni dettagliate, consultare [prezzi degli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Perché creare un prefisso di indirizzo IP pubblico?

Quando si creano risorse di indirizzi IP pubblici, Azure assegna un indirizzo IP pubblico disponibile da tutti gli intervalli usati in un'area. Si conosce l'indirizzo una volta che Azure lo assegna; tuttavia, fino a quando ciò non avviene, non si sa quale indirizzo potrebbe essere assegnato. Ciò può costituire un problema quando, ad esempio, l'utente o i partner commerciali configurano le regole del firewall che consentono indirizzi IP specifici. Ogni volta che si assegna un nuovo indirizzo IP pubblico a una risorsa, l'indirizzo dovrà essere aggiunto alla regola del firewall. Quando si assegnano gli indirizzi alle risorse da un prefisso di indirizzo IP pubblico, non è necessario aggiornare le regole del firewall ogni volta che si assegna uno degli indirizzi, perché l'intero intervallo potrebbe essere aggiunto a una regola.

## <a name="benefits"></a>Vantaggi

- È possibile creare risorse di indirizzi IP pubblici da un intervallo noto.
- L'utente o i partner commerciali possono creare regole firewall con intervalli che includono gli indirizzi IP pubblici attualmente assegnati, nonché indirizzi che non sono stati ancora assegnati. Ciò elimina la necessità di modificare le regole del firewall quando si assegnano gli indirizzi IP alle nuove risorse.
- La dimensione predefinita di un intervallo che è possibile creare riguarda indirizzi IP /28 o 16.
- Non sono previsti limiti per quanto riguarda il numero di intervalli che è possibile creare, tuttavia, sono previsti limiti al numero massimo di indirizzi IP pubblici statici che è possibile avere in una sottoscrizione di Azure. Di conseguenza, il numero di intervalli creati non può includere più indirizzi IP pubblici statici di quanti sono disponibili nella sottoscrizione. Per altre informazioni, consultare [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Gli indirizzi che si creano usando quelli del prefisso possono essere assegnati a qualsiasi risorsa di Azure alla quale è possibile assegnare un indirizzo IP pubblico.
- È possibile visualizzare facilmente quali indirizzi IP sono allocati e quali non all'interno dell'intervallo.

## <a name="scenarios"></a>Scenari
È possibile associare le risorse seguenti a un indirizzo IP pubblico statico da un prefisso:

|Resource|Scenario|Passaggi|
|---|---|---|
|Macchine virtuali| L'associazione di IP pubblici da un prefisso alle macchine virtuali in Azure riduce il sovraccarico di gestione quando si tratta di inserire nell'elenco elementi consentiti gli indirizzi IP di un firewall. È possibile consentire un intero prefisso con una singola regola del firewall. Quando si scala con macchine virtuali in Azure, è possibile associare gli indirizzi IP dallo stesso prefisso risparmiando costi, tempi e spese generali di gestione.| Per associare gli IP da un prefisso alla macchina virtuale: 1. [Creare un prefisso.](manage-public-ip-address-prefix.md) 2. [Creare un indirizzo IP dal prefisso.](manage-public-ip-address-prefix.md) 3. [Associare l'indirizzo IP all'interfaccia di rete della macchina virtuale.](virtual-network-network-interface-addresses.md#add-ip-addresses)
| Servizi di bilanciamento del carico | L'associazione di indirizzi IP pubblici da un prefisso alla configurazione IP del front-end o la regola in uscita di un Load Balancer garantisce la semplificazione dello spazio di indirizzi IP pubblici di Azure. È possibile semplificare lo scenario preparando le connessioni in uscita in modo che siano originate da un intervallo di indirizzi IP contigui definito dal prefisso IP pubblico. | Per associare gli indirizzi IP da un prefisso a un Load Balancer: 1. [Creare un prefisso.](manage-public-ip-address-prefix.md) 2. [Creare un indirizzo IP dal prefisso.](manage-public-ip-address-prefix.md) 3. Quando si crea il Load Balancer, selezionare o aggiornare l'indirizzo IP creato nel passaggio 2 precedente come indirizzo IP front-end del Load Balancer. |
| Firewall di Azure | È possibile usare un indirizzo IP pubblico da un prefisso per lo SNAT in uscita. Ciò significa che tutto il traffico della rete virtuale in uscita viene traslato nell'indirizzo IP pubblico [Firewall di Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Poiché questo indirizzo IP proviene da un prefisso predeterminato, è molto semplice sapere in anticipo quale sarà l'aspetto del footprint dell'IP pubblico in Azure. | 1. [Creare un prefisso.](manage-public-ip-address-prefix.md) 2. [Creare un indirizzo IP dal prefisso.](manage-public-ip-address-prefix.md) 3. Quando si [distribuisce il firewall di Azure](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), assicurarsi di selezionare l'indirizzo IP allocato in precedenza dal prefisso.|

## <a name="constraints"></a>Vincoli

- Non è possibile specificare gli indirizzi IP per il prefisso. Azure alloca gli indirizzi IP per il prefisso, in base alla dimensione specificata.
- È possibile creare un prefisso di un massimo di 16 indirizzi IP o/28. Per altre informazioni, consultare [limiti di Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).
- Una volta creato il prefisso,non è possibile modificare l'intervallo.
- L'intervallo è solo per gli indirizzi IPv4. L'intervallo non contiene indirizzi IPv6.
- Solo gli indirizzi IP pubblici statici creati con lo SKU Standard possono essere assegnati dall'intervallo del prefisso. Per altre informazioni sugli indirizzi IP pubblici di SKU, consultare [Indirizzi IP pubblici](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses).
- Gli indirizzi dell'intervallo possono essere assegnati solo alle risorse di Azure Resource Manager. Gli indirizzi non possono essere assegnati alle risorse nel modello di distribuzione classica.
- Tutti gli indirizzi IP pubblici creati dal prefisso devono esistere nella stessa area di Azure e di sottoscrizione come prefisso e devono essere assegnati alle risorse nella stessa area e sottoscrizione.
- Non è possibile eliminare un prefisso se tutti gli indirizzi in esso contenuti sono assegnati a risorse di indirizzi IP pubblici associate a una risorsa. In primo luogo, annullare l'associazione di tutte le risorse di indirizzi IP pubblici assegnati dal prefisso.


## <a name="next-steps"></a>Passaggi successivi

- [Creare](manage-public-ip-address-prefix.md) un prefisso di indirizzo IP pubblico
