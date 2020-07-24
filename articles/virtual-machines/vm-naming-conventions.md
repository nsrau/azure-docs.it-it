---
title: Convenzioni di denominazione delle dimensioni delle VM di Azure
description: Illustra le convenzioni di denominazione usate per le dimensioni delle macchine virtuali di Azure
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 2059c6f374e4cd5c2518e2fc0ac0da5858b99825
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131719"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Convenzioni di denominazione per le dimensioni delle macchine virtuali di Azure

Questa pagina descrive le convenzioni di denominazione usate per le macchine virtuali di Azure. Le macchine virtuali usano queste convenzioni di denominazione per indicare caratteristiche e specifiche variabili.

## <a name="naming-convention-explanation"></a>Spiegazione della convenzione di denominazione

**[Famiglia]**  +  **[Sottofamiglia *]**  +  **[# di vCPU]**  +  **[Funzionalità additive]**  +  **[Tipo di acceleratore *]**  +  **[Versione]**

|valore | Spiegazione|
|---|---|
| Standard, di base o sperimentale | "Standard" è il valore predefinito assegnato per tutte le dimensioni delle VM GA | 
| Famiglia | Indica la serie di famiglie di macchine virtuali| 
| * Sottofamiglia | Usato solo per differenziazioni specifiche delle macchine virtuali|
| n. di vCPU| Indica il numero di vCPU della macchina virtuale |
| Funzionalità additive | Una o più lettere minuscole indicano funzionalità additive, ad esempio: <br> a = processore basato su AMD <br> d = disco (il disco temporaneo locale è presente); per le macchine virtuali di Azure più recenti, vedere [Ddv4 e serie Ddsv4](./ddv4-ddsv4-series.md) <br> h = ibernazione in grado di supportare <br> i = isolato <br> l = memoria insufficiente <br> m = utilizzo intensivo della memoria <br> t = memoria minima <br> r = RDMA <br> s = archiviazione Premium, incluso possibile uso di [ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#ultra-disk) (Nota: alcune dimensioni più recenti senza l'attributo s possono comunque supportare l'archiviazione Premium, ad esempio M128, m64 e così via).<br> |
| * Tipo di acceleratore | Indica il tipo di acceleratore hardware negli SKU specializzati/GPU. Solo i nuovi SKU specializzati/GPU avviati dal terzo trimestre 2020 avranno il tasto di scelta rapida hardware nel nome. |
| Version | Indica la versione della serie della famiglia di VM |

## <a name="example-breakdown"></a>Suddivisione di esempio

**[Famiglia]**  +  **[Sottofamiglia *]**  +  **[# di vCPU]**  +  **[Funzionalità additive]**  +  **[Tipo di acceleratore *]**  +  **[Versione]**

### <a name="example-1-m416ms_v2"></a>Esempio 1: M416ms_v2

|valore | Spiegazione|
|---|---|
| Famiglia | M | 
| n. di vCPU | 416 |
| Funzionalità additive | m = utilizzo intensivo della memoria <br> s = archiviazione Premium in grado di supportare |
| Version | v2 |

### <a name="example-2-nv16as_v4"></a>Esempio 2: NV16as_v4

|valore | Spiegazione|
|---|---|
| Famiglia | N | 
| Sottofamiglia | V |
| n. di vCPU | 16 |
| Funzionalità additive | a = processore basato su AMD <br> s = archiviazione Premium in grado di supportare |
| Version | v4 |

### <a name="example-3-nc4as_t4_v3"></a>Esempio 3: NC4as_T4_v3

|valore | Spiegazione|
|---|---|
| Famiglia | N | 
| Sottofamiglia | C |
| n. di vCPU | 4 |
| Funzionalità additive | a = processore basato su AMD <br> s = archiviazione Premium in grado di supportare |
| Tipo di acceleratore | T4 |
| Version | v3 |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle [dimensioni delle macchine virtuali](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) disponibili in Azure. 
