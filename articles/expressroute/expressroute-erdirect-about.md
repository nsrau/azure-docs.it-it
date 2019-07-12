---
title: Informazioni su ExpressRoute Direct -Azure | Microsoft Docs
description: Questa pagina fornisce una panoramica di ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: jaredro
ms.custom: seodec18
ms.openlocfilehash: e598cc03a1b7b4999719152540866c7168130e03
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807474"
---
# <a name="about-expressroute-direct"></a>Informazioni su ExpressRoute Direct

ExpressRoute Direct offre la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct fornisce duale 100 Gbps o 10 Gbps connettività, che supporta la connettività di attivo/attivo su larga scala.

Tra le funzionalità principali di ExpressRoute Direct sono incluse:

* Inserimento dati massiccio in servizi quali Archiviazione e Cosmos DB
* Isolamento fisico per i settori regolamentati che richiedono una connettività dedicata e isolata, ad esempio banche, enti pubblici e punti vendita al dettaglio
* Controllo granulare della distribuzione del circuito in base alla business unit

## <a name="onboard-to-expressroute-direct"></a>Eseguire l'onboarding di ExpressRoute Direct

Prima di usare ExpressRoute Direct, è innanzitutto necessario registrare la sottoscrizione. Per eseguire la registrazione inviare un messaggio di posta elettronica all'indirizzo <ExpressRouteDirect@microsoft.com> con l'ID della sottoscrizione, includendo i dettagli seguenti:

* Scenari che si intende realizzare con **ExpressRoute Direct**
* Preferenze di località. Per un elenco completo di tutte le località, vedere [Partner e località peer](expressroute-locations-providers.md)
* Sequenza temporale per l'implementazione
* Eventuali altre domande

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute tramite un provider di servizi ed ExpressRoute Direct

| **ExpressRoute tramite un provider di servizi** | **ExpressRoute Direct** | 
| --- | --- |
| Usa i provider di servizi per abilitare il caricamento rapido e la connettività nell'infrastruttura esistente | Richiede 100 Gbps o 10 Gbps infrastruttura e gestione completa di tutti i livelli
| Si integra con centinaia di provider, tra cui Ethernet e MPLS | Capacità diretta/dedicata per settori regolamentati e inserimento dati massiccio |
| SKU di circuiti da 50 Mbps a 10 Gbps | Cliente può selezionare una combinazione del circuito seguente SKU su 100 Gbps ExpressRoute Direct: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 GB/s</li></ul> Cliente può selezionare una combinazione del circuito seguente SKU su 10 Gbps ExpressRoute Direct:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Ottimizzato per tenant singolo | Ottimizzato per tenant singolo/provider di servizi cloud/più business unit

## <a name="expressroute-direct-circuits"></a>Circuiti di ExpressRoute Direct

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.  

Ogni località peer ha accesso alla rete globale di Microsoft e può accedere a qualsiasi area in una regione geopolitica per impostazione predefinita e a tutte le aree globali con un circuito premium.  

Nella maggior parte degli scenari la funzionalità è uguale ai circuiti che utilizzano un provider del servizio ExpressRoute. Per supportare un'ulteriore granularità e le nuove funzionalità offerte da ExpressRoute Direct, sono disponibili alcune funzionalità chiave nei circuiti di ExpressRoute Direct.

## <a name="circuit-skus"></a>SKU di circuiti

ExpressRoute Direct supporta scenari di inserimento dati massiccio in Archiviazione di Azure e altri servizi Big Data. Circuiti ExpressRoute in 100 Gbps ExpressRoute diretto ora anche il supporto **40 Gbps** e **100 Gbps** SKU del circuito. Le coppie di fisiche porta fanno **100 o 10 Gbps** solo e può avere più circuiti virtuali. Dimensioni circuito:

| **100 GB/s ExpressRoute Direct** | **Direct ExpressRoute a 10 Gbps** | 
| --- | --- |
| **Sottoscrizione della larghezza di banda**: 200 GB/s | **Sottoscrizione della larghezza di banda**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 GB/s</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisiti tecnici

* Interfacce di Router (MSEE) di Microsoft Enterprise Edge:
    * Porte doppie a 100 o 10 Gigabit Ethernet solo tra la coppia di router
    * Connettività in modalità Fiber di LR singola
    * IPv4 e IPv6
    * IP MTU 1500 byte

* Router/commutatore livello 2 o Layer 3 connettività:
    * Deve supportare il tag 1 802.1Q (Dot1Q) o due Tag 802.1Q (QinQ) incapsulamento del tag
    * EtherType = 0x8100
    * È necessario aggiungere i tag VLAN esterno (STAG) in base all'ID di VLAN specificato da Microsoft - *applicabile solo su QinQ*
    * Deve supportare più sessioni BGP (VLAN) per ogni porta e dispositivo
    * Connettività IPv4 e IPv6. *Non verrà creato alcun aggiuntiva sotto-interfaccia per IPv6. IPv6 indirizzo verrà aggiunto al sotto-interfaccia esistente*. 
    * Facoltativo: [Rilevamento di inoltro bidirezionale (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) supportare, che è configurato per impostazione predefinita su tutti i Peerings privati nei circuiti ExpressRoute

## <a name="vlan-tagging"></a>Codifica VLAN

ExpressRoute Direct supporta la codifica VLAN QinQ e Dot1Q.

* La **codifica VLAN QinQ** consente domini di routing isolati per circuito di ExpressRoute. Azure alloca un S-Tag in modo dinamico al momento della creazione del circuito e non può essere modificato. Ogni peering nel circuito (privato e Microsoft) utilizza un C-Tag univoco come VLAN. Non è necessario che il C-Tag sia univoco tra i circuiti sulle porte di ExpressRoute Direct.

* La **codifica VLAN Dot1Q** consente una singola VLAN con tag per coppia di porte di ExpressRoute Direct. Un C-Tag usato in un peering deve essere univoco tra i circuiti e i peering sulla coppia di porte di ExpressRoute Direct.

## <a name="workflow"></a>Flusso di lavoro

[![workflow](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contratto di servizio

ExpressRoute Direct offre lo stesso contratto di servizio di livello enterprise con connessioni ridondanti attivo/attivo nella rete globale Microsoft. L'infrastruttura di ExpressRoute è ridondante, la connettività alla rete globale Microsoft è ridondante, diversificata e scalabile in base ai requisiti del cliente. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare ExpressRoute Direct](expressroute-howto-erdirect.md)
