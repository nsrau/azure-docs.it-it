---
title: Informazioni sul monitoraggio di rete in Monitoraggio di Azure registra | Microsoft Docs
description: Panoramica sulle soluzioni di monitoraggio della rete, incluso Monitoraggio prestazioni rete, per la gestione di rete in ambienti cloud, locali e ibridi.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: b112771e8c0f08e6b9a02c2fc5b7e1887b3f2588
ms.sourcegitcommit: 3675daec6c6efa3f2d2bf65279e36ca06ecefb41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65620425"
---
# <a name="network-monitoring-solutions"></a>Soluzioni di monitoraggio della rete 

Azure offre una vasta gamma di soluzioni per il monitoraggio delle risorse di rete. Azure offre soluzioni e utilità per monitorare la connettività della rete, l'integrità dei circuiti ExpressRoute, nonché analizzare il traffico di rete nel cloud.

## <a name="network-performance-monitor-npm"></a>Monitoraggio delle prestazioni di rete

Monitoraggio prestazioni rete è una suite di funzionalità che congiuntamente consentono di monitorare lo stato di integrità della rete, la connettività a livello di rete con le applicazioni, nonché analizzare in dettaglio le prestazioni della rete in uso. Monitoraggio prestazioni rete è una soluzione ibrida basata sul cloud di monitoraggio della rete e della connettività tra gli elementi seguenti:
 
* Distribuzioni cloud e percorsi locali
* Più data center e succursali
* Applicazioni/microservizi multilivello cruciali
* Percorsi utente e applicazioni basate sul Web (HTTP/HTTPs) 

Performance Monitor, ExpressRoute Monitor e Service Connectivity Monitor sono funzionalità di monitoraggio integrate in Monitoraggio prestazioni rete, descritte nelle sezioni seguenti.

## <a name="performance-monitor"></a>Monitoraggio delle prestazioni

Performance Monitor è un componente di Monitoraggio prestazioni rete ed è una soluzione di monitoraggio della rete per ambienti cloud, ibridi e locali. È possibile monitorare la connettività della rete tra sedi e succursali remote, posizioni dei negozi, data center e ambienti cloud. Questa funzionalità consente di rilevare tempestivamente i problemi a livello di rete. I vantaggi chiave sono:

* Monitorare perdita e latenza tra varie subnet e configurare avvisi
* Monitorare tutti i percorsi (inclusi i percorsi ridondanti) sulla rete
* Risolvere i problemi di rete temporanei e temporizzati che sono difficili da replicare
* Determinare un segmento specifico nella rete che è responsabile della riduzione delle prestazioni
* Monitorare l'integrità della rete senza la necessità di SNMP

![Mappa della topologia di Monitoraggio prestazioni rete](./media/network-monitoring-overview/npm-topology-map.png) 

Per altre informazioni, vedere gli articoli seguenti:

* [Configurare una soluzione di monitoraggio prestazioni rete in log di monitoraggio di Azure](../azure-monitor/insights/network-performance-monitor.md) 
* [Casi d'uso](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Aggiornamenti del prodotto:
  * [Febbraio 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Agosto 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitoraggio di ExpressRoute

Monitoraggio prestazioni rete per ExpressRoute offre una soluzione di monitoraggio di ExpressRoute completa per connessioni basate su Peering privato di Azure e Peering Microsoft. È possibile monitorare la connettività e le prestazioni end-to-end tra succursali e Azure tramite ExpressRoute. Le funzionalità principali sono elencate di seguito:

* Rilevamento automatico dei circuiti ExpressRoute associati alla sottoscrizione
* Rilevamento della topologia di rete da applicazioni locali ad applicazioni cloud
* Pianificazione della capacità, analisi dell'utilizzo della larghezza di banda
* Monitoraggio e avviso per percorsi primari e secondari
* Monitoraggio della connettività a servizi di Azure, come Office 365, Dynamics 365 e così via tramite ExpressRoute
* Rilevamento della riduzione delle prestazioni della connettività delle reti virtuali

![Mappa geografica che mostra il traffico tra le varie aree geografiche](./media/network-monitoring-overview/expressroute-topology-map.png) 

Per altre informazioni, vedere gli articoli seguenti:

* [Configurare Monitoraggio prestazioni rete per ExpressRoute](../expressroute/how-to-npm.md)
* [Post di blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitoraggio connettività servizio

Con il monitoraggio della connettività di servizio è possibile testare la raggiungibilità delle applicazioni e rilevare eventuali colli di bottiglia a livello di prestazioni in reti dell'operatore, reti locali e data center cloud/privati.

* Monitorare la connettività di rete end-to-end per le applicazioni
* Correlare la consegna delle applicazioni alle prestazioni di rete, individuare l'esatta posizione della riduzione delle prestazioni nel percorso tra l'utente e l'applicazione
* Testare la raggiungibilità delle applicazioni da più posizioni utente a livello globale
* Determinare la perdita di latenza di rete e pacchetti per le applicazioni line-of-business e SaaS
* Determinare le aree sensibili della rete alla base delle scadenti prestazioni delle applicazioni
* Monitorare la raggiungibilità alle applicazioni di Office 365 tramite test predefiniti per Microsoft Office 365, Dynamics 365, Skype for Business e altri servizi Microsoft

Per altre informazioni, vedere gli articoli seguenti:

* [Configurare Monitoraggio prestazioni rete per il monitoraggio degli endpoint di servizio](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Post di blog](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analisi del traffico
Analisi del traffico è una soluzione basata sul cloud che fornisce visibilità delle attività di utenti e applicazioni nella rete cloud. Vengono analizzati i log del flusso dei gruppi di sicurezza di rete per fornire informazioni sugli argomenti seguenti:

* Il flusso di traffico nelle reti tra Azure e Internet, nelle aree di cloud pubblico, nelle reti virtuali e nelle subnet
* Applicazioni e protocolli di rete, senza la necessità di sniffer o appliance di agenti di raccolta di flussi dedicati
* Talker principali, applicazioni con un livello di comunicazioni elevato, conversazioni tra macchine virtuali nel cloud e aree sensibili del traffico
* Origini e destinazioni del traffico su reti virtuali, interrelazioni tra applicazioni e servizi aziendali critici
* Sicurezza: traffico dannoso, porte aperte a Internet, applicazioni o macchine virtuali che tentano di accedere a Internet.
* Utilizzo della capacità: consente di eliminare eventuali problemi di overprovisioning o sottoutilizzo monitorando le tendenze di utilizzo dei gateway VPN e di altri servizi

Analisi del traffico fornisce quindi informazioni fruibili che consentono di controllare l'attività di rete dell'organizzazione, proteggere applicazioni e dati, ottimizzare le prestazioni dei carichi di lavoro e garantire la conformità.

![Mappa geografica che mostra il traffico tra le varie aree geografiche](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Collegamenti correlati:
* [Post di blog](https://aka.ms/trafficanalytics), [documentazione](https://aka.ms/trafficanalyticsdocs), [domande frequenti](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Analisi DNS
Sviluppata per amministratori DNS, questa soluzione raccoglie, analizza e mette in correlazione i log di DNS per fornire informazioni correlate alla sicurezza, alle operazioni e alle prestazioni.  Alcune delle funzionalità offerte comprendono:

* Identificazione dei client che tentano di risolvere domini dannosi
* Identificazione di record di risorse non aggiornati
* Visibilità dei nomi di dominio sottoposti frequentemente a query e dei client DNS loquaci
* Visibilità del carico di richieste sui server DNS
* Monitoraggio degli errori di registrazione di DNS dinamici

![Dashboard di Analisi DNS](./media/network-monitoring-overview/dns-analytics-overview.png) 

Collegamenti correlati:
* [Post di blog](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [documentazione](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Miscellaneous

* [Nuovo piano tariffario](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
