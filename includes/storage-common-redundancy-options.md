---
title: includere il file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77157220"
---
Le opzioni di ridondanza per un account di archiviazione includono:

* Archiviazione con ridondanza locale (con ridondanza locale): strategia di ridondanza semplice e A basso costo. I dati vengono copiati in modo sincrono tre volte all'interno dell'area primaria.
* Archiviazione con ridondanza della zona (ZRS): ridondanza per gli scenari che richiedono una disponibilità elevata. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria.
* Archiviazione con ridondanza geografica: ridondanza tra più aree per la protezione da interruzioni a livello di area. I dati vengono copiati in modo sincrono tre volte nell'area primaria, quindi vengono copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
* Archiviazione con ridondanza della zona geografica (GZRS) (anteprima): ridondanza per gli scenari che richiedono disponibilità elevata e durabilità massima. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria, quindi vengono copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

Per altre informazioni sulle opzioni di ridondanza in archiviazione di Azure, vedere [ridondanza di archiviazione di Azure](../articles/storage/common/storage-redundancy.md).
