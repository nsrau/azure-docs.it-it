---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 8f3d57f8f1f3631421618e31e943606a16e6bf5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670020"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

### <a name="nvidia-tesla-cuda-drivers"></a>Driver NVIDIA Tesla (CUDA)

I driver NVIDIA Tesla (CUDA) per le macchine virtuali serie NC, NCv2, NCv3 e ND (facoltativi per la serie NV) sono supportati solo nei sistemi operativi elencati nella tabella seguente. I collegamenti per il download dei driver sono quelli aggiornati al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Windows Server, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Windows Server 2016 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.


| OS | Driver |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Driver NVIDIA GRID

Microsoft ridistribuisce i programmi di installazione dei driver NVIDIA-GRID per le macchine virtuali serie NV usate come workstation virtuali o per applicazioni virtuali. Installare solo questi driver GRID nelle macchine virtuali NV di Azure, solo nei sistemi operativi elencati nella tabella seguente. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure.

| OS | Driver |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [GRID 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |