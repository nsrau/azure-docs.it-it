---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 4/20/2019
ms.author: tamram
ms.openlocfilehash: aab17966862c57a52f252b3c4e9b757673078b0a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67180359"
---
| Risorsa | Destinazione        |
|----------|---------------|
| Dimensioni massime di un singolo contenitore BLOB | Uguale alla capacità massima dell'account di archiviazione |
| Numero massimo di blocchi in un BLOB in blocchi o in un BLOB di Accodamento | 50.000 blocchi |
| Dimensione massima di un blocco in un BLOB in blocchi | 100 MiB |
| Dimensioni massime di un BLOB in blocchi | 50.000 X 100 MiB (approssimativamente 4,75 TiB) |
| Dimensione massima di un blocco in un BLOB di Accodamento | 4 MiB |
| Dimensioni massime di un BLOB di Accodamento | 50.000 x 4 MiB (circa 195 GiB) |
| Dimensioni massime di un BLOB di pagine | 8 TiB |
| Numero massimo di criteri di accesso archiviati per ogni contenitore BLOB | 5 |
|Velocità effettiva da raggiungere per BLOB singolo |Fino a limiti di ingresso/uscita dell'account di archiviazione<sup>1</sup> |

<sup>1</sup> la velocità effettiva di un singolo oggetto dipende da diversi fattori, tra cui: concorrenza, dimensioni della richiesta, livello di prestazioni, velocità dell'origine per i caricamenti e destinazione per i download. Per sfruttare i vantaggi offerti dai miglioramenti delle prestazioni dei [BLOB in blocchi con velocità effettiva elevata](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/) , usare una dimensione Put Blob o Put Block request di > 4 MiB (> 256 KiB per l'archiviazione BLOB in blocchi a prestazioni Premium o per data Lake storage Gen2).
