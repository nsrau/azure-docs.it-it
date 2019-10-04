---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b7fddce8f682bc341b361a47f8e083cc281e90aa
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309578"
---
L'archiviazione con ridondanza della zona (ZRS) replica i dati in modo sincrono in tre cluster di archiviazione in una singola area. Ogni cluster di archiviazione è separato fisicamente dagli altri e si trova nella propria zona di disponibilità. Ogni zona di disponibilità&mdash;con il cluster di archiviazione con ridondanza della zona al suo interno&mdash;è autonoma e include funzionalità di rete e utilità separate. Una richiesta di scrittura a un account di archiviazione ZRS restituisce correttamente solo dopo che i dati sono stati scritti in tutte le repliche nei tre cluster.

Quando si archiviano i dati in un account di archiviazione con replica di archiviazione con ridondanza della zona, è possibile continuare ad accedere ai dati e gestirli se una zona di disponibilità non è più disponibile. L'archiviazione con ridondanza della zona offre prestazioni eccellenti e bassa latenza. L'archiviazione con ridondanza della zona offre gli stessi [obiettivi di scalabilità](../articles/storage/common/storage-scalability-targets.md) dell'[archiviazione con ridondanza locale](../articles/storage/common/storage-redundancy-lrs.md).

Prendere in considerazione l'archiviazione con ridondanza della zona per scenari che richiedono coerenza, durabilità e disponibilità elevata. Anche se un'interruzione o un disastro naturale rende non disponibile una zona di disponibilità, l'archiviazione con ridondanza della zona offre durabilità per gli oggetti di archiviazione pari ad almeno il 99,9999999999% (12 9) per un determinato anno.

Archiviazione con ridondanza geografica (GZRS) (anteprima) consente di replicare i dati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria, quindi replica i dati in modo asincrono nell'area secondaria. GZRS offre disponibilità elevata insieme alla durabilità massima. GZRS è progettato per offrire almeno il 99,99999999999999% (16 9) di durabilità degli oggetti in un determinato anno. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS). Per altre informazioni su GZRS, vedere [archiviazione con ridondanza della zona geografica per disponibilità elevata e durabilità massima (anteprima)](../articles/storage/common/storage-redundancy-gzrs.md).

Per altre informazioni sulle zone di disponibilità, vedere [Informazioni sulle zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).
