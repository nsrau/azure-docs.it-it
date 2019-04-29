---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/04/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 037996385f34c5037e0386686e3bdf8dc1b7a37a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731397"
---
L'archiviazione con ridondanza della zona (ZRS) replica i dati in modo sincrono in tre cluster di archiviazione in una singola area. Ogni cluster di archiviazione è separato fisicamente dagli altri e si trova nella propria zona di disponibilità. Ogni zona di disponibilità&mdash;con il cluster di archiviazione con ridondanza della zona al suo interno&mdash;è autonoma e include funzionalità di rete e utilità separate.

Quando si archiviano i dati in un account di archiviazione con replica di archiviazione con ridondanza della zona, è possibile continuare ad accedere ai dati e gestirli se una zona di disponibilità non è più disponibile. L'archiviazione con ridondanza della zona offre prestazioni eccellenti e bassa latenza. L'archiviazione con ridondanza della zona offre gli stessi [obiettivi di scalabilità](../articles/storage/common/storage-scalability-targets.md) dell'[archiviazione con ridondanza locale](../articles/storage/common/storage-redundancy-lrs.md).

Prendere in considerazione l'archiviazione con ridondanza della zona per scenari che richiedono coerenza, durabilità e disponibilità elevata. Anche se un'interruzione o un disastro naturale rende non disponibile una zona di disponibilità, l'archiviazione con ridondanza della zona offre durabilità per gli oggetti di archiviazione pari ad almeno il 99,9999999999% (12 9) per un determinato anno.

Per altre informazioni sulle zone di disponibilità, vedere [Informazioni sulle zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).