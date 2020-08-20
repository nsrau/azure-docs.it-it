---
title: Convenzioni di denominazione delle dimensioni delle macchine virtuali di Azure
description: Illustra le convenzioni di denominazione usate per le dimensioni delle macchine virtuali di Azure
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 13894e534dc8d6dd89baf75ea2bd3b6500b718f7
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650962"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Convenzioni di denominazione per le dimensioni delle macchine virtuali di Azure

Questa pagina descrive le convenzioni di denominazione usate per le macchine virtuali di Azure. Le macchine virtuali usano queste convenzioni di denominazione per indicare caratteristiche e specifiche variabili.

## <a name="naming-convention-explanation"></a>Spiegazione della convenzione di denominazione

**[Famiglia]**  +  **[Sottofamiglia *]**  +  **[# di vCPU]**  +  **[Funzionalità additive]**  +  **[Tipo di acceleratore *]**  +  **[Versione]**

|valore | Spiegazione|
|---|---|
| Famiglia | Indica la serie di famiglie di macchine virtuali| 
| * Sottofamiglia | Usato solo per differenziazioni specifiche delle macchine virtuali|
| n. di vCPU| Indica il numero di vCPU della macchina virtuale |
| Funzionalità additive | Una o più lettere minuscole indicano funzionalità additive, ad esempio: <br> a = processore basato su AMD <br> d = disco (il disco temporaneo locale è presente); per le macchine virtuali di Azure più recenti, vedere [Ddv4 e serie Ddsv4](./ddv4-ddsv4-series.md) <br> h = ibernazione in grado di supportare <br> i = dimensioni isolate <br> l = memoria insufficiente; quantità inferiore di memoria rispetto alle dimensioni a elevato utilizzo di memoria <br> m = elevato utilizzo di memoria; maggiore quantità di memoria in una determinata dimensione <br> t = memoria minima; quantità minima di memoria in una determinata dimensione <br> r = RDMA in grado di supportare <br> s = archiviazione Premium, incluso possibile uso di [ultra SSD](./disks-types.md#ultra-disk) (Nota: alcune dimensioni più recenti senza l'attributo s possono comunque supportare l'archiviazione Premium, ad esempio M128, m64 e così via).<br> |
| * Tipo di acceleratore | Indica il tipo di acceleratore hardware negli SKU specializzati/GPU. Solo i nuovi SKU specializzati/GPU avviati dal terzo trimestre 2020 avranno il tasto di scelta rapida hardware nel nome. |
| Versione | Indica la versione della serie della famiglia di VM |

## <a name="example-breakdown"></a>Suddivisione di esempio

**[Famiglia]**  +  **[Sottofamiglia *]**  +  **[# di vCPU]**  +  **[Funzionalità additive]**  +  **[Tipo di acceleratore *]**  +  **[Versione]**

### <a name="example-1-m416ms_v2"></a>Esempio 1: M416ms_v2

|valore | Spiegazione|
|---|---|
| Famiglia | M | 
| n. di vCPU | 416 |
| Funzionalità additive | m = utilizzo intensivo della memoria <br> s = archiviazione Premium in grado di supportare |
| Versione | v2 |

### <a name="example-2-nv16as_v4"></a>Esempio 2: NV16as_v4

|valore | Spiegazione|
|---|---|
| Famiglia | N | 
| Sottofamiglia | V |
| n. di vCPU | 16 |
| Funzionalità additive | a = processore basato su AMD <br> s = archiviazione Premium in grado di supportare |
| Versione | v4 |

### <a name="example-3-nc4as_t4_v3"></a>Esempio 3: NC4as_T4_v3

|valore | Spiegazione|
|---|---|
| Famiglia | N | 
| Sottofamiglia | C |
| n. di vCPU | 4 |
| Funzionalità additive | a = processore basato su AMD <br> s = archiviazione Premium in grado di supportare |
| Tipo di acceleratore | T4 |
| Versione | v3 |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [dimensioni delle macchine virtuali](./sizes.md) disponibili in Azure. 