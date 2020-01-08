---
title: Installazione del driver GPU AMD serie N di Azure per Windows
description: Come configurare i driver GPU AMD per le VM serie N che eseguono Windows Server o Windows in Azure
services: virtual-machines-windows
author: vikancha
manager: jkabat
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 6f0aa8a08b8a502edbd15d3ede157b78a13b8588
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479358"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installare i driver GPU AMD nelle VM serie N che eseguono Windows

Per sfruttare i vantaggi delle funzionalità GPU delle nuove macchine virtuali della serie NVv4 di Azure che eseguono Windows, è necessario installare i driver GPU AMD. L'estensione del driver AMD sarà disponibile nelle prossime settimane. Questo articolo fornisce sistemi operativi supportati, driver e procedure manuali di installazione e verifica.

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

| Sistema operativo | Driver |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10-Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [19. q 4.1](https://download.microsoft.com/download/7/e/5/7e558ac0-3fff-413d-af62-800285a2fc53/Radeon-Pro-Software-for-Enterprise-19.Q4.1-Technical-Preview.exe) (. exe) |

## <a name="driver-installation"></a>Installazione del driver

1. Connettersi tramite Desktop remoto a ogni macchina virtuale della serie NVv4.

1. Scaricare, estrarre e installare il driver supportato per il sistema operativo Windows.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.
<br />
Proprietà del driver ![GPU](./media/n-series-amd-driver-setup/device-manager.png)

È possibile usare dxdiag per verificare le proprietà di visualizzazione della GPU, inclusa la RAM del video. Nell'esempio seguente viene illustrata una partizione 1/ottava della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.
<br />
Proprietà del driver ![GPU](./media/n-series-amd-driver-setup/dxdiag.png)
