---
title: includere file
description: includere file
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: bfb7d1d52549d7fda9547b65a259fe2ce73f8839
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997915"
---
## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

### <a name="nvidia-tesla-cuda-drivers"></a>Driver NVIDIA Tesla (CUDA)

I driver NVIDIA Tesla (CUDA) per le macchine virtuali serie NC, NCv2, NCv3, NCasT4_v3, ND e NDv2 (facoltativo per serie NV) sono supportati solo nei sistemi operativi elencati nella tabella seguente. I collegamenti per il download dei driver sono quelli aggiornati al momento della pubblicazione. Per i driver più aggiornati, visitare il sito Web di [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Anziché installare manualmente i driver CUDA in una VM Windows Server, è possibile distribuire un'immagine della [macchina virtuale per data science](../articles/machine-learning/data-science-virtual-machine/overview.md) di Azure. Le edizioni della macchina virtuale per data science per Windows Server 2016 preinstallano i driver NVIDIA CUDA, CUDA Deep Neural Network Library e altri strumenti.


| OS | Driver |
| -------- |------------- |
| Windows Server 2019 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |
| Windows Server 2016 | [451,82](http://us.download.nvidia.com/tesla/451.82/451.82-tesla-desktop-winserver-2019-2016-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Driver NVIDIA GRID

Microsoft ridistribuisce i programmi di installazione dei driver NVIDIA GRID per le macchine virtuali serie NV e NVv3 utilizzate come workstation virtuali o per le applicazioni virtuali. Installare solo i driver della griglia nelle macchine virtuali serie NV di Azure, solo nei sistemi operativi elencati nella tabella seguente. Questi driver includono la licenza per il software della GPU virtuale GRID in Azure. Non è necessario configurare un server licenze software NVIDIA vGPU.

I driver della griglia ridistribuiti da Azure non funzionano con macchine virtuali serie non NV come le macchine virtuali serie NC, NCv2, NCv3, ND e NDv2.

Si noti che l'estensione NVIDIA installerà sempre il driver più recente. Sono disponibili collegamenti alla versione precedente per i clienti che hanno una dipendenza da una versione precedente.

Per Windows Server 2019, Windows Server 2016 e Windows 10 (fino alla build 2004):
- [Griglia 11 (452,39)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Griglia 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 

Per Windows Server 2012 R2: 
- [Griglia 11,0 (451,48)](https://download.microsoft.com/download/C/1/4/c147a482-1364-4d12-b9e3-0beda0f00a13/451.48_grid_win10_server2016_server2019_64bit_international.exe) (. exe) 
- [Griglia 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
