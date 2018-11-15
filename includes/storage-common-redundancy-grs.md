---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: jeking
ms.custom: include file
ms.openlocfilehash: efa593d0ff0043d81574b67192deed30933e1e40
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219852"
---
L'archiviazione con ridondanza geografica (GRS) è progettata per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti nell'arco di un anno eseguendo la replica dei dati in un'area secondaria distante centinaia di chilometri dall'area primaria. Se per l'account di archiviazione è stata abilitata l'archiviazione con ridondanza geografica, la durabilità dei dati è assicurata anche in caso di un'interruzione completa dell'alimentazione locale o in situazioni di emergenza in cui l'area primaria non è recuperabile.

Se si opta per l'archiviazione con ridondanza geografica, è possibile scegliere tra due opzioni correlate:

* L'archiviazione con ridondanza geografica replica i dati in un altro data center in un'area secondaria, ma i dati sono disponibili per la lettura solo se Microsoft avvia un failover dall'area primaria a quella secondaria. 
* L'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) è basata sull'archiviazione con ridondanza geografica. L'archiviazione con ridondanza geografica e accesso in lettura replica i dati in un altro data center in un'area secondaria e offre anche la possibilità di lettura dall'area secondaria. Con l'archiviazione con ridondanza geografica e accesso in lettura, è possibile leggere dall'area secondaria indipendentemente dal fatto che Microsoft avvii un failover dall'area primaria a quella secondaria. 

Per un account di archiviazione con ridondanza geografica o di archiviazione con ridondanza geografica e accesso in lettura abilitata, tutti i dati vengono prima replicati con l'archiviazione con ridondanza locale. Prima di tutto, viene eseguito il commit di un aggiornamento nella località primaria e viene eseguita la replica con l'archiviazione con ridondanza locale. L'aggiornamento viene quindi replicato in modo asincrono nell'area secondaria tramite l'archiviazione con ridondanza geografica. Quando i dati vengono scritti nella località secondaria, vengono anche replicati all'interno di tale località usando l'archiviazione con ridondanza locale. 

Entrambe le aree primaria e secondaria gestiscono le repliche tra domini di errore e domini di aggiornamento separati all'interno di un'unità di scala di archiviazione. L'unità di scala di archiviazione è l'unità di replica di base nel data center. La replica a questo livello viene fornita dall'archiviazione con ridondanza locale. Per altre informazioni, vedere [Archiviazione con ridondanza locale: ridondanza dei dati a basso costo per l'Archiviazione di Azure](../articles/storage/common/storage-redundancy-lrs.md).

Nella scelta dell'opzione di replica da usare, tenere presenti queste considerazioni:

* L'archiviazione con ridondanza della zona (ZRS) fornisce disponibilità elevata con replica sincrona e per alcuni scenari può essere una scelta migliore rispetto all'archiviazione con ridondanza geografica o all'archiviazione con ridondanza geografica e accesso in lettura. Per altre informazioni sull'archiviazione con ridondanza della zona, vedere [Archiviazione con ridondanza della zona](../articles/storage/common/storage-redundancy-zrs.md).
* La replica asincrona implica un ritardo dal momento in cui i dati vengono scritti nell'area primaria a quello in cui vengono replicati nell'area secondaria. Nel caso in cui si verifichi un'emergenza a livello di area, è possibile che le modifiche non ancora replicate nell'area secondaria vadano perse se non è possibile recuperare i dati dall'area primaria.
* Con l'archiviazione con ridondanza geografica, la replica non è disponibile per la lettura o la scrittura a meno che Microsoft non avvii un failover nell'area secondaria. In caso di failover, si avrà accesso in lettura e scrittura a tali dati al termine del failover. Per altre informazioni, vedere le [indicazioni sul ripristino di emergenza](../articles/storage/common/storage-disaster-recovery-guidance.md).
* Per consentire a un'applicazione di leggere nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura.