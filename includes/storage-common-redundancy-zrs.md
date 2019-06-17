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
ms.openlocfilehash: d25efa8c666fa91c7bd652a7cf931ea6ca379aa2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133227"
---
L'archiviazione con ridondanza della zona (ZRS) replica i dati in modo sincrono in tre cluster di archiviazione in una singola area. Ogni cluster di archiviazione è separato fisicamente dagli altri e si trova nella propria zona di disponibilità. Ogni zona di disponibilità&mdash;con il cluster di archiviazione con ridondanza della zona al suo interno&mdash;è autonoma e include funzionalità di rete e utilità separate. Una richiesta di scrittura in un account di archiviazione ZRS viene restituita correttamente solo dopo che i dati vengono scritti in tutte le repliche tra i tre cluster.

Quando si archiviano i dati in un account di archiviazione con replica di archiviazione con ridondanza della zona, è possibile continuare ad accedere ai dati e gestirli se una zona di disponibilità non è più disponibile. L'archiviazione con ridondanza della zona offre prestazioni eccellenti e bassa latenza. L'archiviazione con ridondanza della zona offre gli stessi [obiettivi di scalabilità](../articles/storage/common/storage-scalability-targets.md) dell'[archiviazione con ridondanza locale](../articles/storage/common/storage-redundancy-lrs.md).

Prendere in considerazione l'archiviazione con ridondanza della zona per scenari che richiedono coerenza, durabilità e disponibilità elevata. Anche se un'interruzione o un disastro naturale rende non disponibile una zona di disponibilità, l'archiviazione con ridondanza della zona offre durabilità per gli oggetti di archiviazione pari ad almeno il 99,9999999999% (12 9) per un determinato anno.

Per altre informazioni sulle zone di disponibilità, vedere [Informazioni sulle zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).