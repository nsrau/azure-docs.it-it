---
title: Rete di Azure per enti pubblici | Documentazione Microsoft
description: "Fornisce un confronto di funzionalità e informazioni aggiuntive per la connettività privata all&quot;eGovernment"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


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

## <a name="next-steps"></a>Passaggi successivi
Per informazioni aggiuntive e aggiornamenti, sottoscrivere il <a href="https://blogs.msdn.microsoft.com/azuregov/">blog di Microsoft Azure per enti pubblici. </a>




<!--HONumber=Nov16_HO3-->


