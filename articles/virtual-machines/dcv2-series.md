---
title: Serie DC - Macchine virtuali di AzureDC-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali serie DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: d35e37e53b84d317446a93a2301fc3b703b426b7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085723"
---
# <a name="dcsv2-series"></a>Serie DCsv2


La serie DCsv2 consente di proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Queste macchine sono supportate dall'ultima generazione di processore Intel XEON E-2288G con tecnologia SGX. Con la tecnologia Intel Turbo Boost queste macchine possono raggiungere i 5,0 GHz. Le istanze della serie DCsv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

Esempi di casi d'uso includono: condivisione di dati confidenziali multiparte, rilevamento di frodi, antiriciclaggio, blockchain, analisi di utilizzo confidenziale, analisi di intelligence e apprendimento automatico riservato.

Archiviazione Premium: Supportato

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

- Eccetto per Standard_DC8_v2



| Dimensione             | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | NiDi massima / Larghezza di banda di rete prevista (MBps) |
|------------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC1s_v2 | 1    | 4           | 50                     | 1              | 2000/16 (21)                                                            | 1600/24                                   | 2                                            |
| Standard_DC2s_v2 | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                            | 3200/48                                   | 2                                            |
| Standard_DC4s_v2 | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                            | 6400/96                                   | 2                                            |
| Standard_DC8_v2  | 8   | 32          | 400                    | 8              | 16000/128 (172)                                                         | 12800/192                                 | 2                                            |

- Le macchine virtuali serie DCsv2 sono macchine `Gen2` virtuali di generazione [2](./linux/generation-2.md#creating-a-generation-2-vm) e supportano solo immagini.
- Attualmente disponibile solo nel Regno Unito sud, Canada centrale e Stati Uniti orientali.
- Generazione precedente di macchine virtuali di calcolo riservato: [serie DC](sizes-previous-gen.md#preview-dc-series)
- Creare macchine virtuali DCsv2 usando il portale di Azure o [Azure MarketplaceCreate](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) DCsv2 VMs using the [Azure portal](./linux/quick-create-portal.md) or Azure Marketplace



## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
