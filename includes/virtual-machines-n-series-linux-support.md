---
title: File di inclusione
description: File di inclusione
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9f0d694badaa6f4484a13364c6a56aee2ad1dcfb
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744268"
---
## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

### <a name="nvidia-cuda-drivers"></a>Driver NVIDIA CUDA

I driver NVIDIA CUDA per le macchine virtuali serie NC, NCv2, NCv3, ND e NDv2 (facoltativi per la serie NV) sono supportati solo nelle distribuzioni Linux elencate nella tabella seguente. Le informazioni sui driver CUDA sono quelle aggiornate al momento della pubblicazione. Per i driver CUDA più aggiornati, visitare il sito Web [NVIDIA](https://developer.nvidia.com/cuda-zone). Assicurarsi di installare o eseguire l'aggiornamento ai driver CUDA più recenti per la distribuzione. 

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Linux, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Ubuntu 16.04 LTS o CentOS 7.4 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.

| Distribuzione | Driver |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> HPC basato su CentOS 7.3, 7.4, 7.5, 7.6, basata su CentOS 7.4 | NVIDIA CUDA 10.1, ramo del driver R418 |

### <a name="nvidia-grid-drivers"></a>Driver NVIDIA GRID

Microsoft ridistribuisce i programmi di installazione dei driver NVIDIA-GRID per le macchine virtuali serie NV e NVV2 usate come workstation virtuali o per applicazioni virtuali. Installare solo questi driver GRID nelle macchine virtuali NV di Azure, solo nei sistemi operativi elencati nella tabella seguente. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure. Non è necessario configurare un server licenze software NVIDIA vGPU.

| Distribuzione | Driver |
| --- | -- |
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/>Basato su CentOS 7.3, 7.4, 7.5, 7.6 | NVIDIA griglia 8.0, ramo del driver R418|

> [!WARNING] 
> L'installazione di software di terze parti in prodotti Red Hat può influenzare i termini di supporto di Red Hat. Vedere l'[articolo della Knowledge base di Red Hat](https://access.redhat.com/articles/1067).
>
