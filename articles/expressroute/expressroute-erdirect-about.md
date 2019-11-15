---
title: Informazioni su Azure ExpressRoute Direct
description: Questa pagina offre una panoramica di ExpressRoute Direct
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jaredro
ms.openlocfilehash: f26fdd75e0a6c6228d329a8b9be4743072ae54c1
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083594"
---
# <a name="about-expressroute-direct"></a>Informazioni su ExpressRoute Direct

ExpressRoute Direct offre la possibilità di connettersi direttamente alla rete globale di Microsoft in località peer distribuite in modo strategico in tutto il mondo. ExpressRoute Direct offre connettività dual 100 Gbps o 10 Gbps, che supporta la connettività attiva/attiva su larga scala.

Tra le funzionalità principali di ExpressRoute Direct sono incluse:

* Inserimento dati massiccio in servizi quali Archiviazione e Cosmos DB
* Isolamento fisico per i settori regolamentati che richiedono una connettività dedicata e isolata, ad esempio banche, enti pubblici e punti vendita al dettaglio
* Controllo granulare della distribuzione del circuito in base alla business unit

## <a name="onboard-to-expressroute-direct"></a>Onboarding in ExpressRoute Direct

Prima di usare ExpressRoute Direct, è necessario prima registrare la sottoscrizione. Per eseguire la registrazione inviare un messaggio di posta elettronica all'indirizzo <ExpressRouteDirect@microsoft.com> con l'ID della sottoscrizione, includendo i dettagli seguenti:

* Scenari che si intende realizzare con **ExpressRoute Direct**
* Preferenze di località. Per un elenco completo di tutte le località, vedere [Partner e località peer](expressroute-locations-providers.md)
* Sequenza temporale per l'implementazione
* Eventuali altre domande

## <a name="expressroute-using-a-service-provider-and-expressroute-direct"></a>ExpressRoute tramite un provider di servizi ed ExpressRoute Direct

| **ExpressRoute tramite un provider di servizi** | **ExpressRoute Direct** | 
| --- | --- |
| Usa i provider di servizi per abilitare il caricamento rapido e la connettività nell'infrastruttura esistente | Richiede l'infrastruttura 100 Gbps/10 Gbps e la gestione completa di tutti i livelli
| Si integra con centinaia di provider, tra cui Ethernet e MPLS | Capacità diretta/dedicata per settori regolamentati e inserimento dati massiccio |
| SKU di circuiti da 50 Mbps a 10 Gbps | Il cliente può selezionare una combinazione degli SKU di circuito seguenti su 100 Gbps ExpressRoute Direct: <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> Il cliente può selezionare una combinazione degli SKU di circuito seguenti su 10 Gbps ExpressRoute Direct:<ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>
| Ottimizzato per tenant singolo | Ottimizzato per un singolo tenant con più business unit e più ambienti di lavoro

## <a name="expressroute-direct-circuits"></a>Circuiti di ExpressRoute Direct

Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure e Office 365.

Ogni località peer ha accesso alla rete globale di Microsoft e può accedere a qualsiasi area in una regione geopolitica per impostazione predefinita e a tutte le aree globali con un circuito premium.  

Nella maggior parte degli scenari la funzionalità è uguale ai circuiti che utilizzano un provider del servizio ExpressRoute. Per supportare un'ulteriore granularità e le nuove funzionalità offerte da ExpressRoute Direct, sono disponibili alcune funzionalità chiave nei circuiti di ExpressRoute Direct.

## <a name="circuit-skus"></a>SKU di circuiti

ExpressRoute Direct supporta scenari di inserimento dati massiccio in Archiviazione di Azure e altri servizi Big Data. I circuiti ExpressRoute su 100 Gbps ExpressRoute Direct ora supportano anche gli SKU del circuito **40 Gbps** e **100 Gbps** . Le coppie di porte fisiche sono solo **100 o 10 Gbps** e possono avere più circuiti virtuali. Dimensioni del circuito:

| **100 Gbps ExpressRoute Direct** | **ExpressRoute diretto 10 Gbps** | 
| --- | --- |
| **Larghezza di banda sottoscritta**: 200 Gbps | **Larghezza di banda sottoscritta**: 20 Gbps |
| <ul><li>5 Gbps</li><li>10 Gbps</li><li>40 Gbps</li><li>100 Gbps</li></ul> | <ul><li>1 Gbps</li><li>2 Gbps</li><li>5 Gbps</li><li>10 Gbps</li></ul>

## <a name="technical-requirements"></a>Requisiti tecnici

* Interfacce di Microsoft Enterprise Edge router (MSEE):
    * Doppio porte Ethernet da 10 o 100 Gigabit solo tra coppie di router
    * Connettività Fiber LR in modalità singola
    * IPv4 e IPv6
    * IP MTU 1500 byte

* Connettività di livello 2/livello 3 switch/router:
    * Deve supportare 1 tag 802.1 Q (Dot1Q) o l'incapsulamento di due tag 802.1 Q (QinQ)
    * Ethertype = 0x8100
    * È necessario aggiungere il tag di VLAN esterno (STAG) in base all'ID VLAN specificato da Microsoft, *applicabile solo su QinQ*
    * Deve supportare più sessioni BGP (VLAN) per porta e dispositivo
    * Connettività IPv4 e IPv6. *Per IPv6 non verrà creata alcuna interfaccia secondaria aggiuntiva. L'indirizzo IPv6 verrà aggiunto all'interfaccia secondaria esistente*. 
    * Facoltativo: supporto del [rilevamento di inoltri bidirezionale (BFD)](https://docs.microsoft.com/azure/expressroute/expressroute-bfd) , configurato per impostazione predefinita in tutti i peering privati nei circuiti ExpressRoute

## <a name="vlan-tagging"></a>Codifica VLAN

ExpressRoute Direct supporta la codifica VLAN QinQ e Dot1Q.

* La **codifica VLAN QinQ** consente domini di routing isolati per circuito di ExpressRoute. Azure alloca un S-Tag in modo dinamico al momento della creazione del circuito e non può essere modificato. Ogni peering nel circuito (privato e Microsoft) utilizza un C-Tag univoco come VLAN. Non è necessario che il C-Tag sia univoco tra i circuiti sulle porte di ExpressRoute Direct.

* La **codifica VLAN Dot1Q** consente una singola VLAN con tag per coppia di porte di ExpressRoute Direct. Un C-Tag usato in un peering deve essere univoco tra i circuiti e i peering sulla coppia di porte di ExpressRoute Direct.

## <a name="workflow"></a>Flusso di lavoro

[flusso di lavoro ![](./media/expressroute-erdirect-about/workflow1.png)](./media/expressroute-erdirect-about/workflow1.png#lightbox)

## <a name="sla"></a>Contratto di servizio

ExpressRoute Direct offre lo stesso contratto di servizio di livello enterprise con connessioni ridondanti attivo/attivo nella rete globale Microsoft. L'infrastruttura di ExpressRoute è ridondante, la connettività alla rete globale Microsoft è ridondante, diversificata e scalabile in base ai requisiti del cliente. 

## <a name="next-steps"></a>Passaggi successivi

[Configurare ExpressRoute Direct](expressroute-howto-erdirect.md)
