---
title: Panoramica del servizio peering di Azure
description: Informazioni sul servizio peering di Azure-Panoramica
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 548d4f90bd9632e4807547120ac1be589668e8fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871341"
---
# <a name="azure-peering-service-overview"></a>Panoramica del servizio peering di Azure

Il servizio di peering di Azure è un servizio di rete che migliora la connettività dei clienti ai servizi cloud Microsoft, come Office 365, Dynamics 365, i servizi SaaS (Software as a Service), Azure o qualsiasi servizio Microsoft accessibile tramite la rete Internet pubblica. Microsoft ha collaborato con i provider di servizi Internet (ISP), Internet Exchange partner (IXPs) e i provider SDCI (software-defined cloud Interconnect) in tutto il mondo per offrire una connettività pubblica affidabile e ad alte prestazioni con routing ottimale dal cliente alla rete Microsoft.

Con il servizio peering, i clienti possono selezionare un provider di servizi partner ben connesso in una determinata area. La connettività pubblica è ottimizzata per un'affidabilità elevata e una latenza minima dai servizi cloud al percorso dell'utente finale.

![Connettività distribuita a Microsoft Cloud](./media/peering-service-about/peering-service-what.png)

I clienti possono anche scegliere la telemetria del servizio di peering, ad esempio le misure di latenza utente per la rete Microsoft, il monitoraggio della route BGP e gli avvisi contro perdite e Hijack registrando la connessione del servizio di peering nel portale di Azure. 

Per usare il servizio di peering, non è necessario che i clienti si registrino a Microsoft. L'unico requisito consiste nel contattare un [partner del servizio di peering](location-partners.md) per ottenere il servizio. Per acconsentire esplicitamente alla telemetria del servizio di peering, i clienti devono registrarsi nel portale di Azure.

Per istruzioni su come registrare il servizio di peering, vedere [registrare il servizio di peering usando il portale di Azure](azure-portal.md). 

> [!NOTE]
> Questo articolo è destinato agli architetti di rete responsabili della connettività aziendale al cloud e a Internet.


## <a name="what-is-peering-service"></a>Che cos'è il Servizio di peering?

Il servizio peering è:

- Un servizio IP che usa la rete Internet pubblica. 
- Piattaforma di collaborazione con provider di servizi e un servizio a valore aggiunto progettato per offrire routing ottimale e affidabile al cliente tramite partner di provider di servizi al cloud Microsoft attraverso la rete pubblica.

Il servizio di peering non è un prodotto di connettività privato come Azure ExpressRoute o un prodotto VPN.

> [!NOTE]
> Per ulteriori informazioni su ExpressRoute, vedere la [documentazione di ExpressRoute](https://docs.microsoft.com/azure/expressroute/).
>

## <a name="background"></a>Sfondo

Office 365, Dynamics 365 e qualsiasi altro servizio SaaS Microsoft sono ospitati in più Data Center Microsoft ed è possibile accedervi da qualsiasi posizione geografica. La rete globale Microsoft dispone di località PoP (Point of Presence) Microsoft Edge in tutto il mondo in cui è in grado di connettersi a un utente finale tramite i propri provider di servizi. 

I provider di servizi Microsoft e dei partner assicurano che il traffico per i prefissi registrati con una connessione al servizio di peering entri e esca dalle località PoP Microsoft Edge più vicine sulla rete globale Microsoft. Microsoft garantisce che il traffico di rete in uscita da dai prefissi registrati con le connessioni al servizio di peering accetti le località PoP Microsoft Edge più vicine sulla rete globale Microsoft.

![Rete Microsoft e connettività pubblica](./media/peering-service-about/peering-service-background-final.png)

> [!NOTE]
> Per ulteriori informazioni sulla rete globale Microsoft, vedere la pagina relativa alla [rete globale Microsoft](https://docs.microsoft.com/azure/networking/microsoft-global-network).
>

## <a name="why-use-peering-service"></a>Perché usare il servizio di peering?

Le aziende che cercano il primo accesso Internet al cloud o che considerano l'architettura SD-WAN o con un utilizzo elevato dei servizi SaaS Microsoft necessitano di una connettività Internet affidabile e ad alte prestazioni. I clienti possono eseguire tale transizione usando il servizio di peering. Microsoft e i provider di servizi hanno collaborato per offrire connettività pubblica affidabile e incentrata sulle prestazioni al cloud Microsoft. Di seguito sono elencate alcune delle principali funzionalità del cliente:

- Migliore routing pubblico tramite Internet per Servizi cloud di Microsoft Azure prestazioni e affidabilità ottimali.
- Possibilità di selezionare il provider di servizi preferito per la connessione al cloud Microsoft.
- Informazioni dettagliate sul traffico, ad esempio la segnalazione della latenza e il monitoraggio del prefisso.
- Hop di rete ottimale (hop) dal cloud Microsoft.
- Analisi delle route e statistiche: eventi per ([BGP](https://en.wikipedia.org/wiki/Border_Gateway_Protocol)) anomalie di route (rilevamento di perdite o Hijack) e routing non ottimale.

### <a name="robust-reliable-peering"></a>Peering affidabile e affidabile

Il servizio di peering usa due tipi di ridondanza:

- **Ridondanza locale**

   Microsoft e i provider di servizi interconnettono tra più sedi PoP Microsoft Edge per fornire il servizio di peering. In ogni posizione, l'interconnessione deve supportare il failover tra due router.

   Ogni località di peering viene sottoposta a provisioning con collegamenti di peering ridondanti e diversificati.

- **Ridondanza geografica**

   Microsoft si è interconnessa con i provider di servizi in più posizioni della metro, in modo che se uno dei nodi perimetrali ha ripercussioni negative sulle prestazioni, il traffico viene indirizzato verso e da Microsoft tramite siti alternativi. Microsoft instrada il traffico nella propria rete globale usando i criteri di routing basati su SDN per ottenere prestazioni ottimali.

    Questo tipo di ridondanza usa il percorso di routing più breve scegliendo sempre il PoP Microsoft Edge più vicino all'utente finale e garantisce che il cliente sia un hop di rete (hop) lontano da Microsoft.

   ![Ridondanza geografica](./media/peering-service-about/peering-service-geo-shortest.png)

### <a name="optimal-routing"></a>Routing ottimale

È preferibile la seguente tecnica di routing:

-  **Routing con patata fredda**

   La tecnica di routing di patata fredda definita dal software offre il controllo sul traffico di rete originato da Microsoft Cloud. Garantisce che il traffico rimanga sulla rete globale Microsoft a capacità elevata, a bassa latenza e altamente affidabile fino a quando non è il più possibile vicino alla destinazione.
   
   Il routing che non usa la tecnica di patata fredda viene definito routing con patata a caldo. Con il routing Hot-Potato, il traffico originato da Microsoft Cloud passa quindi attraverso Internet.

   ![Routing con patata fredda](./media/peering-service-about/peering-service-cold-potato.png)

### <a name="monitoring-platform"></a>Piattaforma di monitoraggio

   Il monitoraggio del servizio viene offerto per analizzare il traffico e il routing dei clienti e offre le funzionalità seguenti: 

-  **Rilevamento delle anomalie della route BGP Internet**
          
   Questo servizio viene usato per rilevare e inviare avvisi per gli eventi di anomalia di route, ad esempio Hijack Route ai prefissi dei clienti.

-  **Latenza cliente**

   Questo servizio monitora le prestazioni di routing tra la località del cliente e Microsoft. 
   
   Il routing delle prestazioni viene misurato convalidando il tempo di round trip impiegato dal client per raggiungere il PoP Microsoft Edge. I clienti possono visualizzare i report sulla latenza per diverse posizioni geografiche.

   Il monitoraggio acquisisce gli eventi in caso di riduzione del servizio.

   ![Piattaforma di monitoraggio per il servizio di peering](media/peering-service-about/peering-service-latency-report.png)

### <a name="traffic-protection"></a>Protezione del traffico

Il routing avviene solo tramite un percorso preferito che viene definito quando il cliente viene registrato con il servizio di peering.

Microsoft garantisce di instradare il traffico tramite percorsi preferiti anche se viene rilevata un'attività dannosa.

Le anomalie della route BGP vengono segnalate nel portale di Azure, se presenti.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulle connessioni al servizio di peering, vedere [peering Service Connections](connection.md).
- Per informazioni sui dati di telemetria delle connessioni al servizio di peering, vedere [Telemetria delle connessioni al servizio di peering](connection-telemetry.md).
- Per trovare un partner del provider di servizi, vedere [peering Service Partners and locations](location-partners.md).
- Per caricare una connessione al servizio di peering, vedere il [modello di servizio di peering di onboarding](onboarding-model.md).
- Per registrare una connessione usando il portale di Azure, vedere [registrare una connessione al servizio di peering usando il portale di Azure](azure-portal.md).
- Per misurare i dati di telemetria, vedere [Misurare la telemetria delle connessioni](measure-connection-telemetry.md).
