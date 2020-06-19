---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: f230fc247c6ad94bfdfb3cdbc0f897d66313b039
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83696399"
---
Di seguito è riportato un elenco degli account di archiviazione e dei tipi di archiviazione supportati per il dispositivo Data Box. Per l'intero elenco dei diversi tipi di account di archiviazione e delle funzionalità complete, vedere [Tipi di account di archiviazione](/azure/storage/common/storage-account-overview#types-of-storage-accounts).

| **Account di archiviazione/Tipi di account di archiviazione supportati** | **BLOB in blocchi** |**BLOB di pagine*** |**File di Azure** |**Note**|
| --- | --- | -- | -- | -- |
| Versione classica Standard | S | S | S |
| Utilizzo generico v1 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v1 Premium  |  | S| | |
| Utilizzo generico v2 Standard  | S | S | S | Sono supportati BLOB sia ad accesso frequente che sporadico.|
| Utilizzo generico v2 Premium  |  |S | | |
| Archiviazione BLOB Standard |S | | |Sono supportati BLOB sia ad accesso frequente che sporadico. |

\* *- I dati caricati nei BLOB di pagine devono essere pari a 512 byte allineati, ad esempio VHD.*
