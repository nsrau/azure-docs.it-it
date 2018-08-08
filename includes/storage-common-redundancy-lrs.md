---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 58d81bd4c1ce4b3af91a335039f62df08b340576
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399992"
---
L'archiviazione con ridondanza locale è progettata per garantire almeno il 99,999999999% (11 9) di durabilità degli oggetti nell'arco di un anno specifico tramite la replica dei dati all'interno di un'unità di scala di archiviazione. Un'unità di scala di archiviazione è ospitata in un data center nell'area in cui è stato creato l'account di archiviazione. Una richiesta di scrittura in un account di archiviazione con ridondanza locale viene restituita correttamente solo dopo che i dati sono stati scritti in tutte le repliche. Queste repliche risiedono ognuna in domini di errore e domini di aggiornamento distinti all'interno di una singola unità di scala di archiviazione.

Un'unità di scala di archiviazione è una raccolta di rack di nodi di archiviazione. Un dominio di errore è un gruppo di nodi che rappresentano un'unità di errore fisica e possono essere considerati come nodi appartenenti allo stesso rack fisico. Un dominio di aggiornamento è un gruppo di nodi che vengono aggiornati contemporaneamente durante il processo di aggiornamento del servizio (implementazione). Le repliche vengono distribuite tra domini di aggiornamento e domini di errore all'interno di un'unità di scala di archiviazione. Questa architettura assicura che i dati siano disponibili se un errore hardware interessa un singolo rack o quando i nodi vengono aggiornati durante un'implementazione.

L'archiviazione con ridondanza locale è l'opzione di replica più economica e offre una durabilità inferiore rispetto alle altre opzioni. Se si verifica un'emergenza a livello di data center (ad esempio un incendio o un allagamento), tutte le repliche possono andare perse o non essere più recuperabili. Per attenuare questo rischio, è consigliabile usare l'archiviazione con ridondanza della zona (ZRS, Zone-Redundant Storage) o l'archiviazione con ridondanza geografica (GRS, Geo-Redundant Storage).

* Se l'applicazione archivia dati che possono essere ricostruiti facilmente in caso di perdita, è possibile scegliere l'archiviazione con ridondanza locale.
* Per alcune applicazioni è necessario replicare i dati solo all'interno di un paese a causa di requisiti di governance dei dati. In alcuni casi, le aree abbinate tra le quali i dati vengono replicati per gli account di archiviazione con ridondanza geografica possono trovarsi in un altro paese. Per altre informazioni sulle aree abbinate, vedere [Aree di Azure](https://azure.microsoft.com/regions/).
