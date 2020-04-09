---
title: Serie Mv2 - Macchine virtuali di AzureMv2-series - Azure Virtual Machines
description: Specifiche per le macchine virtuali della serie Mv2.
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: article
ms.date: 04/07/2020
ms.author: lahugh
ms.openlocfilehash: 764dc93608ae3b8882b7048a722c6d3415cbc644
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885787"
---
# <a name="mv2-series"></a>Serie Mv2

La serie Mv2 è dotata di una piattaforma ad alta velocità effettiva e bassa latenza in esecuzione su un processore Intel ® Xeon ® Platinum 8180M 2.5GHz (Skylake) con una frequenza di base all core di 2,5 GHz e una frequenza massima di turbo di 3,8 GHz. Tutte le macchine virtuali della serie Mv2 possono utilizzare dischi persistenti sia standard che premium. Le istanze di serie Mv2 sono dimensioni delle macchine virtuali ottimizzate per la memoria che offrono prestazioni di calcolo senza precedenti per supportare carichi di lavoro e database in memoria di grandi dimensioni, con un rapporto tra memoria e CPU elevato, ideale per server di database relazionali, cache di grandi dimensioni e analisi in memoria.

Funzionalità intel® tecnologia Hyper-Threading di serie Mv2

Archiviazione Premium: supportata

Memorizzazione nella cache di archiviazione Premium: supportata

Live Migration: non supportato

Aggiornamenti di conservazione della memoria: non supportatiMemory Preserving Updates: Not Supported

Acceleratore di scrittura: [supportato](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

|Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima di archiviazione temporanea e nella cache: IOPS/MBps (dimensioni della cache in GiB) | Max velocità effettiva del disco non memorizzato nella cache: IOPS/MBps | Schede di interfaccia di rete max/larghezza di banda della rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| <sup>Standard_M208ms_v2 1</sup> | 208 | 5700 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| <sup>Standard_M208s_v2 1</sup> | 208 | 2850 | 4096 | 64 | 80000 / 800 (7040) | 40000 / 1000 | 8 / 16000 |
| Standard_M416ms_v2<sup>1</sup> | 416 | 11400 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8/32000 |
| Standard_M416s_v2<sup>1</sup> | 416 | 5700 | 8192 | 64 | 250000 / 1600 (14080) | 80000 / 2000 | 8/32000 |

<sup>1</sup> i VM di serie Mv2 sono solo di generazione 2.1 Mv2-series VMs are generation 2 only. Se si usa Linux, vedere [Supporto per le macchine virtuali di generazione 2 in Azure](./linux/generation-2.md) per istruzioni su come trovare e selezionare un'immagine. Se si usa Windows, vedere [Supporto per le macchine virtuali di generazione 2 in Azure](./windows/generation-2.md) per istruzioni su come trovare e selezionare un'immagine. Versioni minime del sistema operativo richieste per tutti e quattro i diversi tipi di VM serie Mv2 elencano come:

- Windows Server 2019 o versione successiva
- SUSE Linux Enterprise Server 12 SP4 e versioni successive o SUSE Linux Enterprise Server 15 SP1 e versioni successive
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 o versioni successive 
- Oracle Enterprise Linux 7.7 o versione successiva



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Altre dimensioni

- [Scopo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
