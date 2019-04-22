---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/11/2019
ms.author: tamram
ms.openlocfilehash: b3e2f018a3f1ba2563ba8cf2df6dfd4959be592e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59737137"
---
| Risorsa | Destinazione        |
|----------|---------------|
| Dimensioni massime del singolo contenitore blob | Uguale a capacità dell'account di archiviazione massimo |
| Numero massimo di blocchi in un blocco di blob o blob di aggiunta | 50.000 blocchi |
| Dimensioni massime di un blocco in un blob in blocchi | 100 MiB |
| Dimensioni massime di un blob in blocchi | 50.000 x 100 MiB (circa 4,75 TiB) |
| Dimensioni massime di un blocco in un blob di Accodamento | 4 MiB |
| Dimensioni massime di un blob di Accodamento | 50.000 x 4 MiB (circa 195 GiB) |
| Dimensioni massime di un blob di pagine | 8 TiB |
| Numero massimo di criteri di accesso archiviati per il contenitore blob | 5 |
|Velocità effettiva da raggiungere per BLOB singolo |Fino a limiti di ingresso/uscita di account di archiviazione<sup>1</sup> |

<sup>1</sup> la velocità effettiva singolo oggetto dipende da diversi fattori, tra cui, ma non solo: concorrenza, le dimensioni di operazione, livello di prestazioni, velocità di origine per il caricamento e di destinazione per i download.