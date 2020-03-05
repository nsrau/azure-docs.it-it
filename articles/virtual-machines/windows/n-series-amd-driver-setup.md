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
ms.openlocfilehash: 164f07f6545c1c225814958bba5722536b11a9b4
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78269429"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installare i driver GPU AMD nelle VM serie N che eseguono Windows

Per sfruttare i vantaggi delle funzionalità GPU delle nuove macchine virtuali della serie NVv4 di Azure che eseguono Windows, è necessario installare i driver GPU AMD. L'estensione del driver AMD sarà disponibile nelle prossime settimane. Questo articolo fornisce sistemi operativi supportati, driver e procedure manuali di installazione e verifica.

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10-Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Installazione del driver

1. Connettersi tramite Desktop remoto a ogni macchina virtuale della serie NVv4.

2. Se sei un cliente di NVv4 Preview, arresta la macchina virtuale e attendi lo stato di arresto (deallocazione).

3. Avviare la macchina virtuale e quindi disinstallare il driver di anteprima eseguendo "amdcleanuputility-x64. exe" che si trova nella cartella ". ..\AMDCleanUninstallUtility". Il percorso esatto può variare in base alla posizione in cui si trovano i file di installazione dei driver precedenti.  

4. Scaricare e installare il driver più recente.

5. Riavviare la macchina virtuale.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.
<br />
Proprietà del driver ![GPU](./media/n-series-amd-driver-setup/device-manager.png)

È possibile usare dxdiag per verificare le proprietà di visualizzazione della GPU, inclusa la RAM del video. Nell'esempio seguente viene illustrata una partizione 1/ottava della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.
<br />
Proprietà del driver ![GPU](./media/n-series-amd-driver-setup/dxdiag.png)
