---
title: includere file
description: includere file
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157220"
---
Le opzioni di ridondanza per un account di archiviazione includono:

* Archiviazione con ridondanza locale (LRS): strategia di ridondanza semplice e a costi contenuti. I dati vengono copiati in modo sincrono per tre volte all'interno dell'area primaria.
* Archiviazione con ridondanza della zona (ZRS): ridondanza per gli scenari in cui è richiesta la disponibilità elevata. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria.
* Archiviazione con ridondanza geografica: ridondanza tra più aree per evitare interruzioni a livello di area. I dati vengono copiati in modo sincrono per tre volte all'interno dell'area primaria e quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).
* Archiviazione con ridondanza geografica della zona (anteprima): ridondanza per gli scenari in cui sono richieste sia la disponibilità elevata cha la durabilità massima. I dati vengono copiati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria e quindi copiati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica della zona e accesso in lettura (RA-GZRS).

Per altre informazioni sulle opzioni di ridondanza in Archiviazione di Azure, vedere [Ridondanza di Archiviazione di Azure](../articles/storage/common/storage-redundancy.md).
