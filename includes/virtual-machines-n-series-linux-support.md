---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7139ec67536a1c0e41c991db6d867b956f995c11
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/03/2018
---
## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>Serie NC, NCv2, NCv3 e ND: driver NVIDIA CUDA

Le informazioni sul driver CUDA nella tabella seguente sono quelle aggiornate al momento della pubblicazione. Per i driver CUDA più aggiornati, visitare il sito Web [NVIDIA](https://developer.nvidia.com/cuda-zone). Assicurarsi di installare o eseguire l'aggiornamento ai driver CUDA più recenti per la distribuzione. 

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Linux, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Ubuntu 16.04 LTS o CentOS 7.4 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.

| Distribuzione | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 o 7.4<br/><br/> Basata su CentOS 7.3 o 7.4, basata su CentOS 7.4 HPC | NVIDIA CUDA 9.1, driver branch R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>Serie NV: driver NVIDIA GRID

Microsoft ridistribuisce programmi di installazione dei driver NVIDIA GRID per le macchine virtuali NV. Nelle macchine virtuali NV di Azure installare solo driver GRID di questo tipo. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure.

| Distribuzione | Driver |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 o 7.4<br/><br/>Basata su CentOS 7.3 o 7.4 | NVIDIA GRID 6.0, driver branch R390|



> [!WARNING] 
> L'installazione di software di terze parti in prodotti Red Hat può influenzare i termini di supporto di Red Hat. Vedere l'[articolo della Knowledge base di Red Hat](https://access.redhat.com/articles/1067).
>
