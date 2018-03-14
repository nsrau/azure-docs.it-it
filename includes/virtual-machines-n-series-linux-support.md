---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Serie NC, NCv2, NCv3 e ND: driver NVIDIA CUDA
| Distribuzione | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 o 7.4<br/><br/> CentOS 7.3 o 7.4 | NVIDIA CUDA 9.1, driver branch R390 |

> [!IMPORTANT]
> Assicurarsi di installare o eseguire l'aggiornamento ai driver CUDA più recenti per la distribuzione. I driver precedenti alla versione R390 potrebbero avere problemi con i kernel Linux aggiornati.
>

### <a name="nv-series---nvidia-grid-drivers"></a>Serie NV: driver NVIDIA GRID

| Distribuzione | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>CentOS-based 7.3 | NVIDIA GRID 5.2, driver branch R384|

> [!NOTE]
> Microsoft ridistribuisce programmi di installazione dei driver NVIDIA GRID per le macchine virtuali NV. Nelle macchine virtuali NV di Azure installare solo driver GRID di questo tipo. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure.
>

> [!WARNING] 
> L'installazione di software di terze parti in prodotti Red Hat può influenzare i termini di supporto di Red Hat. Vedere l'[articolo della Knowledge base di Red Hat](https://access.redhat.com/articles/1067).
>
