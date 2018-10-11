---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 09/24/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4da90cf636ab2010d7c369f4c13e45190dc6b2db
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2018
ms.locfileid: "48020804"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

### <a name="nvidia-tesla-cuda-drivers"></a>Driver NVIDIA Tesla (CUDA)

I driver NVIDIA Tesla (CUDA) per le macchine virtuali serie NC, NCv2, NCv3 e ND (facoltativi per la serie NV) sono supportati solo nei sistemi operativi elencati nella tabella seguente. I collegamenti per il download dei driver sono quelli aggiornati al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Windows Server, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Windows Server 2016 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.


| OS | Driver |
| -------- |------------- |
| Windows Server 2016 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](http://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Driver NVIDIA GRID

Microsoft ridistribuisce i programmi di installazione dei driver NVIDIA-GRID per le macchine virtuali serie NV amd NVV2 usate come workstation virtuali o per applicazioni virtuali. Installare solo questi driver GRID nelle macchine virtuali NV di Azure, solo nei sistemi operativi elencati nella tabella seguente. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure.

| OS | Driver |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [GRID 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |