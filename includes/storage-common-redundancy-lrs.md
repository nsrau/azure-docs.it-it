---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219036"
---
Archiviazione con ridondanza locale (con ridondanza locale) replica i dati tre volte all'interno di una singola data center. CON ridondanza locale offre almeno il 99,999999999% (11 nove) di durabilità degli oggetti nel corso di un determinato anno. L'archiviazione con ridondanza locale è l'opzione di replica più economica e offre una durabilità inferiore rispetto alle altre opzioni.

Se si verifica una situazione di emergenza a livello di Data Center (ad esempio, incendi o flood), tutte le repliche in un account di archiviazione con con ridondanza locale potrebbero essere perse o irrecuperabili. Per attenuare questo rischio, Microsoft consiglia di usare l'archiviazione con ridondanza della zona (ZRS), l'archiviazione con ridondanza geografica (GRS) o l'archiviazione con ridondanza della zona geografica (GZRS).

Una richiesta di scrittura a un account di archiviazione con ridondanza locale restituisce correttamente solo dopo che i dati sono stati scritti in tutte e tre le repliche.

È possibile utilizzare con ridondanza locale negli scenari seguenti:

* Se l'applicazione archivia dati che possono essere ricostruiti facilmente in caso di perdita, è possibile scegliere l'archiviazione con ridondanza locale.
* Per alcune applicazioni è necessario replicare i dati solo all'interno di un paese a causa di requisiti di governance dei dati. In alcuni casi, le aree abbinate in cui i dati vengono replicati per gli account GRS possono trovarsi in un altro paese/area geografica. Per altre informazioni sulle aree abbinate, vedere [Aree di Azure](https://azure.microsoft.com/regions/).
