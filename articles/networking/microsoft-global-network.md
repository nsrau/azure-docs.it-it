---
title: Rete globale Microsoft - Azure
description: Scopri in che modo Microsoft sviluppa e gestisce una delle più grandi reti backbone del mondo e perché è fondamentale per la realizzazione di un'esperienza cloud eccezionale.
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
ms.openlocfilehash: 05e966d3f4631e3c344bd97c0faec99cc3c6dd66
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91819891"
---
# <a name="microsoft-global-network"></a>Rete globale Microsoft

Microsoft possiede e gestisce una delle più grandi reti backbone del mondo. Questa architettura globale e sofisticata, che si estende su più di 165.000 chilometri, connette i nostri data center e i clienti. 
 
Ogni giorno, i clienti di tutto il mondo si connettono e passano trilioni di richieste a Microsoft Azure, Bing, Dynamics 365, Microsoft 365, XBox e molti altri. Indipendentemente dal tipo, i clienti si aspettano affidabilità e velocità di risposta istantanee dai servizi Microsoft. 
 
La [rete globale Microsoft](https://azure.microsoft.com/global-infrastructure/global-network/) è una parte essenziale dell'erogazione di un'esperienza eccezionale sul cloud. Connettendo i nostri [Data Center](https://azure.microsoft.com/global-infrastructure/) Microsoft in 61 aree di Azure e una rete di grandi dimensioni di nodi perimetrali dislocati in tutto il mondo, la nostra rete globale offre la disponibilità, la capacità e la flessibilità necessaria per soddisfare qualsiasi esigenza.

![Rete globale Microsoft](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Rete cloud all'avanguardia
 
Usando il cloud Microsoft, è facile scegliere la [migliore esperienza possibile](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/). Dal momento in cui il traffico dei clienti arriva nella rete globale attraverso i nodi perimetrali ubicati strategicamente, i dati si spostano su route ottimizzate quasi alla velocità della luce. In questo modo, si ottiene latenza ottimale per le migliori prestazioni. Questi nodi perimetrali, tutti interconnessi a più di 4000 partner Internet esclusivi (peer) con migliaia di connessioni in più di 175 località, costituiscono la base della strategia di interconnessione. 
 
Per connessioni da Londra a Tokyo, da Washington DC a Los Angeles, le prestazioni di rete vengono quantificate e interessate da fattori come latenza, instabilità, perdita di pacchetti e velocità effettiva.  Microsoft preferisce e usa interconnessioni dirette anziché collegamenti di transito, perché queste mantengono il traffico di risposta simmetrico e gli hop, le parti di peering e i percorsi quanto più brevi e semplici possibile. 

Se, ad esempio, un utente a Londra tenta di accedere a un servizio a Tokyo, il traffico Internet raggiunge una delle reti perimetrali a Londra, attraversa la rete WAN Microsoft in Francia, segue i percorsi in Trans-Arabia tra Europa e India e quindi arriva in Giappone, dove è ospitato il servizio. Il traffico di risposta è simmetrico. Questo approccio è noto come [cold-potato routing](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing), termine che indica che il traffico resta nella rete Microsoft il più a lungo possibile prima di essere passato ad altre reti.  
  
Si intende proprio tutto il traffico legato all'uso di servizi Microsoft? Sì, il traffico tra i Data Center, all'interno Microsoft Azure o tra i servizi Microsoft, ad esempio macchine virtuali, Microsoft 365, XBox, database SQL, archiviazione e reti virtuali, viene instradato all'interno della rete globale e mai sulla rete Internet pubblica, per garantire prestazioni e integrità ottimali.  
 
Ingenti investimenti nella capacità e nell'eterogeneità della fibra ottica su traiettorie metropolitane, terrestri e sottomarine sono essenziali per Microsoft per mantenere un livello di servizio uniforme ed elevato, garantendo una crescita illimitata del cloud e dei servizi online. Le novità aggiunte di recente alla rete globale sono il cavo sottomarino [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea), il primo sistema a linea aperta (OLS, Open Line System) sottomarino del settore, tra Bilbao in Spagna e Virginia Beach in Virginia, negli Stati Uniti, insieme ad [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1), tra New York negli Stati Uniti e Dublino in Irlanda, e [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) tra Tokyo in Giappone e Portland in Oregon, negli Stati Uniti. 
 

## <a name="our-network-is-your-network"></a>La rete Microsoft è la rete di ogni utente

Microsoft ha dedicato alla propria rete due decenni di esperienza e ingenti investimenti, per garantire prestazioni ottimali in ogni momento. Le aziende possono sfruttare al meglio gli asset di rete per creare architetture sovrapposte avanzate. 
 
Microsoft Azure offre la gamma più ricca di servizi e funzionalità, permettendo ai clienti di creare, espandere e soddisfare requisiti di rete rapidamente e facilmente, ovunque. La famiglia di servizi di connettività comprende peering di reti virtuali tra aree, architetture da punto a sito e da sito a sito ibride e nel cloud e scenari di transito di proprietà intellettuale globali.  Per le aziende che desiderano connettere il proprio data center o la propria rete ad Azure o i clienti con esigenze complesse di inserimento o transito di dati, [ExpressRoute](../expressroute/expressroute-introduction.md) ed [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) offrono opzioni fino a 100 Gbps di larghezza di banda, direttamente nella rete globale Microsoft in località di peering in tutto il mondo.  
 
[Copertura globale di ExpressRoute](../expressroute/expressroute-global-reach.md) è un servizio progettato per integrare l'implementazione WAN del provider di servizi e la connessione di siti locali in tutto il mondo. Chi si occupa della gestione di operazioni globali può usare Copertura globale di ExpressRoute insieme ai provider di servizi locali preferiti per connettere tutti i siti globali tramite la rete globale Microsoft. L'espansione della nuova rete nel cloud (WAN) in modo da includere un numero elevato di filiali può essere attuata tramite la rete WAN virtuale di Azure, che permette di connettere uniformemente le filiali alla rete globale Microsoft con dispositivi SDWAN e VPN (ovvero, le apparecchiature locali del cliente) con semplicità d'uso integrata e gestione automatica della connettività e della configurazione. 
 
Il [peering di reti virtuali globale](../virtual-network/virtual-network-peering-overview.md) consente ai clienti di connettere facilmente due o più reti virtuali di Azure tra aree. Dopo avere eseguito il peering, le reti virtuali vengono visualizzate come una sola. Il traffico tra le macchine virtuali presenti nelle reti virtuali con peering viene instradato attraverso l'infrastruttura backbone di Microsoft, in modo simile all'instradamento del traffico tra macchine virtuali presenti nella stessa rete virtuale, solo tramite indirizzi IP privati. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Ottima gestione grazie all'innovazione software-defined

L'esecuzione di uno degli ambienti cloud leader mondiali del settore ha permesso a Microsoft di acquisire molti dati analitici e una vasta esperienza nella creazione e nella gestione di un'infrastruttura globale ad alte prestazioni.  
 
Viene applicato un solido insieme di principi operativi: 
 
- Usare hardware di commutazione all'avanguardia tra i diversi livelli della rete.  
- Distribuire nuove funzionalità con nessun impatto per gli utenti finali.  
- Implementare aggiornamenti in modo sicuro e affidabile in tutta la gamma di servizi. Tempi operativi in termini di ore e non di settimane.  
- Utilizzare telemetria avanzata di livello cloud e prevenzione degli errori completamente automatizzata.  
- Usare tecnologie di rete unificate e software-defined per controllare tutti gli elementi hardware della rete.  Eliminare la duplicazione e ridurre gli errori. 
 
Questi principi si applicano a tutti i livelli della rete: dall'interfaccia di rete host alla piattaforma di commutazione, dalle funzioni di rete nel data center, come i servizi di bilanciamento del carico, fino alla rete WAN con una piattaforma di progettazione del traffico e reti ottiche.  
 
La crescita esponenziale di Azure e della sua rete ha raggiunto un punto in cui ci si è resi conto che l'intuizione umana non poteva essere più del tutto attendibile per la gestione delle operazioni di rete globali. Per soddisfare l'esigenza di convalidare modifiche a lungo, medio e breve termine sulla rete, è stata sviluppata una piattaforma per il mirroring e l'emulazione della rete di produzione in modo sintetico. La capacità di creare ambienti con mirroring ed eseguire milioni di simulazioni consente di testare le modifiche software e hardware e il rispettivo impatto, prima di eseguirne il commit nella piattaforma e nella rete di produzione. 

## <a name="next-steps"></a>Passaggi successivi
- [Altre informazioni sui servizi di rete disponibili in Azure](https://azure.microsoft.com/product-categories/networking/)
