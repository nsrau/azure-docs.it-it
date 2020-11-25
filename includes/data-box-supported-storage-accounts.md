---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/git14/2020
ms.author: alkohli
ms.openlocfilehash: 25c2ea04cd062554a975c63aae9b97846e646d68
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025778"
---
Di seguito è riportato un elenco degli account di archiviazione e dei tipi di archiviazione supportati per il dispositivo Data Box. Per l'intero elenco dei diversi tipi di account di archiviazione e delle funzionalità complete, vedere [Tipi di account di archiviazione](../articles/storage/common/storage-account-overview.md#types-of-storage-accounts).

Per gli ordini di importazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine** _ |_ *File di Azure** |**Note**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S |
| Utilizzo generico v1 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Sono supportati BLOB sia ad accesso frequente che sporadico. |

\* *- I dati caricati nei BLOB di pagine devono essere pari a 512 byte allineati, ad esempio VHD.*

Per gli ordini di esportazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine** _ |_ *File di Azure** |**Livelli di accesso supportati**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S | |
| Utilizzo generico v1 Standard  | S | S | S | Frequente, ad accesso sporadico|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Frequente, ad accesso sporadico|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB in blocchi Premium |S | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB di pagine Premium | |S | | |

> [!IMPORTANT]
> - Per gli account per utilizzo generico, Data Box non supporta i tipi di archiviazione di Accodamento, tabelle e dischi per gli ordini di importazione. Per gli ordini di esportazione, Data Box non supporta i tipi di archiviazione Queue, Table, disk e Azure Data Lake gen 2 per gli account per utilizzo generico.
> - Data Box non supporta i BLOB di Accodamento per l'archiviazione BLOB e gli account di archiviazione BLOB in blocchi.
> - Data Box non supporta gli account di archiviazione di file Premium.
> - I dati caricati nei BLOB di pagine devono essere allineati a 512 byte, ad esempio VHD.
> - È possibile esportare un massimo di 80 TB.
> - La cronologia file e gli snapshot BLOB non vengono esportati.