---
title: Ripristino di emergenza geografico in griglia di eventi di Azure | Microsoft Docs
description: Viene descritto come griglia di eventi di Azure supporta il ripristino di emergenza geografico (GeoDR) automaticamente.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307318"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Ripristino di emergenza geografico sul lato server in griglia di eventi di Azure
Griglia di eventi include ora un ripristino di emergenza geografico automatica (GeoDR) dei metadati non solo per nuovo, ma tutti i domini esistenti, argomenti e sottoscrizioni di eventi. Se un'intera area di Azure diventa inattiva, griglia di eventi avrà già tutti i metadati relativi agli eventi dell'infrastruttura sincronizzato con un'area abbinata. I nuovi eventi inizierà a flusso nuovo con alcun intervento da parte dell'utente. 

Ripristino di emergenza viene misurato con due metriche:

- [Obiettivo del punto di ripristino (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): i minuti o ore di dati che potrebbero andare persi.
- [Obiettivo tempo di ripristino (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective): i minuti di ore di servizio potrebbe essere verso il basso.

Il failover automatico della griglia di eventi ha diversi RPO e RTO per i metadati (sottoscrizioni di eventi e così via) e i dati (eventi). Se è necessario specifica diversa da quelle seguenti, è anche possibile implementare il proprio [sul lato client, eseguire il failover tramite l'argomento API integrità](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Obiettivo del punto di ripristino (RPO)
- **RPO Metadata**: zero minuti. Ogni volta che viene creata una risorsa in griglia di eventi, è replicarlo immediatamente in aree geografiche. Quando si verifica un failover, i metadati non vengono persi.
- **I dati RPO**: se il sistema è integro e ha gestito tutto il traffico esistente fino al momento del failover a livello di area, l'obiettivo del punto di ripristino per gli eventi è di circa 5 minuti.

## <a name="recovery-time-objective-rto"></a>Obiettivo del tempo di ripristino (RTO)
- **Metadata RTO**: Anche se lo si verifica in genere molto più rapidamente, entro 60 minuti, griglia di eventi inizierà accettare le chiamate di creazione/aggiornamento/eliminazione per gli argomenti e sottoscrizioni.
- **Dati RTO**: Come i metadati, lo si verifica in genere molto più rapidamente, tuttavia entro 60 minuti, griglia di eventi inizierà accettando il traffico di nuovo dopo un failover a livello di area.

> [!NOTE]
> Il costo per i metadati GeoDR in griglia di eventi è: $0.


## <a name="next-steps"></a>Passaggi successivi
Se si desidera implementare è proprio failover lato client per la logica, vedere [& proprio ripristino di emergenza per gli argomenti personalizzati in griglia di eventi di compilazione](custom-disaster-recovery.md)
