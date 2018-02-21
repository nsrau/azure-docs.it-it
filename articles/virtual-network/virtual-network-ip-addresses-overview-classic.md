---
title: Tipi di indirizzi IP in Azure (modello di distribuzione classica) | Documentazione Microsoft
description: Informazioni sugli indirizzi IP pubblici e privati (classici) in Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 2f8664ab-2daf-43fa-bbeb-be9773efc978
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: jdial
ms.openlocfilehash: d5eea5e4499b9de40002ce2fc6aac39239c41b19
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>Tipi di indirizzi IP e metodi di allocazione (modello di distribuzione classica) in Azure
È possibile assegnare gli indirizzi IP alle risorse di Azure per comunicare con altre risorse di Azure, con la rete locale e con Internet. Sono disponibili due tipi di indirizzi IP che è possibile usare in Azure: pubblici e privati.

Gli indirizzi IP pubblici consentono di comunicare con Internet e con i servizi pubblici di Azure.

Gli indirizzi IP privati vengono usati per la comunicazione all'interno di una rete virtuale Azure (VNet), di un servizio cloud e della rete locale quando si usa un gateway VPN o un circuito ExpressRoute per estendere la rete ad Azure.

> [!IMPORTANT]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md).  Questo articolo illustra l'uso del modello di distribuzione classica. Microsoft consiglia di usare Resource Manager per la maggior parte delle distribuzioni più recenti. Informazioni sugli indirizzi IP in Resource Manager sono disponibili nell'articolo sugli [indirizzi IP](virtual-network-ip-addresses-overview-arm.md).

## <a name="public-ip-addresses"></a>Indirizzi IP pubblici
Gli indirizzi IP pubblici consentono alle risorse di Azure di comunicare con Internet e i servizi pubblici di Azure, ad esempio [Cache Redis di Azure](https://azure.microsoft.com/services/cache/), [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/), i [database SQL](../sql-database/sql-database-technical-overview.md) e [Archiviazione di Azure](../storage/common/storage-introduction.md).

Un indirizzo IP pubblico è associato ai seguenti tipi di risorse:

* Servizi cloud
* Macchine virtuali IaaS
* Istanze del ruolo PaaS
* Gateway VPN
* Gateway di applicazione

### <a name="allocation-method"></a>Metodo di allocazione
Quando un indirizzo IP pubblico deve essere assegnato a una risorsa di Azure, viene allocato *dinamicamente* da un pool di indirizzi IP pubblici disponibili all'interno del percorso in cui è stata creata la risorsa. Questo indirizzo IP viene rilasciato quando la risorsa viene arrestata. Nel caso di un servizio cloud, questa situazione si verifica quando tutte le istanze del ruolo vengono arrestate, evento che è possibile evitare usando un indirizzo IP *statico* (riservato). Vedere la sezione [Servizi cloud](#Cloud-services).

> [!NOTE]
> L'elenco degli intervalli IP da cui gli indirizzi IP pubblici vengono allocati alle risorse di Azure è pubblicato negli [intervalli IP dei data center di Azure](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>Risoluzione del nome host DNS
Quando si crea un servizio cloud o una VM IaaS, è necessario fornire un nome DNS del servizio cloud che sia univoco tra tutte le risorse in Azure. In questo modo si crea un mapping nei server DNS gestiti di Azure per *dnsname*.cloudapp.net all'indirizzo IP pubblico della risorsa. Ad esempio, se si crea un servizio cloud con il nome DNS del servizio cloud **contoso**, il nome di dominio completo (FQDN) **contoso.cloudapp.net** verrà risolto in un indirizzo IP pubblico (VIP) del servizio cloud. È possibile usare questo nome di dominio completo per creare un record CNAME di dominio personalizzato che punta all'indirizzo IP pubblico in Azure.

### <a name="cloud-services"></a>Servizi cloud
Un servizio cloud ha sempre un indirizzo IP pubblico detto indirizzo IP virtuale (VIP). È possibile creare endpoint in un servizio cloud per associare porte diverse nell'indirizzo VIP alle porte interne sulle macchine virtuali e istanze di ruolo all'interno del servizio cloud. 

Un servizio cloud può contenere più VM IaaS, o istanze del ruolo PaaS, esposte tramite lo stesso indirizzo VIP del servizio cloud. È anche possibile assegnare [più indirizzi VIP a un servizio cloud](../load-balancer/load-balancer-multivip.md), consentendo così scenari con più indirizzi VIP come quello di un ambiente multi-tenant con siti Web basati su SSL.

È possibile garantire che l'indirizzo IP pubblico di un servizio cloud rimanga invariato, anche quando tutte le istanze del ruolo vengono arrestate, usando un indirizzo IP *statico* pubblico, detto [IP riservato](virtual-networks-reserved-public-ip.md). È possibile creare una risorsa IP statica (riservata) in un percorso specifico e assegnarla a qualsiasi servizio cloud in tale percorso. Non è possibile specificare l'indirizzo IP effettivo per l'IP riservato, poiché viene allocato dal pool di indirizzi IP disponibili nel percorso in cui viene creato. Questo indirizzo IP non viene rilasciato finché non viene eliminato esplicitamente.

Gli indirizzi IP pubblici statici (riservati) sono comunemente usati negli scenari in cui un servizio cloud:

* richiede regole del firewall per essere configurato dagli utenti finali.
* dipende dalla risoluzione del nome DNS esterno, e un indirizzo IP dinamico richiederebbe l'aggiornamento dei record A.
* utilizza i servizi Web esterni che usano il modello di sicurezza basato su IP.
* usa i certificati SSL collegati a un indirizzo IP.

> [!NOTE]
> Quando si crea una VM classica, Azure crea un *servizio cloud* contenitore con un indirizzo IP virtuale (VIP). Quando la creazione avviene tramite portale, il portale configura un *endpoint* RDP o SSH predefinito, in modo da potersi connettere alla VM tramite l'indirizzo VIP del servizio cloud. L'indirizzo VIP del servizio cloud può essere riservato, in modo da avere a disposizione un indirizzo IP riservato per connettersi alla VM. È possibile aprire porte aggiuntive tramite la configurazione di più endpoint.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>Istanze del ruolo PaaS e delle macchine virtuali IaaS
È possibile assegnare un indirizzo IP pubblico direttamente a una [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) IaaS o a un'istanza del ruolo PaaS all'interno di un servizio cloud. In questo caso si definisce indirizzo IP pubblico a livello di istanza ([ILPIP](virtual-networks-instance-level-public-ip.md)). Questo indirizzo IP pubblico può essere solo dinamico.

> [!NOTE]
> Questo è diverso dall'indirizzo VIP del servizio cloud, che è un contenitore per le VM IaaS o le istanze del ruolo PaaS, in quanto un servizio cloud può contenere più VM IaaS, o più istanze del ruolo PaaS, esposte tramite lo stesso indirizzo VIP del servizio cloud.
> 
> 

### <a name="vpn-gateways"></a>Gateway VPN
È possibile usare un oggetto [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) per connettere una rete virtuale di Azure ad altre reti virtuali o locali di Azure. Al gateway VPN viene assegnato *dinamicamente*un indirizzo IP pubblico, che consente la comunicazione con la rete remota.

### <a name="application-gateways"></a>Gateway di applicazione
È possibile usare un [gateway applicazione](../application-gateway/application-gateway-introduction.md) di Azure per il bilanciamento del carico Layer7 allo scopo di instradare il traffico di rete basato su HTTP. Al gateway applicazione viene assegnato *dinamicamente*un indirizzo IP pubblico, che agisce come l'indirizzo VIP con bilanciamento del carico.

### <a name="at-a-glance"></a>Riepilogo
Nella tabella seguente viene illustrato ogni tipo di risorsa con i metodi di allocazione possibili (dinamico o statico) e la possibilità di assegnare più indirizzi IP pubblici.

| Risorsa | Dinamico | statico | Più indirizzi IP |
| --- | --- | --- | --- |
| servizio cloud |Sì |Sì |Sì |
| Istanza del ruolo PaaS o della macchine virtuale IaaS |Sì |No  |No  |
| gateway VPN |Sì |No  |No  |
| gateway applicazione |Sì |No  |No  |

## <a name="private-ip-addresses"></a>Indirizzi IP privati
Gli indirizzi IP privati consentono alle risorse Azure di comunicare con altre risorse in un servizio cloud, in una [rete virtuale](virtual-networks-overview.md)(VNet) o nella rete locale (tramite un gateway VPN o il circuito ExpressRoute), senza usare un indirizzo IP raggiungibile tramite Internet.

Nel modello di distribuzione classico di Azure, un indirizzo IP privato può essere assegnato alle risorse di Azure seguenti:

* Istanze del ruolo PaaS e delle macchine virtuali IaaS
* Servizio di bilanciamento del carico interno
* gateway applicazione

### <a name="iaas-vms-and-paas-role-instances"></a>Istanze del ruolo PaaS e delle macchine virtuali IaaS
Le macchine virtuali create con il modello di distribuzione classica vengono sempre inserite in un servizio cloud, in modo analogo alle istanze del ruolo PaaS. Il comportamento degli indirizzi IP privati è pertanto simile per tali risorse.

È importante notare che un servizio cloud può essere distribuito in due modi:

* Come un servizio cloud *autonomo* , non incluso in una rete virtuale.
* Come parte di una rete virtuale.

#### <a name="allocation-method"></a>Metodo di allocazione
Nel caso di un servizio cloud *autonomo*, alle risorse viene allocato *dinamicamente* un indirizzo IP privato dall'intervallo di indirizzi IP privati del data center di Azure. Può essere usato solo per la comunicazione con altre macchine virtuali all'interno dello stesso servizio cloud. Questo indirizzo IP può cambiare quando la risorsa viene arrestata e avviata.

Nel caso di un servizio cloud distribuito in una rete virtuale, alle risorse vengono allocati indirizzi IP privati dall'intervallo di indirizzi delle subnet associate, come specificato nella configurazione di rete. Questi indirizzi IP privati possono essere usati per la comunicazione tra tutte le macchine virtuali all'interno della rete virtuale.

Inoltre, nel caso dei servizi cloud all'interno di una rete virtuale, viene allocato *dinamicamente* un indirizzo IP privato (tramite DHCP) per impostazione predefinita. Questo indirizzo IP può cambiare quando la risorsa viene arrestata e avviata. Per verificare che l'indirizzo IP resti invariato, è necessario impostare il metodo di allocazione *statico*e fornire un indirizzo IP valido nell'intervallo di indirizzi corrispondente.

Gli indirizzi IP privati statici vengono comunemente usati per:

* Macchine virtuali che fungono da controller di dominio o server DNS.
* Macchine virtuali che richiedono regole firewall basate su indirizzi IP.
* Macchine virtuali che eseguono servizi accessibili da altre applicazioni tramite un indirizzo IP.

#### <a name="internal-dns-hostname-resolution"></a>Risoluzione del nome host DNS interno
Tutte le istanze del ruolo PaaS e delle macchine virtuali Azure sono configurate con [server DNS gestiti da Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) per impostazione predefinita, a meno che non si configurano in modo esplicito server DNS personalizzati. Questi server DNS forniscono la risoluzione dei nomi interni per le macchine virtuali e le istanze del ruolo che si trovano nello stesso servizio cloud o rete virtuale.

Quando si crea una macchina virtuale, ai server DNS gestiti da Azure viene aggiunto un mapping del nome host al relativo indirizzo IP privato. Se si usa una VM con più schede di interfaccia di rete, il nome host viene mappato all'indirizzo IP privato della scheda di interfaccia di rete principale. Tuttavia, queste informazioni di mapping sono limitate alle risorse che si trovano all'interno dello stesso servizio cloud o rete virtuale.

Nel caso di un servizio cloud *autonomo* , sarà possibile risolvere i nomi host di tutte le istanze di macchine virtuali o del ruolo solo all'interno dello stesso servizio cloud. Nel caso di un servizio cloud all'interno di una rete virtuale, sarà possibile risolvere i nomi host di tutte le istanze di macchine virtuali o del ruolo all'interno della rete virtuale.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Servizi di bilanciamento del carico interno e gateway applicazione
È possibile assegnare un indirizzo IP privato alla configurazione **front-end** di un [servizio di bilanciamento del carico interno di Azure](../load-balancer/load-balancer-internal-overview.md) o di un [gateway applicazione di Azure](../application-gateway/application-gateway-introduction.md). Questo indirizzo IP privato funge da endpoint interno, accessibile solo alle risorse all'interno della rete virtuale e alle reti remote connesse alla rete virtuale. È possibile assegnare un indirizzo IP privato dinamico o statico alla configurazione front-end. È inoltre possibile assegnare più indirizzi IP privati per consentire scenari con più indirizzi VIP.

### <a name="at-a-glance"></a>Riepilogo
Nella tabella seguente viene illustrato ogni tipo di risorsa con i metodi di allocazione possibili (dinamico o statico) e la possibilità di assegnare più indirizzi IP privati.

| Risorsa | Dinamico | statico | Più indirizzi IP |
| --- | --- | --- | --- |
| Macchina virtuale (in una rete virtuale o un servizio cloud *autonomo*) |Sì |Sì |Sì |
| Istanza del ruolo PaaS (in una rete virtuale o un servizio cloud *autonomo*) |Sì |No  |No  |
| Front-end del servizio di bilanciamento del carico interno |Sì |Sì |Sì |
| Front-end del gateway applicazione |Sì |Sì |Sì |

## <a name="limits"></a>Limiti
La tabella seguente illustra i limiti imposti sull'assegnazione degli indirizzi IP in Azure per ogni sottoscrizione. È possibile [contattare il supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per aumentare i limiti predefiniti fino ai massimi consentiti in base alle esigenze aziendali.

|  | Limite predefinito | Limite massimo |
| --- | --- | --- |
| Indirizzi IP pubblici (dinamici) |5 |contattare il supporto tecnico |
| Indirizzi IP pubblici riservati. |20 |contattare il supporto tecnico |
| Indirizzo VIP pubblico per distribuzione (servizio cloud) |5 |contattare il supporto tecnico |
| Indirizzo VIP privato (ILB) per distribuzione (servizio cloud) |1 |1 |

Verificare di avere consultato l'elenco completo dei [limiti della rete](../azure-subscription-service-limits.md#networking-limits) in Azure.

## <a name="pricing"></a>Prezzi
Nella maggior parte dei casi, gli indirizzi IP pubblici sono gratuiti. È prevista una tariffa nominale per l'uso di indirizzi IP pubblici statici e/o aggiuntivi. Verificare di avere compreso la [struttura dei prezzi per gli indirizzi IP pubblici](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Differenze tra le distribuzioni di Gestione risorse e le distribuzioni classiche
Di seguito è riportato un confronto tra la funzione di assegnazione degli indirizzi IP in Gestione risorse e quella usata nel modello di distribuzione classico.

|  | Risorsa | Classico | Gestione risorse |
| --- | --- | --- | --- |
| **Indirizzo IP pubblico** |***VM*** |Indicato come un ILPIP (solo dinamico) |Indicato come un IP pubblico (dinamico o statico) |
|  ||Assegnato a una VM IaaS o a un'istanza del ruolo PaaS |Associato alla scheda NIC della VM | |
|  |***Servizio di bilanciamento del carico con connessione Internet*** |Indicato come indirizzo VIP (dinamico) o IP riservato (statico) |Indicato come un IP pubblico (dinamico o statico) | |
|  ||Assegnato a un servizio cloud |Associato alla configurazione front-end del servizio di bilanciamento del carico | |
|  | | | |
| **Indirizzo IP privato** |***VM*** |Indicato come un DIP |Indicato come un indirizzo IP privato |
|  ||Assegnato a una VM IaaS o a un'istanza del ruolo PaaS |Assegnato alla scheda NIC della VM | |
|  |***Servizio di bilanciamento del carico interno*** |Assegnato all'ILB (dinamico o statico) |Assegnato alla configurazione front-end dell'ILB (dinamico o statico) | |

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire una VM con un indirizzo IP statico privato](virtual-networks-static-private-ip-classic-pportal.md) usando il portale di Azure.

