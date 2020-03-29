---
title: Ripristino di emergenza geografico in Griglia di eventi di Azure Documenti Microsoft
description: Descrive come Griglia di eventi di Azure supporta automaticamente il ripristino di emergenza geografico (GeoDR).
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66307318"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Ripristino di emergenza geografico lato server nella griglia di eventi di AzureServer-side geo disaster recovery in Azure Event Grid
Griglia di eventi dispone ora di un ripristino di emergenza geografico automatico (GeoDR) dei metadati non solo per i nuovi domini, ma tutti i domini, gli argomenti e le sottoscrizioni di eventi esistenti. Se un'intera area di Azure si arresta, In Griglia di eventi tutti i metadati dell'infrastruttura correlati agli eventi verranno sincronizzati con un'area associata. I tuoi nuovi eventi inizieranno a fluire di nuovo senza alcun intervento da parte tua. 

Il ripristino di emergenza viene misurato con due metriche:Disaster recovery is measured with two metrics:

- [Recovery Point Objective (RPO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective)i minuti o le ore di dati che potrebbero andare persi.
- [Recovery Time Objective (RTO):](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective)minuti di minuti di ineredimento del servizio.

Il failover automatico della griglia di eventi ha RPO e RTO diversi per i metadati (sottoscrizioni di eventi e così via) e i dati (eventi). Se sono necessarie specifiche diverse dalle seguenti, è comunque possibile implementare il [failover sul lato client utilizzando l'argomento health apis](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Obiettivo del punto di ripristino (RPO)
- **RPO metadati**: zero minuti. Ogni volta che una risorsa viene creata in Griglia di eventi, viene replicata istantaneamente tra le aree. Quando si verifica un failover, non viene perso alcun metadati.
- **Data RPO**: Se il sistema è integro e rilevato sul traffico esistente al momento del failover regionale, l'RPO per gli eventi è di circa 5 minuti.

## <a name="recovery-time-objective-rto"></a>Obiettivo del tempo di ripristino (RTO)
- **Metadati RTO**: Anche se in genere avviene molto più rapidamente, entro 60 minuti, Griglia di eventi inizierà ad accettare chiamate di creazione/aggiornamento/eliminazione per argomenti e sottoscrizioni.
- **RTO dei dati**: Come i metadati, in genere accade molto più rapidamente, tuttavia entro 60 minuti, Griglia di eventi inizierà ad accettare nuovo traffico dopo un failover regionale.

> [!NOTE]
> Il costo per i metadati GeoDR su Griglia di eventi è: 0 USD.


## <a name="next-steps"></a>Passaggi successivi
Se si desidera implementare la propria logica di failover lato client, vedere : Creare il proprio ripristino di [emergenza per gli argomenti personalizzati in Griglia di eventi](custom-disaster-recovery.md)
