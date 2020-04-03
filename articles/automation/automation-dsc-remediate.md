---
title: Correggere i server di configurazione dello stato di automazione di Azure non conformiRemediate non-compliant Azure Automation State Configuration servers
description: Come riapplicare le configurazioni su richiesta ai server in cui lo stato di configurazione è stato derivato
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: f4ca76f4be9d00e185f8774fc33296d1af1aeece
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585508"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Correggere i server DSC non conformi

Quando i server vengono registrati con la configurazione dello stato di automazione di Azure, la 'Modalità di configurazione' è impostata su ApplyOnly, ApplyandMonitor o ApplyAndAutoCorrect.When servers are registered with Azure Automation State Configuration, the 'Configuration Mode' is set to ApplyOnly, ApplyandMonitor, or ApplyAndAutoCorrect.
Se la modalità non è impostata su Correzione automatica, i server che si allontanano da uno stato conforme per qualsiasi motivo rimarranno non conformi fino a quando non verranno corretti manualmente.

Il calcolo di Azure offre una funzionalità denominata Esegui comando che consente ai clienti di eseguire script all'interno di macchine virtuali.
Questo documento fornisce script di esempio per questa funzionalità quando si corregge manualmente la deriva della configurazione.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Corretta deriva di macchine virtuali Windows tramite PowerShell

Per istruzioni dettagliate sull'uso della funzionalità Esegui comando nelle macchine virtuali Windows, vedere la pagina della documentazione [Eseguire script di PowerShell nella macchina virtuale Windows con Esegui comando](/azure/virtual-machines/windows/run-command).

Per forzare un nodo Configurazione stato di automazione di `Update-DscConfiguration` Azure a scaricare la configurazione più recente e applicarla, usare il cmdlet.
Per informazioni dettagliate, vedere la documentazione del cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva corretta delle macchine virtuali Linux

Funzionalità simili non è attualmente disponibile per i server Linux.Similar functionality isn't currently available for Linux servers.
L'unica opzione è ripetere il processo di registrazione.
Per i nodi di Azure, la correzione della deriva può essere eseguita dal portale o tramite i cmdlet di automazione Az.For Azure nodes, drift-correction can be done from the portal or using Az Automation cmdlets.
I dettagli su questo processo sono documentati nella pagina [Onboarding di macchine per la gestione da Configurazione dello stato](/azure/automation/automation-dsc-onboarding#onboard-a-vm-using-azure-portal)di automazione di Azure .
Per i nodi ibridi, la correzione della deriva può essere eseguita utilizzando gli script Python inclusi.
Vedere la documentazione in [PowerShell DSC per il repository Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
