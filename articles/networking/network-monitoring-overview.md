---
title: Informazioni sul monitoraggio della rete in Log Analytics | Microsoft Docs
description: Panoramica sulle soluzioni di monitoraggio della rete, incluso Monitoraggio prestazioni rete, per la gestione di rete in ambienti cloud, locali e ibridi.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: 
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ajaycode
ms.openlocfilehash: 6d93821b59e1f69a48c3d5eeda96dad2edddb188
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="network-monitoring-solutions"></a>Soluzioni di monitoraggio della rete 

Azure offre una vasta gamma di soluzioni per il monitoraggio delle risorse di rete. Azure offre soluzioni e utilità per monitorare la connettività della rete, l'integrità dei circuiti ExpressRoute, nonché analizzare il traffico di rete nel cloud.

## <a name="network-performance-monitor-npm"></a>Monitoraggio delle prestazioni di rete

Monitoraggio prestazioni rete è una suite di funzionalità che congiuntamente consentono di monitorare lo stato di integrità della rete, la connettività a livello di rete con le applicazioni, nonché analizzare in dettaglio le prestazioni della rete in uso. Monitoraggio prestazioni rete è una soluzione ibrida basata sul cloud per il monitoraggio della rete e della connettività tra gli elementi seguenti:
 
* Distribuzioni cloud e percorsi locali
* Più data center e succursali
* Applicazioni/microservizi multilivello cruciali
* Percorsi utente e applicazioni basate sul Web (HTTP/HTTPs) 

## <a name="performance-monitor"></a>Monitoraggio delle prestazioni

Performance Monitor è un componente di Monitoraggio prestazioni rete ed è una soluzione di monitoraggio della rete per ambienti cloud, ibridi e locali. È possibile monitorare la connettività della rete tra sedi e succursali remote, posizioni dei negozi, data center e ambienti cloud. Questa funzionalità consente di rilevare tempestivamente i problemi a livello di rete. I vantaggi chiave sono:

* Monitorare perdita e latenza tra varie subnet e configurare avvisi
* Monitorare tutti i percorsi (inclusi i percorsi ridondanti) sulla rete
* Risolvere i problemi di rete temporanei e temporizzati che sono difficili da replicare
* Determinare un segmento specifico nella rete che è responsabile della riduzione delle prestazioni
* Monitorare l'integrità della rete senza la necessità di SNMP

![Mappa della topologia di Monitoraggio prestazioni rete](./media/network-monitoring-overview/npm-topology-map.png) 

Per altre informazioni, vedere gli articoli seguenti:

* [Configurare una soluzione di monitoraggio delle prestazioni di rete in Log Analytics](../log-analytics/log-analytics-network-performance-monitor.md) 
* [Casi d'uso](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
*  Aggiornamenti del prodotto: [Febbraio 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/), [Agosto 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitoraggio di ExpressRoute

Monitoraggio prestazioni rete per ExpressRoute offre una soluzione di monitoraggio di ExpressRoute completa per connessioni peering private. È possibile monitorare la connettività e le prestazioni end-to-end tra succursali e Azure tramite ExpressRoute. Le funzionalità principali sono elencate di seguito:

* Rilevamento automatico dei circuiti ExpressRoute associati alla sottoscrizione
* Rilevamento della topologia di rete da applicazioni locali ad applicazioni cloud
* Pianificazione della capacità, analisi dell'uso
* Monitoraggio e avviso per percorsi primari e secondari
* Rilevamento della riduzione delle prestazioni della connettività delle reti virtuali

Per altre informazioni, vedere gli articoli seguenti:

* [Configurare Monitoraggio prestazioni rete per ExpressRoute](../expressroute/how-to-npm.md)
* [Post di blog](https://aka.ms/NPMExRmonitorGA)

## <a name="service-endpoint-monitor"></a>Monitoraggio endpoint di servizio

Con il monitoraggio dell'endpoint di servizio è possibile testare la raggiungibilità delle applicazioni e rilevare eventuali colli di bottiglia a livello di prestazioni in reti dell'operatore, reti locali e data center cloud/privati.

* Monitorare la connettività di rete end-to-end per le applicazioni
* Correlare la consegna delle applicazioni alle prestazioni di rete, individuare l'esatta posizione della riduzione delle prestazioni nel percorso tra l'utente e l'applicazione
* Testare la raggiungibilità delle applicazioni da più posizioni utente a livello globale
* Determinare la perdita di latenza di rete e pacchetti per le applicazioni line-of-business e SaaS
* Determinare le aree sensibili della rete alla base delle scadenti prestazioni delle applicazioni
* Monitorare la raggiungibilità alle applicazioni di Office 365 tramite test predefiniti per Microsoft Office 365, Dynamics 365, Skype for Business e altri servizi Microsoft

Per altre informazioni, vedere gli articoli seguenti:

* [Configurare Monitoraggio prestazioni rete per il monitoraggio degli endpoint di servizio](https://aka.ms/applicationconnectivitymonitorguide)
* [Post di blog](https://aka.ms/svcendptmonitor)

## <a name="next-steps"></a>Passaggi successivi

* [Configurare Monitoraggio prestazioni rete](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor)
* [Configurare Monitoraggio prestazioni rete per ExpressRoute](../expressroute/how-to-npm.md)
