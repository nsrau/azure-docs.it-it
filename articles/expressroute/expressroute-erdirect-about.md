---
title: Informazioni su Azure ExpressRoute Direct | Microsoft Docs
description: Questa pagina fornisce una panoramica di ExpressRoute Direct (anteprima)
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: mialdrid
ms.openlocfilehash: 3b46f24edf10fee7001b35ffdd9fa218aa438e19
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959674"
---
# <a name="about-expressroute-direct-preview"></a>Informazioni su ExpressRoute Direct (anteprima)

ExpressRoute Direct offre ai clienti la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce doppia connettività a 100 Gbps che supporta la connettività attivo/attivo su larga scala.

Tra le funzionalità principali di ExpressRoute Direct sono incluse:

* Inserimento dati massiccio in servizi quali Archiviazione e Cosmos DB
* Isolamento fisico per i settori regolamentati che richiedono una connettività dedicata e isolata, ad esempio banche, enti pubblici e punti vendita al dettaglio
* Controllo granulare della distribuzione del circuito in base alla business unit

> [!IMPORTANT]
> ExpressRoute Direct è attualmente in anteprima.
>
> L'anteprima pubblica viene messa a disposizione senza contratto di servizio e non deve essere usata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate, potrebbero avere funzioni limitate o potrebbero non essere disponibili in tutte le località di Azure. Vedere [Condizioni supplementari per l'uso delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="enroll-in-the-preview"></a>Registrarsi all'anteprima

Prima di usare ExpressRoute Direct è necessario registrare la sottoscrizione nell'anteprima. Per eseguire la registrazione inviare un messaggio di posta elettronica all'indirizzo <ExpressRouteDirect@microsoft.com> con l'ID della sottoscrizione, includendo i dettagli seguenti:

* Scenari che si intende realizzare con **ExpressRoute Direct**
* Preferenze di località. Per un elenco completo di tutte le località, vedere [Partner e località peer](expressroute-locations-providers.md)
* Sequenza temporale per l'implementazione
* Eventuali altre domande

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute tramite un provider di servizi ed ExpressRoute Direct

| **ExpressRoute tramite un provider di servizi** | **ExpressRoute Direct** | 
| --- | --- |
| Usa i provider di servizi per abilitare il caricamento rapido e la connettività nell'infrastruttura esistente | Richiede un'infrastruttura a 100 Gbps e la gestione completa di tutti i livelli
| Si integra con centinaia di provider, tra cui Ethernet e MPLS | Capacità diretta/dedicata per settori regolamentati e inserimento dati massiccio |
| SKU di circuiti da 50 Mbps a 10 Gbps | Il cliente può selezionare una combinazione degli SKU di circuito seguenti: 5 Gbps, 10 Gbps, 40 Gbps e 100 Gbps, con il limite totale di 200 Gbps
| Ottimizzato per tenant singolo | Ottimizzato per tenant singolo/provider di servizi cloud/più business unit

## <a name="expressroute-direct-circuits"></a>Circuiti di ExpressRoute Direct

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.  

Ogni località peer ha accesso alla rete globale di Microsoft e può accedere a qualsiasi area in una regione geopolitica per impostazione predefinita e a tutte le aree globali con un circuito premium.  

Nella maggior parte degli scenari la funzionalità è uguale ai circuiti che utilizzano un provider del servizio ExpressRoute. Per supportare un'ulteriore granularità e le nuove funzionalità offerte da ExpressRoute Direct, sono disponibili alcune funzionalità chiave nei circuiti di ExpressRoute Direct.

## <a name="circuit-skus"></a>SKU di circuiti

ExpressRoute Direct supporta scenari di inserimento dati massiccio in Archiviazione di Azure e altri servizi Big Data. I circuiti ExpressRoute in ExpressRoute Direct ora supportano anche SKU di circuiti da **40 Gbps** e **100 Gbps**.

## <a name="vlan-tagging"></a>Codifica VLAN

ExpressRoute Direct supporta la codifica VLAN QinQ e Dot1Q.

* La **codifica VLAN QinQ** consente domini di routing isolati per circuito di ExpressRoute. Azure alloca un S-Tag in modo dinamico al momento della creazione del circuito e non può essere modificato. Ogni peering nel circuito (privato e Microsoft) utilizza un C-Tag univoco come VLAN. Non è necessario che il C-Tag sia univoco tra i circuiti sulle porte di ExpressRoute Direct.

* La **codifica VLAN Dot1Q** consente una singola VLAN con tag per coppia di porte di ExpressRoute Direct. Un C-Tag usato in un peering deve essere univoco tra i circuiti e i peering sulla coppia di porte di ExpressRoute Direct.

## <a name="workflow"></a>Flusso di lavoro

![flusso di lavoro](./media/expressroute-erdirect-about/workflow1.png)

## <a name="sla"></a>Contratto di servizio

ExpressRoute Direct offre lo stesso contratto di servizio di livello enterprise con connessioni ridondanti attivo/attivo nella rete globale Microsoft. L'infrastruttura di ExpressRoute è ridondante, la connettività alla rete globale Microsoft è ridondante, diversificata e scalabile in base ai requisiti del cliente. Durante l'anteprima, non sono disponibili contratti di servizio ed è da considerarsi solo per carichi di lavoro non di produzione.

## <a name="next-steps"></a>Passaggi successivi

[Configurare ExpressRoute Direct](expressroute-howto-erdirect.md)
