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
ms.openlocfilehash: b4b050befe160d69fbc44b7f0a2ebbbbad3d705a
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015940"
---
L'archiviazione con ridondanza locale garantisce almeno il 99,999999999% (11 nove) di durabilità degli oggetti nell'arco di un anno specifico. L'archiviazione con ridondanza locale assicura questa durabilità degli oggetti tramite la replica dei dati in un'unità di scala di archiviazione. L'unità di scala di archiviazione è ospitata in un data center nell'area in cui è stato creato l'account di archiviazione. Una richiesta di scrittura in un account di archiviazione con ridondanza locale viene restituita correttamente solo dopo che i dati sono stati scritti in tutte le repliche. Ogni replica risiede in domini di errore e domini di aggiornamento distinti all'interno di un'unità di scala di archiviazione.

Un'unità di scala di archiviazione è una raccolta di rack di nodi di archiviazione. Un dominio di errore è un gruppo di nodi che rappresentano un'unità fisica di errore. Un dominio di errore può essere descritto come un insieme di nodi appartenenti allo stesso rack fisico. Un dominio di aggiornamento è un gruppo di nodi che vengono aggiornati contemporaneamente durante il processo di aggiornamento del servizio (implementazione). Le repliche vengono distribuite tra domini di aggiornamento e domini di errore all'interno di un'unità di scala di archiviazione. Questa architettura assicura che i dati siano disponibili se un errore hardware interessa un singolo rack o quando i nodi vengono aggiornati durante un aggiornamento del servizio.

L'archiviazione con ridondanza locale è l'opzione di replica più economica e offre una durabilità inferiore rispetto alle altre opzioni. Se si verifica un'emergenza a livello di data center (ad esempio un incendio o un allagamento), tutte le repliche possono andare perse o non essere più recuperabili. Per attenuare questo rischio, Microsoft consiglia di usare l'archiviazione con ridondanza della zona (ZRS), l'archiviazione con ridondanza geografica (GRS) o l'archiviazione con ridondanza della zona geografica (GZRS).

* Se l'applicazione archivia dati che possono essere ricostruiti facilmente in caso di perdita, è possibile scegliere l'archiviazione con ridondanza locale.
* Per alcune applicazioni è necessario replicare i dati solo all'interno di un paese a causa di requisiti di governance dei dati. In alcuni casi, le aree abbinate in cui i dati vengono replicati per gli account GRS possono trovarsi in un altro paese/area geografica. Per altre informazioni sulle aree abbinate, vedere [Aree di Azure](https://azure.microsoft.com/regions/).
