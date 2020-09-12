---
title: Architettura di rete di Azure
description: Questo articolo offre una descrizione generale della rete di infrastruttura di Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: terrylan
ms.openlocfilehash: 3b047489f9cfa3623c11e324cf58114b707c10b7
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89567869"
---
# <a name="azure-network-architecture"></a>Architettura di rete di Azure
L'architettura di rete di Azure fornisce la connettività da Internet ai Data Center di Azure. Qualsiasi carico di lavoro distribuito (IaaS, PaaS e SaaS) in Azure sta sfruttando la rete del Data Center di Azure.

## <a name="network-topology"></a>Topologia di rete
L'architettura di rete di un Data Center di Azure è costituita dai componenti seguenti:

- Rete perimetrale
- Rete WAN
- Rete gateway regionali
- Rete datacenter

![Diagramma della rete di Azure](./media/infrastructure-network/network-arch.png)

## <a name="network-components"></a>Componenti di rete
Breve descrizione dei componenti di rete.

- Rete perimetrale

   - Punto di delimitazione tra reti Microsoft e altre reti, ad esempio Internet, rete aziendale
   - Fornisce il peering Internet e [ExpressRoute](../../expressroute/expressroute-introduction.md) in Azure

- Rete WAN

   - Rete backbone intelligente Microsoft che copre il mondo
   - Fornisce la connettività tra le [aree di Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

- Gateway a livello di area

   - Punto di aggregazione per tutti i Data Center in un'area di Azure
   - Offre connettività massiva tra i Data Center all'interno di un'area di Azure (ad esempio, più centinaia di Terabit per Data Center)

- Rete datacenter

   - Fornisce la connettività tra i server all'interno del datacenter con una larghezza di banda ridotta

I componenti di rete precedenti sono progettati per fornire la massima disponibilità per supportare le attività di cloud sempre disponibili e sempre disponibili. La ridondanza è progettata e incorporata nella rete dall'aspetto fisico fino a controllare il protocollo.

## <a name="datacenter-network-resiliency"></a>Resilienza della rete del Data Center
Di seguito viene illustrato il principio di progettazione della resilienza mediante la rete del Data Center.

La rete del Data Center è una versione modificata di una [rete Clos](https://en.wikipedia.org/wiki/Clos_network), che fornisce una larghezza di banda bidimensionale elevata per il traffico in scala cloud. La rete viene costruita usando un numero elevato di dispositivi di base per ridurre l'effetto causato da singoli guasti hardware. Questi dispositivi sono posizionati in modo strategico in posizioni fisiche diverse con un dominio di alimentazione e raffreddamento separato per ridurre l'impatto di un evento dell'ambiente.  Sul piano di controllo tutti i dispositivi di rete sono in esecuzione come modalità di routing di livello 3 OSI, che elimina il problema cronologico del ciclo di traffico. Tutti i percorsi tra livelli diversi sono attivi per offrire ridondanza elevata e larghezza di banda usando il routing ECMP (Equal-cost multipath).

Il diagramma seguente illustra che la rete del Data Center è costruita con diversi livelli di dispositivi di rete. Le barre del diagramma rappresentano gruppi di dispositivi di rete che forniscono ridondanza e connettività a larghezza di banda elevata.

![Rete datacenter](./media/infrastructure-network/datacenter-network.png)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security (Sicurezza fisica, presupposti e strutture di Azure)](physical-security.md)
- [Disponibilità dell'infrastruttura di Azure](infrastructure-availability.md)
- [Azure information system components and boundaries](infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Rete di produzione di Azure](production-network.md)
- [Funzionalità di sicurezza del database SQL di Azure](infrastructure-sql.md)
- [Azure production operations and management](infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](protection-customer-data.md)
