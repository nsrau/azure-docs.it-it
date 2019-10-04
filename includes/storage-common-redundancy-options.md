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
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029805"
---
Le opzioni di replica per un account di archiviazione includono:

* [Archiviazione con ridondanza locale (con ridondanza locale)](../articles/storage/common/storage-redundancy-lrs.md): Una strategia di replica semplice e a basso costo. I dati vengono replicati in modo sincrono tre volte all'interno dell'area primaria.
* [Archiviazione con ridondanza della zona (ZRS)](../articles/storage/common/storage-redundancy-zrs.md). Replica per gli scenari che richiedono una disponibilità elevata. I dati vengono replicati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria.
* [Archiviazione con ridondanza geografica](../articles/storage/common/storage-redundancy-grs.md): Replica tra più aree per la protezione da interruzioni a livello di area. I dati vengono replicati in modo sincrono tre volte nell'area primaria, quindi replicati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
* [Archiviazione con ridondanza della zona geografica (GZRS) (anteprima)](../articles/storage/common/storage-redundancy-gzrs.md): Replica per scenari che richiedono disponibilità elevata e durabilità massima. I dati vengono replicati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria, quindi replicati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).
