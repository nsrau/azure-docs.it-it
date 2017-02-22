---
title: Rete di Azure per enti pubblici | Documentazione Microsoft
description: "Fornisce un confronto di funzionalità e informazioni aggiuntive per la connettività privata all&quot;eGovernment"
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8


---
# <a name="azure-government-networking"></a>Rete di Azure per enti pubblici
## <a name="expressroute-private-connectivity"></a>ExpressRoute (connettività privata)
ExpressRoute è disponibile a livello generale in Azure per enti pubblici. Per altre informazioni, inclusi i partner e le località di peering, vedere la [documentazione pubblica su ExpressRoute](../expressroute/index.md).

### <a name="variations"></a>Varianti
ExpressRoute è disponibile a livello generale in Azure per enti pubblici. 

* I clienti del settore pubblico si connettono a una capacità fisicamente isolata tramite una connessione ExpressRoute per Azure per enti pubblici dedicata
* Azure per enti pubblici fornisce disponibilità e durabilità più elevate sfruttando più coppie di aree situate a una distanza minima di 805 km 
* Per impostazione predefinita, tutta la connettività ExpressRoute per Azure per enti pubblici viene configurata come ridondante attiva/attiva con il supporto per il burst e fornisce fino a 10 G per la capacità del circuito (la capacità minima è di 50 MB)
* Le località ExpressRoute per Azure per enti pubblici forniscono percorsi ottimizzati (hop brevi, bassa latenza, prestazioni elevate e così via) per i clienti e le aree con ridondanza geografica di Azure per enti pubblici
* La connessione privata di ExpressRoute per Azure per enti pubblici non utilizza, attraversa o dipende da Internet
* L'infrastruttura fisica e logica di Azure per enti pubblici è fisicamente dedicata e separata e l'accesso è limitato agli utenti degli Stati Uniti
* Microsoft è proprietaria e gestisce tutta l'infrastruttura in fibra ottica tra le aree di Azure per enti pubblici e le località Meet-Me di ExpressRoute per Azure per enti pubblici
* ExpressRoute per Azure per enti pubblici fornisce la connettività a Microsoft Azure, O365 e servizi cloud CRM

### <a name="considerations"></a>Considerazioni
Esistono due servizi di base che forniscono la connettività di rete privata in Azure per enti pubblici: VPN (da sito a sito per un'organizzazione tipica) ed ExpressRoute.

Azure ExpressRoute si usa per creare connessioni private tra i data center di Azure per enti pubblici e l'infrastruttura locale o in un ambiente di housing. Le connessioni ExpressRoute non usano la rete Internet pubblica. Offrono maggiore affidabilità, velocità più elevate e latenze più basse rispetto alle connessioni Internet tradizionali. In alcuni casi, l'uso delle connessioni ExpressRoute per trasferire dati tra sistemi locali e Azure offre vantaggi significativi in termini di costi.   

Con ExpressRoute si possono stabilire connessioni con Azure in una località ExpressRoute, ad esempio la struttura di un provider di Exchange, o connettersi direttamente ad Azure dalla rete WAN esistente, ad esempio una VPN MPLS (Multiprotocol Label Switching), fornita da un provider di servizi di rete.

![testo alternativo](./media/azure-government-capability-private-connectivity-options.PNG)  ![testo alternativo](./media/government-capability-expressroute.PNG)  

Perché i servizi di rete possano supportare le applicazioni e le soluzioni dei clienti di Azure per enti pubblici, è consigliabile implementare ExpressRoute (connettività privata) per connettersi ad Azure per enti pubblici. Se si usano connessioni VPN, è opportuno tenere in considerazione i punti seguenti:

* I clienti devono contattare il funzionario o l'ente competente per l'autorizzazione per determinare se è richiesta la connettività privata o un altro meccanismo di connessione sicura e identificare eventuali restrizioni da considerare.
* I clienti devono decidere se imporre che la connessione VPN da sito a sito venga instradata attraverso una zona con connettività privata.
* I clienti possono ottenere un circuito MPLS o una VPN con un provider di accesso alla connettività privata con licenza.

Tutti i clienti che utilizzano un'architettura di connettività privata devono accertarsi che venga stabilita e mantenuta un'implementazione appropriata per la connessione dei clienti al punto di demarcazione del router perimetrale gateway di rete/Internet per Azure per enti pubblici. In modo analogo, l'organizzazione deve stabilire la connettività di rete tra l'ambiente locale e il punto di demarcazione del router perimetrale gateway di rete/Internet per Azure per enti pubblici.

## <a name="support-for-bgp-communities"></a>Supporto per le community BGP
Questa sezione fornisce una panoramica che illustra come vengono usate le community BGP con ExpressRoute in Azure per enti pubblici. Microsoft annuncerà le route nei percorsi per il peering pubblico e il peering Microsoft con route contrassegnate con i valori della community appropriati. I motivi per questa operazione e i dettagli sui valori della community sono descritti di seguito. Microsoft, tuttavia, non rispetterà eventuali valori della community contrassegnati in base a route annunciate a Microsoft.

Se si effettua la connessione a Microsoft tramite ExpressRoute presso una qualsiasi località di peering all'interno di un'area di Azure per enti pubblici, sarà possibile accedere a tutti i servizi cloud Microsoft in tutte le aree all'interno dell'area specifica. 

Se ad esempio ci si connette a Microsoft in Washington D.C. tramite ExpressRoute, sarà possibile accedere a tutti i servizi cloud Microsoft ospitati in Azure per enti pubblici.

Fare riferimento alla scheda "Panoramica" nella [documentazione pubblica di ExpressRoute](../expressroute/index.md) per avere informazioni dettagliate sulle posizioni e i partner e un elenco dettagliato di ExpressRoute per la località di peering di AzureGov.

È possibile acquistare più di un circuito ExpressRoute. Un maggior numero di connessioni offre vantaggi significativi in termini di disponibilità elevata, grazie alla ridondanza geografica. Se si hanno più circuiti ExpressRoute, si riceverà lo stesso set di prefissi annunciati da Microsoft nei percorsi per il peering pubblico e per il peering Microsoft. Questo significa che saranno disponibili più percorsi dalla propria rete a Microsoft. In questo caso, all'interno della rete potrebbero essere prese decisioni di routing non ottimali, che possono, a loro volta, determinare esperienze di connettività non ottimali a vari servizi. 

Microsoft contrassegnerà i prefissi annunciati tramite il peering pubblico e il peering Microsoft con i valori della community BGP appropriati indicanti l'area in cui sono ospitati i prefissi. Per prendere le decisioni di routing appropriate e offrire un servizio di routing ottimale ai client, fare riferimento ai valori della community.  Per ulteriori informazioni, fare riferimento alla scheda "Attività iniziali" nella [documentazione pubblica di ExpressRoute](../expressroute/index.md) e fare clic su "Ottimizzare il routing".

| **Area di Azure per cloud nazionali**| **Valore della community BGP** |
| --- | --- |
| **US Government** |  |
| US Gov Iowa | 12076:51109 |
| US Gov Virginia | 12076:51105 |

Tutte le route annunciate da Microsoft verranno contrassegnate con il valore della community appropriato. 

Microsoft contrassegnerà anche i prefissi in base al servizio di appartenenza. Questo si applica solo al peering Microsoft. La tabella seguente fornisce il mapping del servizio al valore della community BGP.

| **Servizio dei cloud nazionali** | **Valore della community BGP** |
| --- | --- |
| **US Government** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype for Business Online |12076:5130 |
| CRM Online |12076:5140 |
| Altri servizi online di Office 365 |12076:5200 |

> [!NOTE]
> Microsoft non riconosce eventuali valori di BGP Community impostati sulle route pubblicate su Microsoft.

## <a name="support-for-load-balancer"></a>Supporto per il servizio di bilanciamento del carico
Il servizio di bilanciamento del carico è disponibile a livello generale in Azure per enti pubblici. Per altre informazioni, vedere la [documentazione pubblica sul servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manger"></a>Supporto per Gestione traffico
Il servizio di Gestione traffico è disponibile a livello generale in Azure per enti pubblici. Per altre informazioni, vedere la [documentazione pubblica sul servizio di Gestione traffico](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-vnet-peering"></a>Supporto per il peering reti virtuali 
Il peering reti virtuali è disponibile a livello generale in Azure per enti pubblici. Per altre informazioni, vedere la [documentazione pubblica sul peering reti virtuali](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>Supporto per il Gateway VPN 
Il Gateway VPN è disponibile a livello generale in Azure per enti pubblici. Per altre informazioni, vedere la [documentazione pubblica sul Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>




<!--HONumber=Jan17_HO5-->


