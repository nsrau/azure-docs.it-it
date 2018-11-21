---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/23/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 84333b26ac70db4b400f7236d4255f4b57a6ca7d
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572320"
---
La tabella seguente descrive i limiti predefiniti per Archiviazione di Azure. Il limite *in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. Il limite *in uscita* indica tutti i dati (risposte) ricevuti da un account di archiviazione.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account sia Standard che Premium | 250 |
| Capacità massima dell'account di archiviazione | 2 PB per Stati Uniti ed Europa, 500 TB per tutte le altre aree incluso il Regno Unito |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 20 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree non degli Stati Uniti) | 5 Gbps con archiviazione RA-GRS/GRS abilitata, 10 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v2 e BLOB (tutte le aree) | 50 Gbps |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS abilitata, 30 Gbps per LRS/ZRS <sup>2</sup> |
| Traffico in uscita massimo per gli account di archiviazione per utilizzo generico v1 (aree non degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 15 Gbps per LRS/ZRS <sup>2</sup> |

<sup>1</sup> Gli account di archiviazione di Azure supportano limiti più elevati per il traffico in ingresso in base alle richieste. Per richiedere un incremento dei limiti di archiviazione per il traffico in ingresso, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>Le opzioni di [replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona.
* **LRS**: archiviazione con ridondanza locale. 

Se le esigenze dell'applicazione superano gli obiettivi di scalabilità di un singolo account di archiviazione, è possibile compilare l'applicazione in modo che sia possibile usare più account di archiviazione. Quindi partizionare gli oggetti dati tra tali account di archiviazione. Per informazioni sui prezzi in base al volume, vedere la pagina relativa ai [prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/) .

Tutti gli account di archiviazione vengono eseguiti su una topologia di rete flat e supportano gli obiettivi di scalabilità e prestazioni descritti in questo articolo, indipendentemente dal momento in cui sono stati creati. Per ulteriori informazioni sull'architettura di rete flat di Archiviazione di Azure e sulla scalabilità, vedere [Archiviazione di Microsoft Azure: servizio di archiviazione cloud a elevata disponibilità con coerenza assoluta](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).

