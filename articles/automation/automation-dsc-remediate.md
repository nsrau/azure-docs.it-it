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
ms.openlocfilehash: f871b406793e455c857ca14c83434c9ed3e004df
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993849"
---
# <a name="remediate-noncompliant-dsc-servers"></a>Correggere i server DSC non conformi

Quando i server sono registrati con la configurazione dello stato di automazione di Azure, la `ApplyOnly`modalità `ApplyandMonitor`di configurazione `ApplyAndAutoCorrect`è impostata su, o. Se la modalità non è impostata `ApplyAndAutoCorrect`su, i server che passano da uno stato conforme per qualsiasi motivo rimangono non conformi fino a quando non vengono corretti manualmente.

Calcolo di Azure offre una funzionalità denominata Esegui comando che consente ai clienti di eseguire script all'interno di macchine virtuali.
Questo documento fornisce script di esempio per questa funzionalità quando si corregge manualmente la tendenza alla configurazione.

## <a name="correct-drift-of-windows-virtual-machines-using-powershell"></a>Spostamento corretto delle macchine virtuali Windows con PowerShell

Per istruzioni dettagliate sull'uso della funzionalità Esegui comando nelle macchine virtuali Windows, vedere la pagina della documentazione [eseguire script di PowerShell nella macchina virtuale Windows con il comando Esegui](/azure/virtual-machines/windows/run-command).

Per forzare un nodo di configurazione dello stato di automazione di Azure a scaricare la configurazione più recente e applicarla, usare il cmdlet [Update-DscConfiguration](/powershell/module/psdesiredstateconfiguration/update-dscconfiguration) .

```powershell
Update-DscConfiguration -Wait -Verbose
```

## <a name="correct-drift-of-linux-virtual-machines"></a>Spostamento corretto delle macchine virtuali Linux

Funzionalità simili non è attualmente disponibile per i server Linux.
L'unica opzione consiste nel ripetere il processo di registrazione.
Per i nodi di Azure, è possibile correggere la deriva dalla portale di Azure o usando i cmdlet AZ Module. I dettagli di questo processo sono documentati in caricamento dei [computer per la gestione tramite la configurazione dello stato di automazione di Azure](automation-dsc-onboarding.md#enable-a-vm-using-azure-portal).
Per i nodi ibridi è possibile correggere la tendenza usando gli script Python inclusi.
Vedere il [repository PowerShell DSC per Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux#performing-dsc-operations-from-the-linux-computer).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni di riferimento sui cmdlet di PowerShell, vedere [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Per un esempio di come usare la configurazione dello stato di automazione di Azure in una pipeline di distribuzione continua, vedere [distribuzione continua con la configurazione dello stato di automazione di Azure e cioccolato](automation-dsc-cd-chocolatey.md).