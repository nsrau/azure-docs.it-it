---
title: Architettura di rete di Azure
description: Questo articolo offre una descrizione generale della rete di infrastruttura di Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 67781f196b445c9330e0dcb1fc7d8b0a1a53cbc0
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101433"
---
# <a name="azure-network-architecture"></a>Architettura di rete di Azure
L'architettura di rete di Azure segue una versione modificata del modello Core/Distribution/Access (core/di distribuzione/accesso) standard del settore, con livelli hardware distinti. I livelli includono:

- Core (router di data center)
- Distribution (router di accesso e aggregazione L2): il livello Distribution separa il routing L3 dalla commutazione L2
- Access (commutatori host L2)

L'architettura di rete include due livelli di switch L2, un livello di aggregazione del traffico dall'altro e cicli L2 per incorporare la ridondanza. Ne conseguono vantaggi come un footprint VLAN più flessibile e il miglioramento del ridimensionamento delle porte. L'architettura mantiene distinti i livelli L2 e L3, consentendo l'uso dell'hardware in ogni livello nella rete e riducendo al minimo la possibilità che gli errori in un livello abbiano ripercussioni sugli altri. L'uso di trunk consente la condivisione delle risorse, ad esempio la connettività all'infrastruttura L3.

## <a name="network-configuration"></a>Network configuration
L'architettura di rete di un cluster Microsoft Azure all'interno di un data center include i dispositivi seguenti:

- Router (data center, router di accesso e router foglia di confine)
- Commutatori (aggregazione e commutatori Top-of-Rack)
- Digi CM
- PDU o Nucleon

La figura seguente offre una panoramica generale dell'architettura di rete di Azure all'interno di un cluster. Azure offre due architetture separate. Alcuni clienti e servizi condivisi di Azure si trovano nell'architettura LAN predefinita, mentre le nuove regioni e i clienti virtuali saranno accessibili tramite l'architettura Quantum 10 (Q10). L'architettura LAN predefinita è una struttura ad albero tradizionale router di accesso attivi/passivi e ACL di sicurezza applicati ai router di accesso. L'architettura Quantum 10 è una struttura chiusura/mesh di router in cui gli ACL non vengono applicati nei router ma al di sotto del routing tramite VLAN definite dal software o bilanciamento del carico software.

![Rete di Azure][1]

### <a name="quantum-10-devices"></a>Dispositivi Quantum 10
La struttura di Quantum 10 conduce lo switching L3 in più dispositivi in una struttura chiusura/mesh. I vantaggi della struttura di Q10 includono capacità superiore e maggiore scalabilità dell'infrastruttura di rete esistente. La struttura impiega router foglia di confine, commutatori a spina e router Top-of-Rack per passare il traffico al cluster su più route, consentendo la tolleranza di errore. Servizi di sicurezza come Network Address Translation (NAT) vengono gestiti tramite bilanciamento del carico software anziché nei dispositivi hardware.

### <a name="access-routers"></a>Router di accesso
I router L3 di distribuzione/accesso eseguono la funzionalità di routing primaria per i livelli Distribution e Access. Questi dispositivi vengono distribuiti in coppia e costituiscono il gateway predefinito per le subnet. Ogni coppia di router di accesso può supportare più coppie di commutatori di aggregazione L2, a seconda della capacità. Il numero massimo dipende la capacità del dispositivo, nonché dai domini di errore. Un numero tipico in base alla capacità prevista è 3 coppie di commutatori di aggregazione L2 per coppia di router di accesso.

### <a name="l2-aggregation-switches"></a>Commutatori di aggregazione L2  
Questi dispositivi fungono da punto di aggregazione per il traffico L2. Rappresentano il livello di distribuzione per l'infrastruttura L2 e possono gestire volumi elevati di traffico. Poiché questi dispositivi aggregano traffico, è necessaria la funzionalità 802.1q e vengono usate tecnologie a elevata larghezza di banda, come 10GE e l'aggregazione delle porte.

### <a name="l2-host-switches"></a>Commutatori host L2
Gli host si connettono direttamente a questi commutatori. Possono essere commutatori montati in rack switch o distribuzioni di chassis. Lo standard 802.1q consente la designazione di una VLAN come nativa, considerandola un normale frame Ethernet (senza tag). In circostanze normali, i frame nella VLAN nativa vengono trasmessi e ricevuti senza tag su un porta trunk 802.1q. Questa funzionalità è stata progettata per la migrazione a 802.1q e la compatibilità con dispositivi non conformi a 802.1q. In questa architettura solo l'infrastruttura di rete usa la VLAN nativa.

Questa architettura specifica uno standard per la selezione della VLAN nativa che, laddove possibile, garantisce che i router di accesso abbiano una VLAN nativa univoca per ogni trunk e i trunk da L2Aggregation a L2Aggregation. I trunk dei commutatori da L2Aggregation a L2Host hanno una VLAN nativa non predefinita.

### <a name="link-aggregation-8023ad"></a>Link Aggregation (802.3ad)
Link Aggregation (LAG) consente di aggregare singoli collegamenti e considerarli come un unico collegamento logico. Il numero usato per designare le interfacce porta-canale deve essere standardizzato in modo da semplificare il debug delle operazioni. Il resto della rete userà lo stesso numero a entrambe le estremità di una porta-canale. È necessario uno standard per determinare quale estremità della porta-canale usare per definire il successivo numero disponibile.

I numeri specificati per il commutatore da L2Agg a L2Host sono i numeri di porta-canale usati sul lato L2Agg. Poiché l'intervallo di numeri è più limitato sul lato L2Host, lo standard consiste nell'usare i numeri 1 e 2 sul lato L2Host per fare riferimento alla porta-canale rispettivamente al lato "a" e "b".

### <a name="vlans"></a>VLAN
L'architettura di rete usa le VLAN per raggruppare i server in un singolo dominio di trasmissione. I numeri delle VLAN sono conformi allo standard 802.1q, che supporta le VLAN numerate da 1 a 4.094.

### <a name="customer-vlans"></a>VLAN dei clienti
I clienti possono distribuire varie opzioni di implementazione VLAN tramite il portale di Azure per soddisfare le esigenze della soluzione a livello di separazione e architettura. Queste soluzioni vengono distribuite tramite macchine virtuali. Per esempi di architetture di riferimento dei clienti, visitare [Architetture di riferimento di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architettura perimetrale
I data center di Azure sono basati su infrastrutture di rete altamente ridondanti e con provisioning adeguato. Le reti nei data center di Azure vengono implementate con architetture con ridondanza N+1 (need plus one) o superiori. Le funzionalità di failover completo all'interno e tra i data center garantiscono la disponibilità delle reti e dei servizi. Esternamente, i data center vengono serviti da circuiti di rete dedicati a larghezza di banda elevata che connettono in modo ridondante le proprietà con oltre 1.200 provider di servizi Internet a livello globale in più punti di peering che offrono oltre 2.000 Gbps di potenziale capacità perimetrale nella rete.

I router di filtraggio ai livelli di perimetro e accesso della rete di Microsoft Azure garantiscono un grado di sicurezza ben definito a livello di pacchetti per impedire tentativi non autorizzati di connessione ad Azure. Garantiscono che il contenuto effettivo dei pacchetti includa dati nel formato previsto e conformi allo schema di comunicazione client/server previsto. Azure implementa un'architettura a più livelli costituita dai componenti di separazione delle reti e controllo degli accessi seguenti:

- Router perimetrali: separano l'ambiente applicativo da Internet. I router perimetrali sono progettati per garantire protezione anti-spoofing e limitare l'accesso tramite elenchi di controllo di accesso (ACL).
- Router di (accesso) distribuzione: i router di accesso sono progettati per consentire solo gli indirizzi IP approvati da Microsoft, fornire l'anti-spoofing e stabilire connessioni mediante ACL.

### <a name="a10-ddos-mitigation-architecture"></a>Architettura di mitigazione DDoS A10
Gli attacchi Denial of Service continuano a costituire una minaccia reale all'affidabilità dei servizi online Microsoft. In un momento in cui gli attacchi diventano sempre più mirati e sofisticati e i servizi Microsoft sempre più diversificati a livello geografico, la capacità di fornire meccanismi efficaci per identificare e ridurre al minimo l'impatto di questi attacchi è della massima priorità.

Di seguito viene spiegato in che modo il sistema di mitigazione DDoS A10 viene implementato dalla prospettiva dell'architettura di rete.  
Microsoft Azure usa dispositivi di rete A10 nel router del data center che offrono prevenzione e mitigazione automatiche. La soluzione A10 usa il monitoraggio di rete di Azure per campionare i pacchetti di flussi di rete e determinare l'eventuale presenza di un attacco. Una volta rilevato l'attacco, i dispositivi A10 vengono usati come strumenti di scrubbing per mitigare gli attacchi. In seguito alla mitigazione verrà consentito altro traffico pulito nel data center di Azure direttamente dal router del data center. La soluzione A10 viene usata per proteggere l'infrastruttura di rete di Azure.

I tipi di protezione DDoS nella soluzione A10 includono:

- Protezione da flood UDP per IPv4 e IPv6
- Protezione da flood ICMP per IPv4 e IPv6
- Protezione da flood TCP per IPv4 e IPv6
- Protezione da attacchi SYN TCP per IPv4 e IPv6
- Attacco di frammentazione

> [!NOTE]
> La protezione DDoS è disponibile per impostazione predefinita per tutti i clienti Azure.
>
>

## <a name="network-connection-rules"></a>Regole di connessione di rete
Azure distribuisce router perimetrali nella rete che garantiscono sicurezza a livello di pacchetti per impedire tentativi non autorizzati di connessione a Microsoft Azure. Garantiscono che il contenuto effettivo dei pacchetti includa dati nel formato previsto e conformi allo schema di comunicazione client/server previsto.

I router perimetrali separano l'ambiente applicativo da Internet. I router perimetrali sono progettati per garantire protezione anti-spoofing e limitare l'accesso tramite elenchi di controllo di accesso (ACL). Questi router perimetrali vengono configurati adottando un approccio ACL a più livelli per limitare i protocolli di rete autorizzati ad attraversare i router perimetrali e di accesso.

I dispositivi di rete sono posizionati all'accesso e al perimetro e rappresentano le delimitazioni in cui si applicano filtri in ingresso e/o in uscita.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](azure-physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Availability of Azure infrastructure](azure-infrastructure-availability.md) (Disponibilità dell'infrastruttura di Azure)
- [Azure information system components and boundaries](azure-infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Rete di produzione di Azure](azure-production-network.md)
- [Funzionalità di sicurezza del database SQL di Microsoft Azure](azure-infrastructure-sql.md)
- [Azure production operations and management](azure-infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoring of Azure infrastructure](azure-infrastructure-monitoring.md) (Monitoraggio dell'infrastruttura di Azure)
- [Integrity of Azure infrastructure](azure-infrastructure-integrity.md) (Integrità dell'infrastruttura di Azure)
- [Protezione dei dati dei clienti in Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-infrastructure-network/network-arch.png
