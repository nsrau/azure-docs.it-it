---
title: includere file
description: includere file
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 5bfe077a99a3e89efe5fe4257a646484fb3810da
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96536914"
---
## <a name="supported-distributions-and-drivers"></a>Driver e distribuzioni supportati

### <a name="nvidia-cuda-drivers"></a>Driver NVIDIA CUDA

I driver NVIDIA CUDA per le macchine virtuali serie NC, NCv2, NCv3, ND e NDv2 (facoltativi per la serie NV) sono supportati solo nelle distribuzioni Linux elencate nella tabella seguente. Le informazioni sui driver CUDA sono quelle aggiornate al momento della pubblicazione. Per i driver CUDA più recenti e i sistemi operativi supportati, visitare il sito Web [NVIDIA](https://developer.nvidia.com/cuda-zone) . Assicurarsi di installare o eseguire l'aggiornamento ai driver CUDA più recenti per la distribuzione. 

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Linux, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Ubuntu 16.04 LTS o CentOS 7.4 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.


### <a name="nvidia-grid-drivers"></a>Driver NVIDIA GRID

Microsoft ridistribuisce i programmi di installazione dei driver NVIDIA GRID per le macchine virtuali serie NV e NVv3 utilizzate come workstation virtuali o per le applicazioni virtuali. Installare solo questi driver GRID nelle macchine virtuali NV di Azure, solo nei sistemi operativi elencati nella tabella seguente. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure. Non è necessario configurare un server licenze software NVIDIA vGPU.

I driver della griglia ridistribuiti da Azure non funzionano con macchine virtuali serie non NV come le macchine virtuali serie NC, NCv2, NCv3, ND e NDv2.

|Distribuzione|Driver|
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,6 a 7,8, 8,0, 8,1<br/><br/>Basato su CentOS 7,6, 7,7, 8,0, 8 (1911)<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 11,2, driver Branch [R450](https://go.microsoft.com/fwlink/?linkid=874272)(. exe)|

Visitare [GitHub](https://github.com/Azure/azhpc-extensions/blob/master/NvidiaGPU/resources.json) per l'elenco completo di tutti i collegamenti precedenti di NVIDIA Grid driver.

> [!WARNING] 
> L'installazione di software di terze parti in prodotti Red Hat può influenzare i termini di supporto di Red Hat. Vedere l'[articolo della Knowledge base di Red Hat](https://access.redhat.com/articles/1067).
>
