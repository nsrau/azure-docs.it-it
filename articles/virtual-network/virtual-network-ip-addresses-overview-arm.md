---
title: Tipi di indirizzo IP in Azure | Documentazione Microsoft
description: Informazioni sugli indirizzi IP pubblici e privati in Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 610b911c-f358-4cfe-ad82-8b61b87c3b7e
ms.service: virtual-network
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2017
ms.author: jdial
ms.openlocfilehash: d243455be9439a686ecdf6dfa3aadf2802a0714d
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2017
---
# <a name="ip-address-types-and-allocation-methods-in-azure"></a>Tipi di indirizzi IP e metodi di allocazione in Azure

È possibile assegnare gli indirizzi IP alle risorse di Azure per comunicare con altre risorse di Azure, con la rete locale e con Internet. In Azure è possibile usare due tipi di indirizzi IP:

* **Indirizzi IP pubblici**: usati per la comunicazione con Internet, compresi i servizi pubblici di Azure.
* **Indirizzi IP privati**: usati per la comunicazione all'interno di una rete virtuale Azure e della rete locale quando si usa un gateway VPN o un circuito ExpressRoute per estendere la rete ad Azure.

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  Questo articolo illustra l'uso del modello di distribuzione Resource Manager che Microsoft consiglia di usare invece del [modello di distribuzione classica](virtual-network-ip-addresses-overview-classic.md) per le distribuzioni più recenti.
> 

Se si ha familiarità con il modello di distribuzione classica, verificare le [differenze tra gli indirizzi IP nella versione classica e in Resource Manager](virtual-network-ip-addresses-overview-classic.md#differences-between-resource-manager-and-classic-deployments).

## <a name="public-ip-addresses"></a>Indirizzi IP pubblici

Gli indirizzi IP pubblici consentono alle risorse di Azure di comunicare con Internet e i servizi pubblici di Azure, ad esempio [Cache Redis di Azure](https://azure.microsoft.com/services/cache), [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs), i [database SQL](../sql-database/sql-database-technical-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Archiviazione di Azure](../storage/common/storage-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Gestione risorse di Azure un [indirizzo IP pubblico](virtual-network-public-ip-address.md) è una risorsa che ha proprietà specifiche. Alcune delle risorse a cui è possibile associare una risorsa indirizzo IP pubblico sono:

* Interfacce di rete di macchina virtuale
* Servizi di bilanciamento del carico con connessione Internet
* Gateway VPN
* Gateway di applicazione

### <a name="ip-address-version"></a>Versione dell'indirizzo IP

Gli indirizzi IP pubblici vengono creati con un indirizzo IPv4 o IPv6. Gli indirizzi IPv6 pubblici possono essere assegnati solo a servizi di bilanciamento del carico con connessione Internet.

### <a name="sku"></a>SKU

Gli indirizzi IP pubblici vengono creati con uno degli SKU seguenti:

#### <a name="basic"></a>Basic

Tutti gli indirizzi IP pubblici creati prima dell'introduzione degli SKU sono indirizzi IP pubblici con SKU Basic. Con l'introduzione degli SKU, è possibile specificare lo SKU per l'indirizzo IP pubblico. Gli indirizzi con SKU Basic sono:

- Assegnati con il metodo di allocazione statica o dinamica.
- Assegnati a qualsiasi risorsa di Azure a cui può essere assegnato un indirizzo IP pubblico, ad esempio interfacce di rete, gateway VPN e servizi di bilanciamento del carico con connessione Internet.
- Assegnabili a una zona specifica.
- Senza ridondanza della zona. Per altre informazioni sulle zone di disponibilità, vedere [Panoramica delle zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="standard"></a>Standard

Gli indirizzi IP pubblici con SKU Standard sono:

- Assegnati solo con il metodo di allocazione statica.
- Assegnate a interfacce di rete o servizi di bilanciamento del carico con connessione Internet standard. Per altre informazioni sugli SKU di Azure Load Balancer, vedere [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU Standard di Azure Load Balancer).
- Con ridondanza della zona per impostazione predefinita. Possono essere creati come risorsa di zona e garantiti in una zona di disponibilità specifica.  Per altre informazioni sulle zone di disponibilità, vedere [Panoramica di Zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
 
> [!NOTE]
> Quando si assegna un indirizzo IP pubblico con SKU Standard all'interfaccia di rete di una macchina virtuale, è necessario consentire in modo esplicito il traffico previsto con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups).  La comunicazione con la risorsa non riesce finché non si crea e si associa un gruppo di sicurezza di rete e si consente in modo esplicito il traffico desiderato.

Lo SKU Standard è in versione di anteprima. Prima di creare un indirizzo IP pubblico con SKU Standard, è necessario eseguire la registrazione per l'anteprima e creare l'indirizzo in una località supportata. Per eseguire la registrazione per l'anteprima, vedere [Eseguire la registrazione per l'anteprima dello SKU Standard](virtual-network-public-ip-address.md#register-for-the-standard-sku-preview). Per un elenco delle posizioni (aree) supportate, vedere [Region availability](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region-availability) (Disponibilità a livello di area) e monitorare la pagina [Aggiornamenti di Rete virtuale di Azure](https://azure.microsoft.com/updates/?product=virtual-network) per altro supporto sulle aree.


### <a name="allocation-method"></a>Metodo di allocazione

Gli indirizzi IP vengono allocati a una risorsa indirizzo IP pubblico con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è *dinamico*, dove un indirizzo IP **non** viene allocato al momento della creazione. Al contrario, l'indirizzo IP pubblico viene allocato quando si avvia, o si crea, la risorsa associata, ad esempio il servizio di bilanciamento del carico o una VM. L'indirizzo IP viene rilasciato quando si arresta o si elimina la risorsa. Dopo essere stato rilasciato dalla risorsa A, ad esempio, l'indirizzo IP può essere assegnato a un'altra risorsa. Se l'indirizzo IP viene assegnato a un'altra risorsa mentre la risorsa A è arrestata, quando si riavvia la risorsa A, viene assegnato un indirizzo IP diverso.

Per assicurare che l'indirizzo IP per la risorsa associata rimanga invariato, è possibile impostare in modo esplicito il metodo di allocazione su *statico*. Un indirizzo IP statico viene assegnato immediatamente. L'indirizzo viene rilasciato solo quando si elimina la risorsa o modifica il relativo metodo di allocazione in *dinamico*.

> [!NOTE]
> Anche quando si imposta il metodo di allocazione su *statico*, non è possibile specificare l'indirizzo IP effettivo assegnato alla risorsa indirizzo IP pubblico. Azure assegna l'indirizzo IP da un pool di indirizzi IP disponibili nella località di Azure in cui viene creata la risorsa.
>

Gli indirizzi IP pubblici statici sono comunemente usati negli scenari seguenti:

* Quando è necessario aggiornare le regole del firewall per comunicare con le risorse di Azure.
* La risoluzione del nome DNS, in cui una modifica dell'indirizzo IP richiederebbe l'aggiornamento dei record A.
* Le risorse di Azure comunicano con altri servizi o altre app che usano il modello di sicurezza basato su indirizzi IP.
* Si usano certificati SSL collegati a un indirizzo IP.

> [!NOTE]
> Azure assegna gli indirizzi IP pubblici da un intervallo univoco a ogni area di Azure. Per informazioni dettagliate, vedere [Intervalli IP del data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).
>

### <a name="dns-hostname-resolution"></a>Risoluzione del nome host DNS
È possibile specificare un'etichetta del nome di dominio DNS per una risorsa IP pubblica, che crea un mapping per *etichettanomedominio*.*località*.cloudapp.azure.com per l'indirizzo IP pubblico nei server DNS gestiti di Azure. Ad esempio, se si crea una risorsa IP pubblica con **contoso** come *etichettanomedominio* nella *località* di Azure **Stati Uniti occidentali**, il nome di dominio completo (FQDN) **contoso.westus.cloudapp.azure.com** restituisce l'indirizzo IP pubblico della risorsa. È possibile usare il nome di dominio completo per creare un record CNAME di dominio personalizzato che punta all'indirizzo IP pubblico in Azure.

> [!IMPORTANT]
> Ogni etichetta di nome di dominio creata deve essere univoca nella relativa posizione di Azure.  
>

### <a name="virtual-machines"></a>Macchine virtuali

È possibile associare un indirizzo IP pubblico a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) assegnandola alla relativa **interfaccia di rete**. A una macchina virtuale è possibile assegnare un indirizzo IP pubblico statico o dinamico. Per altre informazioni sull'assegnazione di indirizzi IP all'interfaccia di rete, vedere [Aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete di Azure](virtual-network-network-interface-addresses.md).

### <a name="internet-facing-load-balancers"></a>Servizi di bilanciamento del carico con connessione Internet

È possibile associare un indirizzo IP pubblico creato con uno dei due [SKU](#SKU) a un'istanza di [Azure Load Balancer](../load-balancer/load-balancer-overview.md) assegnandolo alla configurazione **front-end** del servizio di bilanciamento del carico. L'indirizzo IP pubblico viene usato come indirizzo IP virtuale (indirizzo VIP) di bilanciamento del carico. A un servizio di bilanciamento del carico front-end è possibile assegnare un indirizzo IP pubblico statico o dinamico. È anche possibile assegnare più indirizzi IP pubblici a un front-end del servizio di bilanciamento del carico, consentendo così scenari con [più indirizzi VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), ad esempio un ambiente multi-tenant con siti Web basati su SSL. Per altre informazioni sugli SKU di Azure Load Balancer, vedere [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU Standard di Azure Load Balancer).

### <a name="vpn-gateways"></a>Gateway VPN

Un [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) connette una rete virtuale di Azure ad altre reti virtuali o locali di Azure. Un indirizzo IP pubblico viene assegnato al gateway VPN per abilitarne la comunicazione con la rete remota. A un gateway VPN è possibile assegnare solo un indirizzo IP pubblico *dinamico*.

### <a name="application-gateways"></a>Gateway di applicazione

È possibile associare un indirizzo IP pubblico a un [gateway applicazione](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)di Azure, assegnandolo alla configurazione **front-end** del gateway. Questo indirizzo IP pubblico viene usato come indirizzo VIP con carico bilanciato. Alla configurazione front-end di un gateway applicazione è possibile assegnare solo un indirizzo IP pubblico *dinamico*.

### <a name="at-a-glance"></a>Riepilogo
La tabella seguente illustra la proprietà specifica tramite la quale un indirizzo IP pubblico può essere associato a una risorsa di livello superiore e i metodi di allocazione possibili (dinamici o statici) utilizzabili.

| Risorse di livello superiore | Associazione di indirizzi IP | dinamico | statico |
| --- | --- | --- | --- |
| Macchine virtuali |interfaccia di rete |Sì |Sì |
| Servizio di bilanciamento del carico con connessione Internet |Configurazione front-end |Sì |Sì |
| Gateway VPN |Configurazione IP del gateway |Sì |No |
| gateway applicazione |Configurazione front-end |Sì |No |

## <a name="private-ip-addresses"></a>Indirizzi IP privati
Gli indirizzi IP privati consentono alle risorse di Azure di comunicare con altre risorse in una [rete virtuale](virtual-networks-overview.md) o in una rete locale tramite un gateway VPN o un circuito ExpressRoute, senza usare un indirizzo IP raggiungibile tramite Internet.

Nel modello di distribuzione Azure Resource Manager un indirizzo IP privato è associato ai tipi di risorse di Azure seguenti:

* Interfacce di rete di macchina virtuale
* Servizi di bilanciamento del carico interno
* Gateway di applicazione

### <a name="ip-address-version"></a>Versione dell'indirizzo IP

Gli indirizzi IP privati vengono creati con un indirizzo IPv4 o IPv6. Gli indirizzi IPv6 privati possono essere assegnati solo con il metodo di allocazione dinamica. Non è possibile comunicare tra gli indirizzi IPv6 privati in una rete virtuale. Per la comunicazione in ingresso verso un indirizzo IPv6 privato da Internet, è possibile usare un servizio di bilanciamento del carico con connessione Internet. Per informazioni dettagliate, vedere [Creare un servizio di bilanciamento del carico Internet con IPv6](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="allocation-method"></a>Metodo di allocazione

Un indirizzo IP privato viene allocato dall'intervallo di indirizzi della subnet a cui è collegata la risorsa. L'intervallo di indirizzi della subnet stessa fa parte dell'intervallo di indirizzi della rete virtuale.

Un indirizzo IP privato viene allocato con due metodi: *dinamico* o *statico*. Il metodo di allocazione predefinito è quello *dinamico*, in base al quale l'indirizzo IP viene allocato automaticamente dalla subnet della risorsa usando DHCP. Questo indirizzo IP può cambiare quando si arresta e avvia la risorsa.

È possibile impostare il metodo di allocazione su *statico* per garantire che l'indirizzo IP rimanga invariato. Quando si specifica *statico*, si specifica un indirizzo IP valido che fa parte della subnet della risorsa.

Gli indirizzi IP privati statici vengono comunemente usati per:

* Macchine virtuali che fungono da controller di dominio o server DNS.
* Risorse che richiedono regole del firewall basate su indirizzi IP.
* Risorse accessibili da altre app o risorse tramite un indirizzo IP.

### <a name="virtual-machines"></a>Macchine virtuali

Un indirizzo IP privato viene assegnato all'**interfaccia di rete** di una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se la macchina virtuale ha più interfacce di rete, un indirizzo IP privato viene assegnato a ogni interfaccia di rete. Per un'interfaccia di rete è possibile specificare un metodo di allocazione statico o dinamico.

#### <a name="internal-dns-hostname-resolution-for-virtual-machines"></a>Risoluzione del nome host DNS interno per le macchine virtuali

Tutte le macchine virtuali di Azure sono configurate con [server DNS gestiti da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) per impostazione predefinita, a meno che non si configurino in modo esplicito server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interna per le macchine virtuali che risiedono nella stessa rete virtuale.

Quando si crea una macchina virtuale, ai server DNS gestiti da Azure viene aggiunto un mapping del nome host al relativo indirizzo IP privato. Se si usa una macchina virtuale con più interfacce di rete, viene eseguito il mapping del nome host all'indirizzo IP privato dell'interfaccia di rete primaria.

Le macchine virtuali configurate con server DNS gestiti di Azure possono risolvere i nomi host di tutte le macchine virtuali nella stessa rete virtuale nei relativi indirizzi IP privati.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Servizi di bilanciamento del carico interno e gateway applicazione

È possibile assegnare un indirizzo IP privato alla configurazione **front-end** di un [servizio di bilanciamento del carico interno di Azure](../load-balancer/load-balancer-internal-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o di un [gateway applicazione di Azure](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo indirizzo IP privato funge da endpoint interno, accessibile solo alle risorse all'interno della rete virtuale e alle reti remote connesse alla rete virtuale. È possibile assegnare un indirizzo IP privato dinamico o statico alla configurazione front-end.

### <a name="at-a-glance"></a>Riepilogo
La tabella seguente illustra la proprietà specifica tramite la quale un indirizzo IP privato può essere associato a una risorsa di livello superiore e i metodi di allocazione possibili (dinamici o statici) utilizzabili.

| Risorse di livello superiore | Associazione di indirizzi IP | dinamico | statico |
| --- | --- | --- | --- |
| Macchine virtuali |interfaccia di rete |Sì |Sì |
| Bilanciamento del carico |Configurazione front-end |Sì |Sì |
| gateway applicazione |Configurazione front-end |Sì |Sì |

## <a name="limits"></a>Limiti
I limiti imposti agli indirizzi IP sono indicati nel set completo di [limiti della rete](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. I limiti sono classificati per area e per sottoscrizione. È possibile [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti fino ai massimi consentiti in base alle esigenze aziendali.

## <a name="pricing"></a>Prezzi
Per gli indirizzi IP pubblici può essere previsto un addebito nominale. Per altre informazioni sui prezzi degli indirizzi IP in Azure, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire una VM con un IP pubblico statico tramite il portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)
* [Distribuire una VM con un IP pubblico statico tramite un modello](virtual-network-deploy-static-pip-arm-template.md)
* [Come impostare un indirizzo IP statico privato nel portale di Azure](virtual-networks-static-private-ip-arm-pportal.md)
