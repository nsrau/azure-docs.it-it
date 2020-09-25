---
title: Serie ANGC T4 V3
description: Specifiche per le macchine virtuali della serie ANGC T4 V3.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: 17ce5314f58a92158ff4fd187ad0ca46bb14a275
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320167"
---
# <a name="ncast4_v3-series-in-preview"></a>Serie NCasT4_v3 (in anteprima) 

Le macchine virtuali serie NCasT4_v3 sono basate su GPU [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) e CPU AMD EPYC 7V12 (Roma). Le macchine virtuali sono dotate di un massimo di 4 GPU NVIDIA T4 con 16 GB di memoria ciascuno, fino a 64 core del processore AMD EPYC 7V12 (Roma) non multithread e 440 GiB della memoria di sistema. Queste macchine virtuali sono ideali per la distribuzione di servizi di intelligenza artificiale, ad esempio l'inferenza in tempo reale delle richieste generate dall'utente o per carichi di lavoro grafici e di visualizzazione interattivi tramite il Driver GRID e la tecnologia GPU virtuale di NVIDIA. I carichi di lavoro di calcolo GPU standard basati su CUDA, TensorRT, caffe, ONNX e altri Framework, o applicazioni grafiche con accelerazione GPU basate su OpenGL e DirectX, possono essere distribuiti in modo economico, con prossimità agli utenti, nella serie NCasT4_v3.

È possibile [inviare una richiesta](https://aka.ms/NCT4v3Preview) per partecipare al programma di anteprima.

<br>

ACU: 230-260

Archiviazione Premium:  Supportato

Memorizzazione nella cache Archiviazione Premium:  Supportato

Live Migration: Non supportato

Manutenzione con mantenimento della memoria: Non supportato

| Dimensione | vCPU | Memoria: GiB | GiB di archiviazione temp (unità SSD) | GPU | Memoria GPU: GiB | Numero massimo di dischi dati | Schede di interfaccia di rete max |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

Per sfruttare i vantaggi delle funzionalità GPU delle VM serie NCasT4_v3 di Azure che eseguono Windows o Linux, è necessario installare i driver GPU NVIDIA.

Per installare manualmente i driver GPU NVIDIA, vedere la pagina relativa alla [configurazione di driver GPU serie N per Windows](./windows/n-series-driver-setup.md) per i sistemi operativi supportati, i driver, l'installazione e i passaggi di verifica.

## <a name="other-sizes"></a>Altre dimensioni

- [Utilizzo generico](sizes-general.md)
- [Ottimizzate per la memoria](sizes-memory.md)
- [Ottimizzate per l'archiviazione](sizes-storage.md)
- [Ottimizzate per la GPU](sizes-gpu.md)
- [High Performance Computing (HPC)](sizes-hpc.md)
- [Generazioni precedenti](sizes-previous-gen.md)

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni su come le [unità di calcolo di Azure](acu.md) consentono di confrontare le prestazioni di calcolo negli SKU di Azure.
