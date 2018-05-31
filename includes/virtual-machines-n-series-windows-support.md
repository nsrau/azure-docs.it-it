---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/16/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9b36a8e90c20c8f0d08ca241e48e9306fb5b10f8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34307506"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>Serie NC, NCv2, NCv3 e ND: driver NVIDIA Tesla (CUDA)

I collegamenti ai download dei driver nella tabella seguente sono quelli attivi al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Windows Server, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Windows Server 2016 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.


| OS | Driver |
| -------- |------------- |
| Windows Server 2016 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [391.29](http://us.download.nvidia.com/Windows/Quadro_Certified/391.29/391.29-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>Serie NV: driver NVIDIA GRID

Microsoft ridistribuisce programmi di installazione dei driver NVIDIA GRID per le macchine virtuali NV. Nelle macchine virtuali NV di Azure installare solo driver GRID di questo tipo. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure.

| OS | Driver |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843), con estensione EXE |
| Windows Server 2012 R2 | [GRID 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844), con estensione EXE  |