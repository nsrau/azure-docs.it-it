---
title: Correggere i server di State Configuration di Automazione di Azure non conformi
description: Questo articolo illustra come riapplicare le configurazioni su richiesta ai server in cui è deviato lo stato di configurazione.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: ff785bf3ace7c65f83fe8e505f0544edd24776d8
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836856"
---
# <a name="remediate-noncompliant-azure-automation-state-configuration-servers"></a>Correggere i server di State Configuration di Automazione di Azure non conformi

Quando i server sono registrati con State Configuration di Automazione di Azure, la modalità di configurazione è impostata su `ApplyOnly`, `ApplyandMonitor` o `ApplyAndAutoCorrect`. Se la modalità non è impostata su `ApplyAndAutoCorrect`, i server con stato deviato da conforme per qualsiasi motivo rimangono non conformi fino a quando non vengono corretti manualmente.

Calcolo di Azure offre una funzionalità denominata Esegui comando che consente ai clienti di eseguire script all'interno di macchine virtuali.
Questo documento fornisce script di esempio per questa funzionalità quando si corregge manualmente la deviazione della configurazione.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Correggere la deviazione delle macchine virtuali Windows con PowerShell

È possibile correggere la deviazione delle macchine virtuali Windows utilizzando la funzionalità del comando `Run`. Vedere [Eseguire gli script di PowerShell nella macchina virtuale Windows con Esegui comando](/azure/virtual-machines/windows/run-command).

Per forzare il download e l'applicazione della configurazione più recente da parte di un nodo State Configuration di Automazione di Azure, usare il cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Correggere la deviazione delle macchine virtuali Linux

Per le macchine virtuali Linux non è possibile usare il comando `Run`. È possibile correggere la deviazione per queste macchine solo ripetendo il processo di registrazione. 

Per i nodi di Azure, è possibile correggere la deviazione dal portale di Azure o mediante i cmdlet Az module. Informazioni dettagliate su questo processo sono descritte in [Abilitare una macchina virtuale usando il portale di Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).

Per i nodi ibridi è possibile correggere la deviazione mediante gli script Python. Vedere [Performing DSC operations from the Linux computer](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer) (Esecuzione di operazioni DSC dal computer Linux).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Per un esempio dell'uso di State Configuration di Automazione di Azure in una pipeline di distribuzione continua, vedere [Configurare la distribuzione continua con Chocolatey](automation-dsc-cd-chocolatey.md).