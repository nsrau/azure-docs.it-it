---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 91f91b1260cc445f90c2608fc5259ad61acd37ac
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90533275"
---
Di seguito è riportato un elenco degli account di archiviazione e dei tipi di archiviazione supportati per il dispositivo Data Box. Per l'intero elenco dei diversi tipi di account di archiviazione e delle funzionalità complete, vedere [Tipi di account di archiviazione](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

Per gli ordini di importazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine*** |**File di Azure** |**Note**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S |
| Utilizzo generico v1 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Sono supportati BLOB sia ad accesso frequente che sporadico. |

\* *- I dati caricati nei BLOB di pagine devono essere pari a 512 byte allineati, ad esempio VHD.*

Per gli ordini di esportazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine*** |**File di Azure** |**Livelli di accesso supportati**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S | |
| Utilizzo generico v1 Standard  | S | S | S | Frequente, ad accesso sporadico|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Frequente, ad accesso sporadico|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB in blocchi Premium |Y | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB di pagine Premium | |Y | | |

> [!IMPORTANT]
> - Per gli account per utilizzo generico, Data Box non supporta i tipi di archiviazione di Accodamento, tabelle e dischi per gli ordini di importazione. Per gli ordini di esportazione, Data Box non supporta i tipi di archiviazione Queue, Table, disk e Azure Data Lake gen 2 per gli account per utilizzo generico.
> - Data Box non supporta i BLOB di Accodamento per l'archiviazione BLOB e gli account di archiviazione BLOB in blocchi.
> - Data Box non supporta gli account di archiviazione di file Premium.
> - I dati caricati nei BLOB di pagine devono essere allineati a 512 byte, ad esempio VHD.
> - È possibile esportare un massimo di 80 TB.
> - La cronologia file e gli snapshot BLOB non vengono esportati.


