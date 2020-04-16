---
title: Correggere i server di configurazione dello stato di automazione di Azure non conformiRemediate noncompliant Azure Automation State Configuration servers
description: Come riapplicare le configurazioni su richiesta ai server in cui lo stato di configurazione è stato derivato
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: cc5884e1f70bdccee4e7a113e6e3ee2d6604b50a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406078"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Correggere i server DSC non conformi

Quando i server vengono registrati con la configurazione `ApplyOnly`dello `ApplyandMonitor`stato `ApplyAndAutoCorrect`di automazione di Azure, la modalità di configurazione è impostata su , o . Se la modalità non `ApplyAndAutoCorrect`è impostata su , i server che si allontanano da uno stato conforme per qualsiasi motivo rimangono non conformi fino a quando non vengono corretti manualmente.

Il calcolo di Azure offre una funzionalità denominata Esegui comando che consente ai clienti di eseguire script all'interno di macchine virtuali.
Questo documento fornisce script di esempio per questa funzionalità quando si corregge manualmente la deriva della configurazione.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Corretta deriva di macchine virtuali Windows tramite PowerShell

Per istruzioni dettagliate sull'uso della funzionalità Esegui comando nelle macchine virtuali Windows, vedere la pagina della documentazione [Eseguire script di PowerShell nella macchina virtuale Windows con Esegui comando](/azure/virtual-machines/windows/run-command).

Per forzare un nodo Configurazione stato di automazione di Azure a scaricare la configurazione più recente e applicarla, usare il cmdlet [Update-DscConfiguration.To](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) force an Azure Automation State Configuration node to download the latest configuration and apply it, use the Update-DscConfiguration cmdlet.

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Deriva corretta delle macchine virtuali Linux

Funzionalità simili non è attualmente disponibile per i server Linux.Similar functionality isn't currently available for Linux servers.
L'unica opzione è ripetere il processo di registrazione.

Per i nodi di Azure, è possibile correggere la deriva dal portale di Azure o usando i cmdlet del modulo Az.For Azure nodes, you can correct drift from the Azure portal or using Az module cmdlets. I dettagli su questo processo sono documentati in [Onboarding di macchine per la gestione da Azure Automation State Configuration](automation-dsc-onboarding.md#onboard-vms-by-using-the-azure-portal).
Per i nodi ibridi, è possibile correggere la deriva utilizzando gli script Python inclusi.
Vedere [PowerShell DSC per Linux repository](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per un esempio di utilizzo della configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [Distribuzione continua tramite Configurazione dello stato](automation-dsc-cd-chocolatey.md)di automazione di Azure e Chocolatey .