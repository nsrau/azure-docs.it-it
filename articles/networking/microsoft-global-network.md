---
title: Microsoft global network - Azure
description: Descrive in che modo Microsoft crea la propria rete globale veloce e affidabile
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
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453065"
---
# <a name="microsoft-global-network"></a>Rete globale Microsoft

Microsoft possiede e gestisce una delle più grandi reti backbone del mondo. Questa architettura globale e sofisticata, che si estende per oltre 100.000 miglia, collega i nostri data center e clienti. 
 
Ogni giorno, i clienti di tutto il mondo si connettono e passano migliaia di miliardi di richieste a Microsoft Azure, Bing, Dynamics 365, Office 365, XBox e molti altri. Indipendentemente dal tipo, i clienti si aspettano affidabilità e reattività istantanee dai nostri servizi. 
 
La [rete globale Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) è una parte centrale della fornitura di un'esperienza cloud eccezionale. Connettendo [i](https://azure.microsoft.com/global-infrastructure/) nostri data center Microsoft in 54 aree di Azure e una grande rete di edge-nodes posizionati strategicamente in tutto il mondo, la nostra rete globale offre sia la disponibilità, la capacità e la flessibilità per soddisfare qualsiasi domanda.

![Rete globale Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Ottieni la rete cloud premium
 
Optare per la [migliore esperienza possibile](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) è facile quando si utilizza Microsoft cloud. Dal momento in cui il traffico dei clienti entra nella nostra rete globale attraverso i nostri edge-nodi posizionati strategicamente, i tuoi dati viaggiano attraverso percorsi ottimizzati a una velocità vicina alla luce. Ciò garantisce una latenza ottimale per ottenere prestazioni ottimali. Questi edge-nodi, tutti interconnessi a più di 3500 partner Internet unici (peer) attraverso migliaia di connessioni in più di 145 sedi, forniscono le basi della nostra strategia di interconnessione. 
 
Che si tratti di connettersi da Londra a Tokyo o da Washington DC a Los Angeles, le prestazioni della rete vengono quantificate e influenzate da elementi quali latenza, jitter, perdita di pacchetti e velocità effettiva.  In Microsoft, preferiamo e usiamo le interconnessioni dirette anziché i collegamenti di transito, in modo che il traffico di risposta sia simmetrico e il problema consente di mantenere i sulmi, le parti peering e i percorsi il più brevi e semplici possibile. 

Ad esempio, se un utente a Londra tenta di accedere a un servizio a Tokyo, il traffico Internet entra in uno dei nostri bordi a Londra, passa attraverso Microsoft WAN attraverso la Francia, i percorsi Trans-Arabia tra Europa e India e quindi in Giappone in cui il servizio è ospitato. Il traffico di risposta è simmetrico. Questo è a volte indicato come [routing di patate fredde,](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) il che significa che il traffico rimane sulla rete Microsoft il più a lungo possibile prima di consegnarlo.  
  
Quindi, questo significa tutto il traffico quando si utilizzano i servizi Microsoft? Sì, qualsiasi traffico tra data center, all'interno di Microsoft Azure o tra servizi Microsoft come Macchine virtuali, Office 365, XBox, SQL DB, Archiviazione e reti virtuali viene instradato all'interno della rete globale e mai attraverso la rete Internet pubblica, per garantire prestazioni e integrità.  
 
I massicci investimenti in capacità e diversità in fibra attraverso i percorsi della metropolitana, della terra e dei sottomarini sono fondamentali per mantenere un livello di servizio coerente e elevato, alimentando al contempo l'estrema crescita dei nostri servizi cloud e online. Le recenti aggiunte alla nostra rete globale sono il nostro cavo sottomarino [MAREA,](https://www.submarinecablemap.com/#/submarine-cable/marea) il primo Open Line System (OLS) del settore sul sottomarino, tra Bilbao, Spagna e Virginia Beach, Virginia, USA, così come [l'AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) tra New York, Stati Uniti e Dublino, Irlanda e [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) tra Tokyo, Giappone e Portland, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>La nostra rete è la tua rete

Abbiamo messo due decenni di esperienza, insieme a massicci investimenti nella rete, per garantire prestazioni ottimali in ogni momento. Le aziende possono sfruttare appieno le nostre risorse di rete e creare architetture overlay avanzate in cima. 
 
Microsoft Azure offre il portafoglio più ricco di servizi e funzionalità, consentendo ai clienti di creare, espandere e soddisfare rapidamente e facilmente i requisiti di rete ovunque. La nostra famiglia di servizi di connettività si estende sul peering di reti virtuali tra aree, architetture da punto a sito e da sito a sito, nonché scenari di transito IP globale.  Per le aziende che desiderano connettere il proprio data center o rete ad Azure o i clienti con esigenze di inserimento o transito di dati di grandi dimensioni, [ExpressRoute](../expressroute/expressroute-introduction.md)ed [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) offrono opzioni fino a 100 Gbps di larghezza di banda, direttamente nella rete globale di Microsoft in sedi di peering in tutto il mondo.  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) è progettato per integrare l'implementazione WAN del provider di servizi e connettere i siti locali in tutto il mondo. Se si esegue un'operazione globale, è possibile usare La copertura globale ExpressRoute insieme ai provider di servizi locali e preferiti per connettere tutti i siti globali tramite la rete globale Microsoft.If you run a global operation, you can use ExpressRoute Global Reach in conjunction with your preferred and local service providers to connect all your global sites using the Microsoft global network. L'espansione della nuova rete nel cloud (WAN) per includere un numero elevato di siti di succursale può essere eseguita tramite la rete WAN virtuale di Azure, che consente di connettere senza problemi le filiali alla rete globale Microsoft con dispositivi SDWAN & VPN (ovvero apparecchiature locali dei clienti o CPE) con la facilità d'uso integrata e la gestione automatizzata della connettività e della configurazione. 
 
[Il peering globale della rete virtuale](../virtual-network/virtual-network-peering-overview.md) consente ai clienti di connettere due o più reti virtuali di Azure tra aree senza problemi. Una volta sottoposto a peerè, le reti virtuali vengono visualizzate come un'unica. Il traffico tra le macchine virtuali nelle reti virtuali con peering viene instradato attraverso l'infrastruttura backbone Microsoft, in modo analogo al traffico tra macchine virtuali nella stessa rete virtuale, solo tramite indirizzi IP privati. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Ben gestito utilizzando l'innovazione software-defined

Eseguendo uno dei cloud leader nel mondo, Microsoft ha acquisito molte conoscenze ed esperienza nella creazione e gestione di infrastrutture globali ad alte prestazioni.  
 
Aderiamo a una solida serie di principi operativi: 
 
- Utilizzare hardware di commutazione ottimale nei vari livelli della rete.  
- Distribuire nuove funzionalità senza impatto per gli utenti finali.  
- Implementa gli aggiornamenti in modo sicuro e affidabile in tutta la flotta, il più velocemente possibile. Ore invece di settimane.  
- Usa la telemetria profonda su scala cloud e la mitigazione degli errori completamente automatizzata.  
- Utilizzare la tecnologia di rete unificata e definita dal software per controllare tutti gli elementi hardware nella rete.  Eliminazione della duplicazione e riduzione degli errori. 
 
Questi principi si applicano a tutti i livelli della rete: dall'interfaccia di rete host, dalla piattaforma di commutazione, le funzioni di rete del data center come i Load Balancer, fino alla WAN con la nostra piattaforma di ingegneria del traffico e le nostre reti ottiche.  
 
La crescita esponenziale di Azure e della sua rete ha raggiunto un punto in cui alla fine ci siamo resi conto che l'intuizione umana non poteva più essere invocata per gestire le operazioni di rete globali. Per soddisfare la necessità di convalidare i cambiamenti a lungo, medio e breve termine sulla rete, abbiamo sviluppato una piattaforma per rispecchiare ed emulare sinteticamente la nostra rete di produzione. La capacità di creare ambienti con mirroring ed eseguire milioni di simulazioni, ci permette di testare i cambiamenti software e hardware e il loro impatto, prima di impegnarli nella nostra piattaforma di produzione e rete. 

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni sui servizi di rete forniti in AzureLearn more about the networking services provided in Azure](https://azure.microsoft.com/product-categories/networking/)
