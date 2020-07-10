---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 06/08/2020
ms.author: alkohli
ms.openlocfilehash: da36e2bbf358a1c61d2b9b3f7ede592ac88fd427
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200285"
---
Di seguito è riportato un elenco degli account di archiviazione e dei tipi di archiviazione supportati per il dispositivo Data Box. Per l'intero elenco dei diversi tipi di account di archiviazione e delle funzionalità complete, vedere [Tipi di account di archiviazione](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

Per gli ordini di importazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine*** |**File di Azure** |**Note**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | Y |
| Utilizzo generico v1 Standard  | S | S | Y | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v1 Premium  |  | Y| | |
| Utilizzo generico v2 Standard  | S | S | Y | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Sono supportati BLOB sia ad accesso frequente che sporadico. |

\* *- I dati caricati nei BLOB di pagine devono essere pari a 512 byte allineati, ad esempio VHD.*

Per gli ordini di esportazione, la tabella seguente Mostra gli account di archiviazione supportati.

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine*** |**File di Azure** |**Livelli di accesso supportati**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | Y | Y | Y | |
| Utilizzo generico v1 Standard  | Y | Y | Y | Frequente, ad accesso sporadico|
| Utilizzo generico v1 Premium  |  | Y| | |
| Utilizzo generico v2 Standard  | Y | Y | Y | Frequente, ad accesso sporadico|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB in blocchi Premium |Y | | |Frequente, ad accesso sporadico |
| Archiviazione BLOB di pagine Premium | |Y | | |

> [!IMPORTANT]
> - Per gli account per utilizzo generico, Data Box non supporta i tipi di archiviazione Queue, Table, disk e Azure Data Lake gen 2.
> - Data Box non supporta i BLOB di Accodamento per l'archiviazione BLOB e gli account di archiviazione BLOB in blocchi.
> - Data Box non supporta gli account di archiviazione di file Premium.
> - I dati caricati nei BLOB di pagine devono essere allineati a 512 byte, ad esempio VHD.
> - È possibile esportare un massimo di 80 TB.
> - La cronologia file e gli snapshot BLOB non vengono esportati.


