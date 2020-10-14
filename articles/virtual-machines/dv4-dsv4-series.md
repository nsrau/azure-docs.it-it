---
title: Serie DV4 e Dsv4-macchine virtuali di Azure
description: Specifiche per le macchine virtuali serie DV4 e Dsv4.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: ca01397959aa50c77218742320f8ced128876bad
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92018679"
---
# <a name="dv4-and-dsv4-series"></a>Serie Dv4 e Dsv4

La serie DV4 e Dsv4 viene eseguita sui processori Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) in una configurazione con Hyper-Threading, garantendo una proposta di valore migliore per la maggior parte dei carichi di lavoro per uso generico. Offre una velocità di clock di tutti i core di 3,4 GHz. 

> [!NOTE]
> Per le domande frequenti, vedere  [dimensioni delle macchine virtuali di Azure senza disco temporaneo locale](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>Serie DV4

Le dimensioni della serie DV4 vengono eseguite su Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Le dimensioni della serie DV4 offrono una combinazione di opzioni di vCPU, memoria e archiviazione remota per la maggior parte dei carichi di lavoro di produzione. Le macchine virtuali serie DV4 includono la [ &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali. Per usare i dischi di archiviazione Premium, usare le dimensioni Dsv4. I prezzi e i contatori di fatturazione per le dimensioni Dsv4 sono uguali a quelli della serie DV4.

[ACU](acu.md): 195-210<br>
[Archiviazione Premium](premium-storage-performance.md): non supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): non supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1<br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Solo archiviazione remota | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Solo archiviazione remota | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Solo archiviazione remota | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Solo archiviazione remota | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Solo archiviazione remota | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Solo archiviazione remota | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Solo archiviazione remota | 32 | 8|30000 |

## <a name="dsv4-series"></a>Serie Dsv4

Le dimensioni della serie Dsv4 vengono eseguite su Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake). Le dimensioni della serie DV4 offrono una combinazione di opzioni di vCPU, memoria e archiviazione remota per la maggior parte dei carichi di lavoro di produzione. Le macchine virtuali serie Dsv4 includono la [ &reg; tecnologia Intel Hyper-Threading](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). L'archiviazione su disco dati remoto viene fatturata separatamente dalle macchine virtuali.

[ACU](acu.md): 195-210<br>
[Archiviazione Premium](premium-storage-performance.md): supportata<br>
[Caching archiviazione Premium](premium-storage-performance.md): supportato<br>
[Live Migration](maintenance-and-updates.md): supportato<br>
[Aggiornamenti con mantenimento della memoria](maintenance-and-updates.md): supportati<br>
[Supporto](generation-2.md)per la generazione di VM: generazione 1 e 2<br>
<br>

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | Numero massimo di dischi dati | Velocità effettiva massima del disco senza memorizzazione nella cache: Operazioni di I/O al secondo/Mbps | Schede di interfaccia di rete max|Larghezza di banda di rete prevista (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Solo archiviazione remota | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Solo archiviazione remota | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Solo archiviazione remota | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Solo archiviazione remota | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Solo archiviazione remota | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Solo archiviazione remota | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Solo archiviazione remota | 32 | 80000/1200 | 8|30000 |
