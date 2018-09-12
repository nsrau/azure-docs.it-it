---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 4a43966180850645584043690b1be9d6ae232f6e
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44027448"
---
L'archiviazione con ridondanza della zona (ZRS) replica i dati in modo sincrono in tre cluster di archiviazione in una singola area. Ogni cluster di archiviazione è separato fisicamente dagli altri e si trova nella propria zona di disponibilità (AZ). Ogni zona di disponibilità, con il cluster di archiviazione con ridondanza della zona al suo interno, è autonoma, con funzionalità di rete e utilità separate.

L'archiviazione dei dati in un account di archiviazione con ridondanza della zona assicura la possibilità di accedere ai dati e gestirli nel caso in cui una zona smetta di essere disponibile. L'archiviazione con ridondanza della zona offre prestazioni eccellenti e bassa latenza. L'archiviazione con ridondanza della zona offre gli stessi [obiettivi di scalabilità](../articles/storage/common/storage-scalability-targets.md) dell'[archiviazione con ridondanza locale (LRS)](../articles/storage/common/storage-redundancy-lrs.md).

Prendere in considerazione l'archiviazione con ridondanza della zona per scenari che richiedono coerenza assoluta, livelli elevati di durabilità e disponibilità elevata, anche nel caso in cui un'interruzione o una calamità naturale renda non disponibile un data center di una zona. L'archiviazione con ridondanza della zona offre almeno il 99,9999999999% (12 9) di durabilità degli oggetti di archiviazione nell'arco di un anno.

Per altre informazioni sulle zone di disponibilità, vedere [Informazioni sulle zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview).