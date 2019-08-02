---
title: Correggere i server di configurazione dello stato di automazione di Azure non conformi
description: Come riapplicare le configurazioni su richiesta ai server in cui si è verificato lo stato di configurazione
services: automation
ms.service: automation
ms.subservice: dsc
author: mgreenegit
ms.author: migreene
ms.topic: conceptual
ms.date: 07/17/2019
manager: nirb
ms.openlocfilehash: a6c7639cb4988eb13dfaa1c151085cda6e53b5d3
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68614498"
---
# <a name="remediate-non-compliant-dsc-servers"></a>Correggere i server DSC non conformi

Quando i server sono registrati con la configurazione dello stato di automazione di Azure, la modalità' configurazione ' è impostata su ApplyOnly, ApplyandMonitor o ApplyAndAutoCorrect.
Se la modalità non è impostata su AutoCorrect, i server che si spostano da uno stato conforme per qualsiasi motivo rimangono non conformi fino a quando non vengono corretti manualmente.

Calcolo di Azure offre una funzionalità denominata Esegui comando che consente ai clienti di eseguire script all'interno di macchine virtuali.
Questo documento fornisce script di esempio per questa funzionalità quando si corregge manualmente la tendenza alla configurazione.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Spostamento corretto delle macchine virtuali Windows con PowerShell

Per istruzioni dettagliate sull'uso della funzionalità Esegui comando nelle macchine virtuali Windows, vedere la pagina della documentazione [eseguire script di PowerShell nella macchina virtuale Windows con il comando Esegui](/azure/virtual-machines/windows/run-command).

Per forzare un nodo di configurazione dello stato di automazione di Azure a scaricare la configurazione più recente `Update-DscConfiguration` e applicarla, usare il cmdlet.
Per informazioni dettagliate, vedere la documentazione del cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration).

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Spostamento corretto delle macchine virtuali Linux

Funzionalità simili non è attualmente disponibile per i server Linux.
L'unica opzione consiste nel ripetere il processo di registrazione.
Per i nodi di Azure, la correzione della deriva può essere eseguita dal portale o tramite i cmdlet di automazione AZ.
I dettagli di questo processo sono documentati nella pagina onboarding [Computers for Management by Azure Automation state Configuration](/azure/automation/automation-dsc-onboarding#azure-portal).
Per i nodi ibridi, la correzione della deriva può essere eseguita usando gli script Python inclusi.
Vedere la documentazione nel [repository PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Azure Automation State Configuration cmdlets](/powershell/module/azurerm.automation/#automation) (Cmdlet per Configurazione stato di Automazione di Azure)
- Per un esempio dell'uso di Configurazione stato di Automazione di Azure in una pipeline di distribuzione continua, vedere [Continuous Deployment Using Azure Automation State Configuration and Chocolatey](automation-dsc-cd-chocolatey.md) (Distribuzione continua tramite Configurazione stato di Automazione di Azure e Chocolatey)
