---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0264f92fa10bd503a2811ce40ee0b8d4edd5f3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669833"
---
## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

### <a name="nvidia-cuda-drivers"></a>Driver NVIDIA CUDA

I driver NVIDIA CUDA per le macchine virtuali serie NC, NCv2, NCv3 e ND (facoltativi per la serie NV) sono supportati solo nelle distribuzioni Linux elencate nella tabella seguente. Le informazioni sui driver CUDA sono quelle aggiornate al momento della pubblicazione. Per i driver CUDA più aggiornati, visitare il sito Web [NVIDIA](https://developer.nvidia.com/cuda-zone). Assicurarsi di installare o eseguire l'aggiornamento ai driver CUDA più recenti per la distribuzione. 

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Linux, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Ubuntu 16.04 LTS o CentOS 7.4 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.

| Distribuzione | Driver |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 o 7.4<br/><br/> Basata su CentOS 7.3 o 7.4, basata su CentOS 7.4 HPC | NVIDIA CUDA 9.1, driver branch R390 |

### <a name="nvidia-grid-drivers"></a>Driver NVIDIA GRID

Microsoft ridistribuisce i programmi di installazione dei driver NVIDIA-GRID per le macchine virtuali serie NV usate come workstation virtuali o per applicazioni virtuali. Installare solo questi driver GRID in macchine virtuali NV di Azure, solo nelle distribuzioni elencate nella tabella seguente. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure.

| Distribuzione | Driver |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 o 7.4<br/><br/>Basata su CentOS 7.3 o 7.4 | NVIDIA GRID 6.0, driver branch R390|



> [!WARNING] 
> L'installazione di software di terze parti in prodotti Red Hat può influenzare i termini di supporto di Red Hat. Vedere l'[articolo della Knowledge base di Red Hat](https://access.redhat.com/articles/1067).
>
