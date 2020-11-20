---
title: Ripristino di emergenza geografico in griglia di eventi di Azure | Microsoft Docs
description: Descrive in che modo griglia di eventi di Azure supporta il ripristino di emergenza geografico (GeoDR) automaticamente.
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 10beaf0ae25f3ed9b7bcda5961a89494b18b84d9
ms.sourcegitcommit: f311f112c9ca711d88a096bed43040fcdad24433
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94980850"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Ripristino di emergenza geografico lato server in griglia di eventi di Azure
Griglia di eventi dispone ora di un ripristino di emergenza geografico (GeoDR) automatico dei metadati non solo per nuovi, ma per tutti i domini, gli argomenti e le sottoscrizioni di eventi esistenti. Se un'intera area di Azure diventa inattiva, griglia di eventi avrà già tutti i metadati dell'infrastruttura correlati agli eventi sincronizzati in un'area abbinata. I nuovi eventi inizieranno a fluire nuovamente senza alcun intervento da parte dell'utente. 

Il ripristino di emergenza viene misurato con due metriche:

- Obiettivo punto di ripristino (RPO): minuti o ore di dati che potrebbero andare persi.
- Obiettivo del tempo di ripristino (RTO): minuti o ore in cui il servizio potrebbe essere inattivo.

Il failover automatico della griglia di eventi presenta RPOs e RTO diversi per i metadati (sottoscrizioni di eventi e così via) e dati (eventi). Se sono necessarie specifiche diverse da quelle riportate di seguito, è comunque possibile implementare il [failover del lato client usando le API di integrità dell'argomento](custom-disaster-recovery.md).

## <a name="recovery-point-objective-rpo"></a>Obiettivo del punto di ripristino (RPO)
- **RPO metadati**: zero minuti. Ogni volta che una risorsa viene creata in griglia di eventi, viene immediatamente replicata tra le aree. Quando si verifica un failover, i metadati non vengono persi.
- **RPO di dati**: se il sistema è integro e viene aggiornato sul traffico esistente al momento del failover a livello di area, il RPO per gli eventi è di circa 5 minuti.

## <a name="recovery-time-objective-rto"></a>Obiettivo del tempo di ripristino (RTO)
- **RTO dei metadati**: sebbene in genere si verifichi molto più rapidamente, entro 60 minuti la griglia di eventi inizierà ad accettare le chiamate di creazione/aggiornamento/eliminazione per argomenti e sottoscrizioni.
- **Data RTO**: come i metadati, in genere si verifica molto più rapidamente, ma entro 60 minuti la griglia di eventi inizierà ad accettare nuovo traffico dopo un failover a livello di area.

> [!NOTE]
> Il costo per i metadati GeoDR in griglia di eventi è: $0.


## <a name="next-steps"></a>Passaggi successivi
Se si vuole implementare la logica di failover lato client, vedere [# compilare il ripristino di emergenza per gli argomenti personalizzati in griglia di eventi](custom-disaster-recovery.md)
