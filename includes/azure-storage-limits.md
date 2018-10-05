---
title: File di inclusione
description: File di inclusione
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: a0a777151216cb70b696da088b8a0d34779ebc39
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2018
ms.locfileid: "47396128"
---
La tabella seguente descrive i limiti predefiniti per Archiviazione di Azure. Il limite *in ingresso* indica tutti i dati (richieste) inviati a un account di archiviazione. Il limite *in uscita* indica tutti i dati (risposte) ricevuti da un account di archiviazione.

| Risorsa | Limite predefinito |
| --- | --- |
| Numero di account di archiviazione per ogni area per ogni sottoscrizione, inclusi gli account sia Standard che Premium | 200 |
| Capacità massima dell'account di archiviazione<sup>1</sup> | 500 TiB |
| Numero massimo di contenitori BLOB, BLOB, condivisioni file, tabelle, code, entità o messaggi per account di archiviazione | Nessun limite |
| Frequenza massima di richieste<sup>1</sup> per account di archiviazione | 20.000 richieste al secondo |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 20 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo<sup>1</sup> per account di archiviazione (aree degli Stati Uniti) | 20 Gbps con archiviazione RA-GRS/GRS abilitata, 30 Gbps per LRS/ZRS |
| Traffico in ingresso massimo<sup>1</sup> per account di archiviazione (aree non degli Stati Uniti) | 5 Gbps con archiviazione RA-GRS/GRS abilitata, 10 Gbps per LRS/ZRS<sup>2</sup> |
| Traffico in uscita massimo<sup>1</sup> per account di archiviazione (aree non degli Stati Uniti) | 10 Gbps con archiviazione RA-GRS/GRS abilitata, 15 Gbps per LRS/ZRS |

<sup>1</sup> Gli account di archiviazione di Azure supportano limiti più elevati per capacità, frequenza di richieste, traffico in ingresso e traffico in uscita in base alle richieste. Per altre informazioni sull'aumento dei limiti, vedere [Announcing larger, higher scale storage accounts](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/) (Annuncio di account di archiviazione di dimensioni maggiori a scalabilità più elevata). Per richiedere un incremento dei limiti di archiviazione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

<sup>2</sup>Le opzioni di [replica di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy) includono:
* **RA-GRS**: Archiviazione con ridondanza geografica e accesso in lettura. Se RA-GRS è abilitata, le destinazioni di uscita per la posizione secondaria sono identiche a quelle per la posizione primaria.
* **GRS**: archiviazione con ridondanza geografica. 
* **ZRS**: archiviazione con ridondanza della zona.
* **LRS**: archiviazione con ridondanza locale. 

