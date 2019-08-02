---
title: Architettura di rete di Azure
description: Questo articolo offre una descrizione generale della rete di infrastruttura di Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2019
ms.author: terrylan
ms.openlocfilehash: 3bb4addfcb5cd40eb3c98f4cec065dbe2e52246c
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68613328"
---
# <a name="azure-network-architecture"></a>Architettura di rete di Azure
L'architettura di rete di Azure segue una versione modificata del modello Core/Distribution/Access (core/di distribuzione/accesso) standard del settore, con livelli hardware distinti. I livelli includono:

- Core (router di data center)
- Distribuzione (i router di accesso e l'aggregazione L2). Il livello di distribuzione consente di separare il routing L3 dalla commutazione L2.
- Access (commutatori host L2)

L'architettura di rete ha due livelli di commutatori di livello 2. Un livello aggrega il traffico proveniente dall'altro livello. Il secondo livello viene riprodotta a ciclo continuo per incorporare la ridondanza. Questa architettura offre un footprint VLAN più flessibile e il miglioramento del ridimensionamento delle porte. L'architettura mantiene distinti i livelli L2 e L3, consentendo l'uso dell'hardware in ogni livello nella rete e riducendo al minimo la possibilità che gli errori in un livello abbiano ripercussioni sugli altri. L'uso di trunk consente la condivisione delle risorse, ad esempio la connettività all'infrastruttura L3.

## <a name="network-configuration"></a>Configurazione di rete
L'architettura di rete di un cluster di Azure all'interno di un data center include i dispositivi seguenti:

- Router (data center, router di accesso e router foglia di confine)
- Commutatori (aggregazione e commutatori Top-of-Rack)
- Digi CM
- Unità PDU (Power Distribution Unit)

Azure offre due architetture separate. Alcuni clienti e servizi condivisi di Azure si trovano nell'architettura LAN predefinita, mentre le nuove regioni e i clienti virtuali si trovano nell'architettura Quantum 10 (Q10). L'architettura LAN predefinita è una struttura ad albero tradizionale con router di accesso attivi/passivi ed elenchi di controllo di accesso di sicurezza applicati ai router di accesso. L'architettura Quantum 10 è una struttura Close/mesh di router, in cui gli ACL non vengono applicati ai router. Al contrario, gli ACL vengono applicati di sotto di routing, tramite il bilanciamento del carico software (SLB) o reti VLAN definite dal software.

Il diagramma seguente offre una panoramica generale dell'architettura di rete in un cluster di Azure:

![Diagramma della rete di Azure](./media/infrastructure-network/network-arch.png)

### <a name="quantum-10-devices"></a>Dispositivi Quantum 10
La struttura di Quantum 10 conduce la commutazione L3 in più dispositivi in una struttura Clos/mesh. I vantaggi della struttura di Q10 includono capacità superiore e maggiore scalabilità dell'infrastruttura di rete esistente. La struttura impiega router foglia di confine, commutatori a spina e router Top-of-Rack per passare il traffico al cluster su più route, consentendo la tolleranza di errore. Servizi di sicurezza come Network Address Translation (NAT) vengono gestiti tramite bilanciamento del carico software anziché nei dispositivi hardware.

### <a name="access-routers"></a>Router di accesso
I router L3 di distribuzione/accesso eseguono la funzionalità di routing primaria per i livelli Distribution e Access. Questi dispositivi vengono distribuiti in coppia e costituiscono il gateway predefinito per le subnet. Ogni coppia di router di accesso può supportare più coppie di commutatori di aggregazione L2, a seconda della capacità. Il numero massimo dipende la capacità del dispositivo, nonché dai domini di errore. Un numero tipico è tre coppie di commutatore di aggregazione di L2 per ogni coppia di AR.

### <a name="l2-aggregation-switches"></a>Commutatori di aggregazione L2  
Questi dispositivi fungono da punto di aggregazione per il traffico L2. Rappresentano il livello di distribuzione per l'infrastruttura L2 e possono gestire volumi elevati di traffico. Poiché questi dispositivi aggregano traffico, sono necessarie la funzionalità 802.1q e le tecnologie a elevata larghezza di banda, come 10GE e l'aggregazione delle porte.

### <a name="l2-host-switches"></a>Commutatori host L2
Gli host si connettono direttamente a questi commutatori. Possono essere commutatori montati in rack switch o distribuzioni di chassis. Lo standard 802.1q consente la designazione di una VLAN come nativa, considerandola un normale frame Ethernet (senza tag). In circostanze normali, i frame nella VLAN nativa vengono trasmessi e ricevuti senza tag su un porta trunk 802.1q. Questa funzionalità è stata progettata per la migrazione a 802.1q e la compatibilità con dispositivi non conformi a 802.1q. In questa architettura solo l'infrastruttura di rete usa la VLAN nativa.

Questa architettura consente di specificare uno standard per la selezione delle VLAN nativa. Questo standard, laddove possibile, garantisce che i router di accesso abbiano una VLAN nativa univoca per ogni trunk e per i trunk da L2Aggregation a L2Aggregation. I trunk dei commutatori da L2Aggregation a L2Host hanno una VLAN nativa non predefinita.

### <a name="link-aggregation-8023ad"></a>Link Aggregation (802.3ad)
Link Aggregation consente di aggregare singoli collegamenti e considerarli come un unico collegamento logico. Per facilitare il debug operativo, il numero usato per designare le interfacce di porta-canale deve essere standardizzato. Il resto della rete utilizza lo stesso numero per entrambe le estremità di una porta-canale.

I numeri specificati per il commutatore da L2Agg a L2Host sono i numeri di porta-canale usati sul lato L2Agg. Poiché l'intervallo di numeri è più limitato sul lato L2Host, lo standard consiste nell'usare i numeri 1 e 2 sul lato L2Host. Questi elementi fanno riferimento alla porta-canale rispettivamente al lato "a" e "b".

### <a name="vlans"></a>VLAN
L'architettura di rete usa le VLAN per raggruppare i server in un singolo dominio di trasmissione. I numeri delle VLAN sono conformi allo standard 802.1q, che supporta le VLAN numerate da 1 a 4094.

### <a name="customer-vlans"></a>VLAN dei clienti
I clienti possono distribuire varie opzioni di implementazione VLAN tramite il portale di Azure per soddisfare le esigenze della soluzione a livello di separazione e architettura. Queste soluzioni vengono distribuite tramite macchine virtuali. Per esempi di architetture di riferimento dei clienti, visitare [Architetture di riferimento di Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/).

### <a name="edge-architecture"></a>Architettura perimetrale
I data center di Azure sono basati su infrastrutture di rete altamente ridondanti e con provisioning adeguato. Le reti nei data center di Azure vengono implementate da Microsoft con architetture con ridondanza N+1 (need plus one) o superiori. Le funzionalità di failover completo all'interno e tra i data center garantiscono la disponibilità delle reti e dei servizi. Esternamente, i data center vengono gestite da circuiti di rete dedicati a larghezza di banda elevata. Questi circuiti connettono in modo ridondante le proprietà con oltre 1200 provider di servizi Internet a livello globale in più punti di peering. Ciò offre oltre 2.000 Gbps di capacità perimetrale potenziale all'interno della rete.

I router di filtraggio ai livelli di perimetro e accesso della rete di Azure garantiscono un grado di sicurezza ben definito a livello di pacchetti e impediscono tentativi non autorizzati di connessione ad Azure. I router garantiscono che il contenuto effettivo dei pacchetti includa dati nel formato previsto e conformi allo schema di comunicazione client/server previsto. Azure implementa un'architettura a più livelli costituita dai componenti di separazione delle reti e di controllo degli accessi seguenti:

- **Router perimetrali.** Separano l'ambiente applicativo da Internet. I router perimetrali sono progettati per garantire protezione anti-spoofing e limitare l'accesso tramite elenchi di controllo di accesso (ACL).
- **Router di distribuzione (accesso).** Sono progettati per consentire solo gli indirizzi IP approvati da Microsoft, fornire l'anti-spoofing e stabilire connessioni mediante ACL.

### <a name="ddos-mitigation"></a>Mitigazione DDoS
Gli attacchi Distributed Denial of Service (DDoS) continuano a costituire una minaccia reale all'affidabilità dei Servizi online. In un momento in cui gli attacchi diventano sempre più mirati e sofisticati e i servizi Microsoft sempre più diversificati a livello geografico, la capacità di identificare e ridurre al minimo l'impatto di questi attacchi è della massima priorità.

La [protezione DDoS di Azure Standard](../../virtual-network/ddos-protection-overview.md) offre un meccanismo di difesa dagli attacchi DDoS. Per altre informazioni, vedere [Protezione DDoS di Azure: procedure consigliate e architetture di riferimento](ddos-best-practices.md).

> [!NOTE]
> Microsoft offre protezione DDoS per impostazione predefinita a tutti i clienti Azure.
>
>

## <a name="network-connection-rules"></a>Regole di connessione di rete
Azure distribuisce router perimetrali nella rete che garantiscono sicurezza a livello di pacchetti per impedire tentativi non autorizzati di connessione ad Azure. I router perimetrali garantiscono che il contenuto effettivo dei pacchetti includa dati nel formato previsto e sia conforme allo schema di comunicazione client/server previsto.

I router perimetrali separano l'ambiente applicativo da Internet. Sono progettati per garantire protezione anti-spoofing e limitare l'accesso tramite elenchi di controllo di accesso (ACL). Questi router perimetrali vengono configurati da Microsoft adottando un approccio ACL a più livelli per limitare i protocolli di rete autorizzati ad attraversare i router perimetrali e di accesso.

I dispositivi di rete sono posizionati da Microsoft all'accesso e al perimetro per rappresentare le delimitazioni in cui si applicano filtri in ingresso o in uscita.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security](physical-security.md) (Sicurezza fisica, presupposti e strutture di Azure)
- [Disponibilità dell'infrastruttura di Azure](infrastructure-availability.md)
- [Azure information system components and boundaries](infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Rete di produzione di Azure](production-network.md)
- [Funzionalità di sicurezza del database SQL di Azure](infrastructure-sql.md)
- [Azure production operations and management](infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](protection-customer-data.md)


