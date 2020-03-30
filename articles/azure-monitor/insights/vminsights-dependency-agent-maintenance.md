---
title: Come aggiornare l'agente di dipendenza di Monitoraggio di Azure per macchine virtualiHow to upgrade the Azure Monitor for VMs Dependency agent
description: Questo articolo descrive come aggiornare l'agente di dipendenza di Monitoraggio di Azure per le macchine virtuali usando la riga di comando, l'installazione guidata e altri metodi.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: c55bee9880c4134f2e304a7fc5176225477fe5f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480760"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Come aggiornare l'agente di dipendenza di Monitoraggio di Azure per macchine virtualiHow to upgrade the Azure Monitor for VMs Dependency agent

Dopo la distribuzione iniziale dell'agente di dipendenza di Monitoraggio di Azure per le macchine virtuali, vengono rilasciati aggiornamenti che includono correzioni di bug o il supporto di nuove funzionalità o funzionalità.  Questo articolo consente di comprendere i metodi disponibili e come eseguire l'aggiornamento manualmente o tramite l'automazione.

## <a name="upgrade-options"></a>Opzioni di aggiornamento 

L'agente di dipendenza per Windows e Linux può essere aggiornato manualmente o automaticamente alla versione più recente a seconda dello scenario di distribuzione e dell'ambiente in cui il computer è in esecuzione. I metodi seguenti possono essere utilizzati per aggiornare l'agente.

|Environment |Metodo di installazione |Metodo di aggiornamento |
|------------|--------------------|---------------|
|Macchina virtuale di Azure | Estensione VM dell'agente di dipendenza per Windows e [LinuxDependency](../../virtual-machines/extensions/agent-dependency-linux.md) agent VM extension for [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) and Linux | L'agente viene aggiornato automaticamente per impostazione predefinita, a meno che il modello di Azure Resource Manager non sia stato configurato per la disattivazione impostando la proprietà *autoUpgradeMinorVersion* su **false**. L'aggiornamento per la versione secondaria in cui l'aggiornamento automatico è disabilitato e un aggiornamento della versione principale segue lo stesso metodo: disinstallare e reinstallare l'estensione. |
| Immagini di macchine virtuali di Azure personalizzateCustom Azure VM images | Installazione manuale dell'agente di dipendenza per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di script di Windows Installer o Linux autoestraente e installabile della shell.|
| Macchine virtuali non Azure | Installazione manuale dell'agente di dipendenza per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di script di Windows Installer o Linux autoestraente e installabile della shell. |

## <a name="upgrade-windows-agent"></a>Aggiornare l'agente Di Windows 

Per aggiornare l'agente in una macchina virtuale Windows alla versione più recente non installata utilizzando l'estensione della macchina virtuale dell'agente di dipendenze, eseguire dal prompt dei comandi, da uno script o da un'altra soluzione di automazione oppure tramite l'Installazione guidata InstallDependencyAgent-Windows.exe.  

È possibile scaricare la versione più recente dell'agente di Windows da [qui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Utilizzo dell'Installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **InstallDependencyAgent-Windows.exe** per avviare l'installazione guidata.
   
3. Seguire l'Installazione guidata **agente di dipendenze** per disinstallare la versione precedente dell'agente di dipendenza e quindi installare la versione più recente.


### <a name="from-the-command-line"></a>Dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire il comando seguente.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Il `/RebootMode=manual` parametro impedisce all'aggiornamento di riavviare automaticamente il computer se alcuni processi utilizzano file della versione precedente e dispongono di un blocco su di essi. 

3. Per verificare che l'aggiornamento sia stato eseguito correttamente, controllare la `install.log` perimpostazione dettagliata delle informazioni di installazione. La directory dei log è *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Aggiornare l'agente LinuxUpgrade Linux agent 

L'aggiornamento da versioni precedenti dell'agente di dipendenza su Linux è supportato ed eseguito seguendo lo stesso comando di una nuova installazione.

È possibile scaricare la versione più recente dell'agente di Windows da [qui](https://aka.ms/dependencyagentlinux).

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire il comando`sh InstallDependencyAgent-Linux64.bin -s`seguente come root . 

Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Negli agenti Linux, la directory di log è */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Passaggi successivi

Se si vuole interrompere il monitoraggio delle macchine virtuali per un periodo di tempo o rimuovere completamente Monitoraggio di Azure per le macchine virtuali, vedere Disabilitare il [monitoraggio delle macchine virtuali in Monitoraggio di Azure per le macchine virtuali.](vminsights-optout.md)
