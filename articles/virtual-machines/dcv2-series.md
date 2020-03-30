---
title: Serie DC - Macchine virtuali di AzureDC-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali serie DC.
services: virtual-machines
author: susaxen
ms.service: virtual-machines
ms.topic: article
ms.date: 02/20/2020
ms.author: lahugh
ms.openlocfilehash: 7834c8a32d4d85fc354bac209e13f19f3b8315fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80256930"
---
# <a name="preview-dcsv2-series"></a>Anteprima: Serie DCsv2


La serie DCsv2 consente di proteggere la riservatezza e l'integrità dei dati e del codice durante l'elaborazione nel cloud pubblico. Queste macchine sono supportate dall'ultima generazione di processore Intel XEON E-2288G con tecnologia SGX. Con la tecnologia Intel Turbo Boost queste macchine possono raggiungere i 5,0 GHz. Le istanze della serie DCsv2 consentono ai clienti di creare applicazioni sicure basate su enclave per proteggere il codice e i dati mentre sono in uso.

Esempi di casi d'uso includono la condivisione di dati confidenziali di più parti, rilevamento di frodi, antiriciclaggio, blockchain, analisi di utilizzo confidenziale, analisi di intelligence e apprendimento automatico riservato.

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
- Attualmente disponibile solo nel Regno Unito sud e Canada Central.
- Generazione precedente di macchine virtuali di calcolo riservate: [serie DCPrevious](sizes-previous-gen.md) generation of Confidential Compute VMs: DC Series
- Creare macchine virtuali DCsv2 usando La creazione di macchine virtuali del portale di [AzureCreate](./linux/quick-create-portal.md) DCsv2 VMs using Azure Portal Create VM - Portal



## <a name="other-sizes"></a>Altre dimensioni

- [Finalità generale](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
