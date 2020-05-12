---
title: Installazione del driver GPU AMD serie N di Azure per Windows
description: Come configurare i driver GPU AMD per le VM serie N che eseguono Windows Server o Windows in Azure
author: vikancha
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 1bcc13db3f503c80fda71a2104d0ff8d99e67df6
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198004"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installare i driver GPU AMD nelle VM serie N che eseguono Windows

Per sfruttare i vantaggi delle funzionalità GPU delle nuove macchine virtuali della serie NVv4 di Azure che eseguono Windows, è necessario installare i driver GPU AMD. L' [estensione AMD GPU driver](../extensions/hpccompute-amd-gpu-windows.md) installa driver GPU AMD in una macchina virtuale della serie NVv4. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione driver GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) per i sistemi operativi e i passaggi di distribuzione supportati.

Se si sceglie di installare manualmente i driver GPU AMD, questo articolo fornisce i sistemi operativi supportati, i driver e i passaggi di installazione e verifica.

Solo i driver GPU pubblicati da Microsoft sono supportati nelle VM NVv4. NON installare driver GPU da altre origini.

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD-Build 1903 <br/><br/>Windows 10-Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20. q 1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (. exe) |


## <a name="driver-installation"></a>Installazione del driver

1. Connettersi tramite Desktop remoto a ogni macchina virtuale della serie NVv4.

2. Se sei un cliente di NVv4 Preview, arresta la macchina virtuale e attendi lo stato di arresto (deallocazione).

3. Avviare la macchina virtuale e scaricare la versione più recente dell' [utilità di pulitura AMD](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe). Disinstallare il driver esistente eseguendo "amdcleanuputility-x64. exe". NON utilizzare alcuna utilità di pulizia esistente installata con il driver precedente.  

4. Scaricare e installare il driver più recente.

5. Riavviare la macchina virtuale.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.
<br />
![Proprietà del driver GPU](./media/n-series-amd-driver-setup/device-manager.png)

È possibile usare dxdiag per verificare le proprietà di visualizzazione della GPU, inclusa la RAM del video. Nell'esempio seguente viene illustrata una partizione 1/2 della scheda Radeon Instinct MI25 in una VM NVv4 di Azure.
<br />
![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-output.png)

Se si esegue Windows 10 Build 1903 o versione successiva, dxdiag non visualizzerà alcuna informazione nella scheda ' display '. Usare l'opzione ' Salva tutte le informazioni ' nella parte inferiore e il file di output visualizzerà le informazioni correlate alla GPU AMD MI25.

![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


