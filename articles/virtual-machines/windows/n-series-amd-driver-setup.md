---
title: Azure N-series AMD GPU driver setup for Windows
description: Come configurare i driver gPU AMD per le macchine virtuali Serie N che eseguono Windows Server o Windows in Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 63114bdf60c1feb2b6cb1092ef78397efdc5b666
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865767"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installare i driver GPU AMD nelle macchine virtuali della serie N che eseguono WindowsInstall AMD GPU drivers on N-series VMs running Windows

Per sfruttare le funzionalità GPU delle nuove macchine virtuali della serie NVv4 di Azure che eseguono Windows, è necessario installare i driver AMD GPU. L'estensione del driver AMD sarà disponibile nelle prossime settimane. In questo articolo vengono forniti i sistemi operativi supportati, i driver e i passaggi di installazione e verifica manuali.

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Installazione del driver

1. Connettersi da Desktop remoto a ogni macchina virtuale della serie NVv4.Connect by Remote Desktop to each NVv4-series VM.

2. Se si è un cliente di anteprima NVv4, arrestare la macchina virtuale e attendere che si sposti allo stato Arrestato(Deal-dalloca).

3. Avviare la macchina virtuale e scaricare l'utilità [amD Cleanup](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)più recente. Disinstallare il driver esistente eseguendo "amdcleanuputility-x64.exe". Si prega di NON utilizzare alcuna utilità di pulizia esistente che è stata installata con il driver precedente.  

4. Scaricare e installare il driver più recente.

5. Riavviare la macchina virtuale.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. The following example shows successful configuration of the Radeon Instinct MI25 card on an Azure NVv4 VM.
<br />
![Proprietà del driver GPU](./media/n-series-amd-driver-setup/device-manager.png)

È possibile utilizzare dxdiag per verificare le proprietà di visualizzazione della GPU, inclusa la RAM video. The following example shows a 1/2 partition of the Radeon Instinct MI25 card on an Azure NVv4 VM.
<br />
![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-output.png)

Se si esegue Windows 10 build 1903 o versione successiva, dxdiag non mostrerà alcuna informazione nella scheda 'Display'. Si prega di utilizzare l'opzione 'Salva tutte le informazioni' in basso e il file di output mostrerà le informazioni relative a AMD MI25 GPU.

![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


