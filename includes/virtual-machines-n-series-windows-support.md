---
title: File di inclusione
description: File di inclusione
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 00661043d1ec9769adbf4119a2c9c1925dcd29fa
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186371"
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
- [Griglia 11 (451,48)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe)
- [Griglia 10,1 (442,06)](https://download.microsoft.com/download/b/8/f/b8f5ecec-b8f9-47de-b007-ac40adc88dc8/442.06_grid_win10_64bit_international_whql.exe) (. exe) 

Per Windows Server 2012 R2: 
- [Griglia 11 (451,48)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)
- [Griglia 10,1 (442,66)](https://download.microsoft.com/download/4/3/3/4330fd5c-c685-4ca1-abca-3b2fb3c11d2e/442.06_grid_win8_win7_64bit_international_whql.exe) (. exe)  
