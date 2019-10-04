---
title: Rete globale Microsoft-Azure
description: Descrive in che modo Microsoft crea la propria rete globale rapida e affidabile
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: c064a5afee8db0c747273ab1c38c901a6f10ef98
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876143"
---
# <a name="microsoft-global-network"></a>Rete globale Microsoft

Microsoft possiede e gestisce una delle più grandi reti backbone del mondo. Questa architettura globale e sofisticata, che si estende su più di 100.000 chilometri, connette i nostri data center e i clienti. 
 
Ogni giorno, i clienti di tutto il mondo si connettono e passano trilioni di richieste a Microsoft Azure, Bing, Dynamics 365, Office 365, XBox e molti altri. Indipendentemente dal tipo, i clienti si aspettano l'affidabilità immediata e la velocità di risposta dei nostri servizi. 
 
[Microsoft Global Network](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) è una parte essenziale della distribuzione di un'esperienza cloud eccezionale. Connettendo i nostri [Data Center](https://azure.microsoft.com/global-infrastructure/) Microsoft in 54 aree di Azure e una rete di grandi dimensioni di nodi perimetrali dislocati in tutto il mondo, la nostra rete globale offre la disponibilità, la capacità e la flessibilità necessaria per soddisfare qualsiasi esigenza.

![Rete globale Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Ottenere la rete cloud Premium
 
Optare per la [migliore esperienza possibile](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) è facile quando si usa Microsoft Cloud. Dal momento in cui il traffico dei clienti passa alla rete globale tramite i nodi perimetrali posizionati in modo strategico, i dati passano attraverso itinerari ottimizzati in prossimità della velocità della luce. Ciò garantisce una latenza ottimale per ottenere prestazioni ottimali. Questi nodi perimetrali, tutti interconnessi a più di 3500 partner Internet esclusivi (peer) con migliaia di connessioni in più di 145 località, costituiscono la base della strategia di interconnessione. 
 
Se ci si connette da Londra a Tokyo o da Washington DC a Los Angeles, le prestazioni di rete vengono quantificate e interessate da elementi come latenza, jitter, perdita di pacchetti e velocità effettiva.  In Microsoft si preferisce e si usano le interconnessioni dirette invece dei collegamenti di transito, in questo modo il traffico di risposta è simmetrico e consente di mantenere gli hop, le parti del peering e i percorsi il più breve e semplice possibile. 

Se, ad esempio, un utente di Londra tenta di accedere a un servizio a Tokyo, il traffico Internet entra in uno dei nostri confini a Londra, passa attraverso Microsoft WAN attraverso la Francia, i percorsi Trans-Arabia tra l'Europa e l'India e quindi il Giappone in cui è ospitato il servizio. Il traffico di risposta è simmetrico. Questo viene talvolta definito [routing con patata fredda](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) , il che significa che il traffico rimane sulla rete Microsoft il più a lungo possibile prima di distribuirlo.  
  
Quindi, significa tutto il traffico quando si usano i servizi Microsoft? Sì, il traffico tra i Data Center, all'interno Microsoft Azure o tra i servizi Microsoft, ad esempio macchine virtuali, Office 365, XBox, database SQL, archiviazione e reti virtuali, viene instradato all'interno della rete globale e mai sulla rete Internet pubblica, per garantire la corretta ottimizzazione prestazioni e integrità.  
 
Gli investimenti massivi nella capacità e nell'eterogeneità di fiber in percorsi metro, terrestri e sottomarini sono fondamentali per garantire un livello di servizio coerente e elevato, garantendo al tempo stesso la crescita estrema del cloud e Servizi online. Le aggiunte recenti alla nostra rete globale sono il nostro cavo sottomarino di [marea](https://www.submarinecablemap.com/#/submarine-cable/marea) , il primo sistema open line di settore (OLS) su sottomarini, tra Bilbao, Spagna e Virginia Beach, Virginia, USA e l' [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) tra New York, USA e Dublino, Irlanda e [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) tra Tokyo, Giappone e Portland, Oregon, Stati Uniti. 
 

## <a name="our-network-is-your-network"></a>La nostra rete è la tua rete

Sono stati rilevati due decenni di esperienza, insieme a investimenti massicci nella rete, per garantire prestazioni ottimali in qualsiasi momento. Le aziende possono sfruttare al meglio gli asset di rete e creare architetture sovrapposte avanzate. 
 
Microsoft Azure offre il portfolio più completo di servizi e funzionalità, consentendo ai clienti di creare, espandere e soddisfare in modo semplice e rapido i requisiti di rete in qualsiasi punto. La nostra famiglia di servizi di connettività si estende sul peering di reti virtuali tra aree, ibride e architetture da punto a sito e da sito a sito, nonché da scenari di transito IP globali.  Per le aziende che desiderano connettere il proprio Data Center o la propria rete ad Azure o i clienti con esigenze di inserimento o transito di dati di grandi dimensioni, [ExpressRoute](../expressroute/expressroute-introduction.md)e [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) forniscono opzioni fino a 100 Gbps di larghezza di banda, direttamente in Rete globale Microsoft in località di peering in tutto il mondo.  
 
[ExpressRoute copertura globale](../expressroute/expressroute-global-reach.md) è progettato per integrare l'implementazione WAN del provider di servizi e connettere i siti locali in tutto il mondo. Se si esegue un'operazione globale, è possibile usare ExpressRoute Copertura globale insieme ai provider di servizi preferiti e locali per connettere tutti i siti globali usando la rete globale Microsoft. Espandendo la nuova rete nel cloud (WAN) per includere un numero elevato di rami, è possibile usare la rete WAN virtuale di Azure, che offre la possibilità di connettere facilmente i rami alla rete globale Microsoft con SDWAN & i dispositivi VPN, ovvero Apparecchiature locali del cliente o CPE) con facilità di utilizzo e connettività automatizzate e gestione della configurazione. 
 
Il [peering VNet globale](../virtual-network/virtual-network-peering-overview.md) consente ai clienti di connettere facilmente due o più reti virtuali di Azure tra aree. Una volta eseguito il peering, le reti virtuali vengono visualizzate come una sola. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato tramite l'infrastruttura backbone di Microsoft, in modo analogo al traffico instradato tra le macchine virtuali nella stessa rete virtuale, solo tramite indirizzi IP privati. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Ben gestito con l'innovazione definita dal software

Con l'esecuzione di uno dei cloud principali del mondo, Microsoft ha acquisito numerose informazioni ed esperienza nella [creazione e nella gestione](https://myignite.techcommunity.microsoft.com/sessions/66668) di un'infrastruttura globale a prestazioni elevate.  
 
Ci atterremo a un solido set di principi operativi: 
 
- Usare l'hardware di cambio migliore tra i vari livelli della rete.  
- Distribuisci nuove funzionalità senza alcun effetto sugli utenti finali.  
- Implementare gli aggiornamenti in modo sicuro e affidabile tra le flotte, il più rapidamente possibile. Ore anziché settimane.  
- Usa la telemetria profonda con scalabilità cloud e la mitigazione degli errori completamente automatizzata.  
- Utilizzare la tecnologia di rete unificata e definita dal software per controllare tutti gli elementi hardware nella rete.  Eliminazione degli errori di duplicazione e riduzione. 
 
Questi principi si applicano a tutti i livelli della rete: dall'interfaccia di rete host, la piattaforma di cambio, le funzioni di rete nel data center come i bilanciamenti del carico, fino alla WAN con la piattaforma di progettazione del traffico e le reti ottiche.  
 
La crescita esponenziale di Azure e della relativa rete ha raggiunto un punto in cui ci siamo resi conto che l'intuizione umana potrebbe non essere più basata sulla gestione delle operazioni di rete globali. Per soddisfare la necessità di convalidare le modifiche a lungo, medio e a breve termine sulla rete, è stata sviluppata una piattaforma per eseguire il mirroring e emulare la rete di produzione in modo sintetico. La possibilità di creare ambienti con mirroring ed eseguire milioni di simulazioni consente di testare le modifiche apportate al software e all'hardware e al loro effetto, prima di eseguirne il commit nella piattaforma e nella rete di produzione. 

## <a name="next-steps"></a>Passaggi successivi
- [Scopri di più sui servizi di rete disponibili in Azure](https://azure.microsoft.com/product-categories/networking/)
