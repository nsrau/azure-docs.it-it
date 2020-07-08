---
title: Installazione del driver GPU AMD serie N di Azure per Windows
description: Informazioni su come configurare i driver GPU AMD per macchine virtuali serie N che eseguono Windows Server o Windows in Azure
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: c4ab6f8dd1c2484ca560fd570f88476f31897b28
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84975189"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Installare i driver GPU AMD sulle macchine virtuali serie N eseguite in Windows

Per usufruire delle funzionalità GPU delle nuove macchine virtuali serie NVv4 di Azure che eseguono Windows, è necessario installare i driver GPU AMD. L'[estensione del driver GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) installa driver GPU AMD in una macchina virtuale serie NVv4. Installare o gestire l'estensione usando il portale di Azure o strumenti come i modelli di Azure PowerShell Azure o Azure Resource Manager. Vedere la [documentazione dell'estensione dei driver GPU AMD](../extensions/hpccompute-amd-gpu-windows.md) per informazioni sui sistemi operativi supportati e sui passaggi di distribuzione.

Se si sceglie di installare manualmente i driver GPU AMD, questo articolo descrive i driver, i sistemi operativi supportati e i passaggi di installazione e verifica.

Nelle macchine virtuali NVv4 sono supportati solo i driver GPU pubblicati da Microsoft. NON installare driver GPU di altro tipo.

Per conoscere le specifiche base, le capacità di archiviazione e i dettagli relativi ai dischi, vedere [Dimensioni delle macchine virtuali Windows GPU](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="supported-operating-systems-and-drivers"></a>Sistemi operativi e driver supportati

| OS | Driver |
| -------- |------------- |
| Windows 10 EVD - Build 1903 <br/><br/>Windows 10 - Build 1809<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q1.1](https://download.microsoft.com/download/3/8/9/3893407b-e8aa-4079-8592-735d7dd1c19a/Radeon-Pro-Software-for-Enterprise-GA.exe) (.exe) |


## <a name="driver-installation"></a>Installazione del driver

1. Connettersi tramite Desktop remoto a ciascuna macchina virtuale serie NVv4.

2. Se è necessario disinstallare la versione precedente del driver, scaricare l'utilità di pulitura AMD [qui](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe) . non usare l'utilità fornita con la versione precedente del driver.

3. Scaricare e installare il driver più recente.

4. Riavviare la macchina virtuale.

## <a name="verify-driver-installation"></a>Verificare l'installazione del driver

È possibile verificare l'installazione del driver in Gestione dispositivi. L'esempio seguente illustra la corretta configurazione della scheda Radeon Instinct MI25 in una macchina virtuale NVv4 di Azure.
<br />
![Proprietà del driver GPU](./media/n-series-amd-driver-setup/device-manager.png)

È possibile usare dxdiag per verificare le proprietà di visualizzazione della GPU, inclusa la RAM video. L'esempio seguente illustra una partizione 1/2 della scheda Radeon Instinct MI25 in una macchina virtuale NVv4 di Azure.
<br />
![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Se si esegue Windows 10 Build 1903 o versione successiva, dxdiag non visualizzerà alcuna informazione nella scheda 'Display'. Usare quindi l'opzione 'Salva tutte le informazioni' nella parte inferiore e il file di output visualizzerà le informazioni correlate alla GPU AMD MI25.

![Proprietà del driver GPU](./media/n-series-amd-driver-setup/dxdiag-details.png)


