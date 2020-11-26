---
title: Indirizzi IP pubblici in Azure
titlesuffix: Azure Virtual Network
description: Informazioni sugli indirizzi IP pubblici in Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/28/2020
ms.author: allensu
ms.openlocfilehash: ef79844cf2f90ce97ea30a1948a441f909255f98
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169934"
---
# <a name="public-ip-addresses"></a>Indirizzi IP pubblici

Gli indirizzi IP pubblici consentono la comunicazione in ingresso dalle risorse Internet alle risorse di Azure, Gli indirizzi IP pubblici consentono alle risorse di Azure di comunicare con Internet e con servizi di Azure pubblici. L'indirizzo è dedicato alla risorsa, fino a quando non viene assegnato dall'utente. Una risorsa senza un indirizzo IP pubblico assegnato può comunicare in uscita. Azure assegna dinamicamente un indirizzo IP disponibile che non è dedicato alla risorsa. Per altre informazioni sulle connessioni in uscita, vedere [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Gestione risorse di Azure un [indirizzo IP pubblico](virtual-network-public-ip-address.md) è una risorsa che ha proprietà specifiche. Di seguito sono riportate alcune delle risorse a cui è possibile associare una risorsa indirizzo IP pubblico:

* Interfacce di rete di macchina virtuale
* Servizi di bilanciamento del carico con connessione Internet
* Gateway VPN
* Gateway applicazione
* Firewall di Azure

## <a name="ip-address-version"></a>Versione dell'indirizzo IP

Gli indirizzi IP pubblici vengono creati con un indirizzo IPv4 o IPv6. 

## <a name="sku"></a>SKU

Per informazioni sull'aggiornamento dello SKU, vedere [aggiornamento dell'indirizzo IP pubblico](../virtual-network/virtual-network-public-ip-address-upgrade.md).

Gli indirizzi IP pubblici vengono creati con uno degli SKU seguenti:

>[!IMPORTANT]
> Gli SKU corrispondenti sono necessari per il servizio di bilanciamento del carico e le risorse IP pubbliche. Non è possibile avere una combinazione di risorse SKU Basic e Standard. Non è possibile collegare le macchine virtuali autonome, le macchine virtuali in una risorsa del set di disponibilità, o una risorsa di un set di scalabilità di macchina virtuale per entrambi gli SKU contemporaneamente.  Per i nuovi progetti è consigliabile prendere in considerazione l'uso di risorse SKU Standard.  Per informazioni dettagliate, consultare [Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="standard"></a>Standard

Indirizzi IP pubblici con SKU standard:

- Assegnati sempre con il metodo di allocazione statica.
- Caratterizzati da un timeout di inattività per i flussi in ingresso modificabile di 4-30 minuti, con un valore predefinito di 4 minuti, e da un timeout di inattività per i flussi in uscita fisso di 4 minuti.
- Sicurezza per impostazione predefinita e chiusa al traffico in ingresso. Consentire il traffico in ingresso elenco con un [gruppo di sicurezza di rete](security-overview.md#network-security-groups).
- Assegnati a interfacce di rete, bilanciamenti del carico pubblico standard o gateway applicazione. Per altre informazioni sul servizio di bilanciamento del carico standard, vedere [Load Balancer standard di Azure](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Può essere con ridondanza della zona (pubblicizzato da tutte e 3 le zone) o di zona (è possibile creare una zona e garantirla in una zona di disponibilità specifica). Per altre informazioni sulle zone di disponibilità, vedere [Panoramica delle zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Load Balancer Standard e zone di disponibilità](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json). **Gli IP con ridondanza della zona possono essere creati solo in aree in cui sono attive [3 zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-region) .** Gli IP creati prima che le zone siano Live non saranno con ridondanza della zona.
 
> [!NOTE]
> La comunicazione in ingresso con una risorsa SKU Standard non riesce finché non si crea e si associa un [gruppo di sicurezza di rete](security-overview.md#network-security-groups) e si consente in modo esplicito il traffico in ingresso.

> [!NOTE]
> Quando si usa il [servizio metadati dell'istanza](../virtual-machines/windows/instance-metadata-service.md), sono disponibili solo indirizzi IP pubblici con SKU di base IMDS. Lo SKU standard non è supportato.

### <a name="basic"></a>Basic

Tutti gli indirizzi IP pubblici creati prima dell'introduzione degli SKU sono indirizzi IP pubblici con SKU Basic. 

Con l'introduzione degli SKU, specificare lo SKU da usare come indirizzo IP pubblico. 

Indirizzi SKU Basic:

- Assegnati con il metodo di allocazione statica o dinamica.
- Caratterizzati da un timeout di inattività per i flussi in ingresso modificabile di 4-30 minuti, con un valore predefinito di 4 minuti, e da un timeout di inattività per i flussi in uscita fisso di 4 minuti.
- Sono aperti per impostazione predefinita.  I gruppi di sicurezza di rete sono consigliati, ma facoltativi, per limitare il traffico in ingresso o in uscita.
- Assegnati a qualsiasi risorsa di Azure a cui è possibile assegnare un indirizzo IP pubblico, ad esempio:
    * Interfacce di rete
    * Gateway VPN
    * Gateway applicazione
    * Bilanciamento del carico pubblico
- Non supportano scenari di zona di disponibilità. Usare l'IP pubblico dello SKU standard per gli scenari di zona di disponibilità. Per altre informazioni sulle zone di disponibilità, vedere [Panoramica delle zone di disponibilità](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [Load Balancer Standard e zone di disponibilità](../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="allocation-method"></a>Metodo di allocazione

Gli indirizzi IP pubblici Basic e standard supportano l'assegnazione **statica** .  Alla risorsa viene assegnato un indirizzo IP al momento della creazione. L'indirizzo IP viene rilasciato quando la risorsa viene eliminata.

Gli indirizzi IP pubblici dello SKU Basic supportano un'assegnazione **dinamica** . Dynamic è il metodo di assegnazione predefinito. Quando si seleziona Dynamic, l'indirizzo IP **non** viene assegnato alla risorsa al momento della creazione.

L'IP viene assegnato quando si associa la risorsa indirizzo IP pubblico a un:

* Macchina virtuale 
* La prima macchina virtuale è associata al pool back-end di un servizio di bilanciamento del carico.

L'indirizzo IP viene rilasciato quando si arresta o si elimina la risorsa.  

Ad esempio, una risorsa IP pubblico viene rilasciata da una risorsa denominata **Resource a**. La **risorsa a** riceve un indirizzo IP diverso all'avvio se la risorsa IP pubblico viene riassegnata.

L'indirizzo IP viene rilasciato quando il metodo di allocazione viene modificato da **statico** a **dinamico**. Per assicurarsi che l'indirizzo IP per la risorsa associata rimanga invariato, impostare il metodo di allocazione in modo esplicito su **statico**. Un indirizzo IP statico viene assegnato immediatamente.

> [!NOTE]
> Anche quando si imposta il metodo di allocazione su **statico**, non è possibile specificare l'indirizzo IP effettivo assegnato alla risorsa indirizzo IP pubblico. Azure assegna l'indirizzo IP da un pool di indirizzi IP disponibili nella località di Azure in cui viene creata la risorsa.
>

Gli indirizzi IP pubblici statici sono comunemente usati negli scenari seguenti:

* Quando è necessario aggiornare le regole del firewall per comunicare con le risorse di Azure.
* La risoluzione del nome DNS, in cui una modifica dell'indirizzo IP richiederebbe l'aggiornamento dei record A.
* Le risorse di Azure comunicano con altri servizi o altre app che usano il modello di sicurezza basato su indirizzi IP.
* Si usano i certificati TLS/SSL collegati a un indirizzo IP.

> [!NOTE]
> Azure assegna gli indirizzi IP pubblici da un intervallo univoco per ogni area del cloud di Azure. È possibile scaricare l'elenco degli intervalli (prefissi) per i cloud [Pubblico](https://www.microsoft.com/download/details.aspx?id=56519), [US Government](https://www.microsoft.com/download/details.aspx?id=57063), [Cina](https://www.microsoft.com/download/details.aspx?id=57062) e [Germania](https://www.microsoft.com/download/details.aspx?id=57064) di Azure.
>

## <a name="dns-hostname-resolution"></a>Risoluzione del nome host DNS

Selezionare l'opzione per specificare un'etichetta del nome di dominio DNS per una risorsa IP pubblica. 

Questa selezione crea un mapping per **domainnamelabel**. **location**. cloudapp.Azure.com per l'indirizzo IP pubblico nel DNS gestito da Azure. 

Ad esempio, la creazione di un indirizzo IP pubblico con:

* **Contoso** come **domainnamelabel**
* **Stati Uniti occidentali** **Località** di Azure

Il nome di dominio completo (FQDN) **contoso.westus.cloudapp.Azure.com** viene risolto nell'indirizzo IP pubblico della risorsa.

> [!IMPORTANT]
> Ogni etichetta di nome di dominio creata deve essere univoca nella relativa posizione di Azure.  
>

## <a name="dns-recommendations"></a>Raccomandazioni DNS

Se è necessario spostare un'area, non è possibile eseguire la migrazione del nome di dominio completo dell'indirizzo IP pubblico. Usare il nome di dominio completo per creare un record CNAME personalizzato che punta all'indirizzo IP pubblico. 

Se è necessario uno spostamento a un indirizzo IP pubblico diverso, aggiornare il record CNAME anziché aggiornare il nome di dominio completo.

È possibile usare [DNS di Azure](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) o un provider DNS esterno per il record DNS. 

## <a name="virtual-machines"></a>Macchine virtuali

È possibile associare un indirizzo IP pubblico a una macchina virtuale [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) assegnandola alla relativa **interfaccia di rete**. 

Scegliere **dinamico** o **statico** per l'indirizzo IP pubblico. Per altre informazioni sull'assegnazione di indirizzi IP all'interfaccia di rete, vedere [Aggiungere, modificare o rimuovere indirizzi IP per un'interfaccia di rete di Azure](virtual-network-network-interface-addresses.md).

## <a name="internet-facing-load-balancers"></a>Servizi di bilanciamento del carico con connessione Internet

È possibile associare un indirizzo IP pubblico di uno [SKU](#sku) a un [Azure Load Balancer](../load-balancer/load-balancer-overview.md), assegnandogli la configurazione front- **end** del servizio di bilanciamento del carico. L'IP pubblico funge da indirizzo IP con carico bilanciato. 

A un servizio di bilanciamento del carico front-end è possibile assegnare un indirizzo IP pubblico statico o dinamico. È possibile assegnare più indirizzi IP pubblici a un front-end di bilanciamento del carico. Questa configurazione consente scenari [Multi-VIP](../load-balancer/load-balancer-multivip-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) come un ambiente multi-tenant con siti Web basati su TLS. 

Per altre informazioni sugli SKU di Azure Load Balancer, vedere [Azure load balancer standard SKU](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (SKU Standard di Azure Load Balancer).

## <a name="vpn-gateways"></a>Gateway VPN

Il [gateway VPN di Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) connette una rete virtuale di Azure a:

* Reti virtuali di Azure
* Rete locale. 

Un indirizzo IP pubblico viene assegnato al gateway VPN per consentire la comunicazione con la rete remota. A un gateway VPN è possibile assegnare solo un indirizzo IP pubblico *dinamico* Basic.

## <a name="application-gateways"></a>Gateway applicazione

È possibile associare un indirizzo IP pubblico a un [gateway applicazione](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)di Azure, assegnandolo alla configurazione **front-end** del gateway. 

* Assegnare un indirizzo IP pubblico di base **dinamico** a una configurazione front-end V1 del gateway applicazione. 
* Assegnare un indirizzo dello SKU standard **statico** a una configurazione front-end V2.

## <a name="azure-firewall"></a>Firewall di Azure

Il [firewall di Azure](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) consente di creare, applicare e registrare i criteri di connettività di rete e di applicazione tra sottoscrizioni e reti virtuali.

È possibile associare solo indirizzi IP pubblici **statici** con un firewall. Questo consente ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. 


## <a name="at-a-glance"></a>Riepilogo

La tabella seguente illustra la proprietà tramite la quale un indirizzo IP pubblico può essere associato a una risorsa di primo livello e ai possibili metodi di allocazione.

| Risorse di livello superiore | Associazione di indirizzi IP | Dynamic | Statico |
| --- | --- | --- | --- |
| Macchina virtuale |Interfaccia di rete |Sì |Sì |
| Servizio di bilanciamento del carico con connessione Internet |Configurazione front-end |Sì |Sì |
| gateway VPN |Configurazione IP del gateway |Sì |No |
| gateway applicazione |Configurazione front-end |Sì (solo V1) |Sì (solo V2) |
| Firewall di Azure | Configurazione front-end | No | Sì|

## <a name="limits"></a>Limiti

I limiti per gli indirizzi IP sono elencati nel set completo di [limiti per la rete](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) in Azure. 

I limiti sono classificati per area e per sottoscrizione. [Contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti fino ai limiti massimi in base alle esigenze aziendali.

## <a name="pricing"></a>Prezzi

Per gli indirizzi IP pubblici può essere previsto un addebito nominale. Per altre informazioni sui prezzi degli indirizzi IP in Azure, vedere la pagina [Prezzi per gli indirizzi IP](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sugli [indirizzi IP privati in Azure](private-ip-addresses.md)
* [Distribuire una VM con un IP pubblico statico tramite il portale di Azure](virtual-network-deploy-static-pip-arm-portal.md)

