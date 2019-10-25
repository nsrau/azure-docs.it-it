---
title: Come aggiornare l'agente di dipendenza Monitoraggio di Azure per le macchine virtuali | Microsoft Docs
description: In questo articolo viene descritto come aggiornare il Monitoraggio di Azure per le macchine virtuali Dependency Agent utilizzando la riga di comando, l'installazione guidata e altri metodi.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 37dcd21e17a47827f9bd4f6e5a756b321cf94495
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809838"
---
# <a name="how-to-upgrade-the-azure-monitor-for-vms-dependency-agent"></a>Come aggiornare l'agente di dipendenza Monitoraggio di Azure per le macchine virtuali

Dopo la distribuzione iniziale di Monitoraggio di Azure per le macchine virtuali Dependency Agent, vengono rilasciati aggiornamenti che includono correzioni di bug o il supporto di nuove funzionalità o funzionalità.  Questo articolo consente di comprendere i metodi disponibili e come eseguire l'aggiornamento manualmente o tramite l'automazione.

## <a name="upgrade-options"></a>Opzioni di aggiornamento 

Dependency Agent per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base allo scenario di distribuzione e all'ambiente in cui è in esecuzione il computer. Per aggiornare l'agente, è possibile utilizzare i metodi seguenti.

|Ambiente |Metodo di installazione |Metodo di aggiornamento |
|------------|--------------------|---------------|
|Macchina virtuale di Azure | Estensione della macchina virtuale dell'agente di dipendenza per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Per impostazione predefinita, Agent viene aggiornato automaticamente a meno che non sia stato configurato il modello di Azure Resource Manager per rifiutare esplicitamente l'impostazione della proprietà *autoUpgradeMinorVersion* su **false**. Aggiornamento per la versione secondaria in cui l'aggiornamento automatico è disabilitato e un aggiornamento della versione principale segue lo stesso metodo: disinstallare e reinstallare l'estensione. |
| Immagini personalizzate di VM di Azure | Installazione manuale di Dependency Agent per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di Windows Installer o il bundle di script di Shell autoestraente e installabile di Linux.|
| Macchine virtuali non di Azure | Installazione manuale di Dependency Agent per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di Windows Installer o il bundle di script di Shell autoestraente e installabile di Linux. |

## <a name="upgrade-windows-agent"></a>Aggiornare l'agente Windows 

Per aggiornare l'agente in una macchina virtuale Windows alla versione più recente non installata usando l'estensione della macchina virtuale dell'agente di dipendenza, è possibile eseguire dal prompt dei comandi, dallo script o da un'altra soluzione di automazione oppure mediante l'installazione guidata di Installdependencyagent-Windows. exe.  

È possibile scaricare la versione più recente dell'agente Windows da [qui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Utilizzo dell'installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **installdependencyagent-Windows. exe** per avviare l'installazione guidata di.

3. Nella finestra di dialogo **Dependency Agent installazione di 9.9.1** fare **clic su** Accetto il contratto di licenza.

5. Nella finestra di dialogo **Dependency Agent disinstallazione 9.9.0** fare clic su **Avanti**. Nella pagina stato viene visualizzato lo stato di avanzamento della disinstallazione della versione precedente.

6. Nella finestra di dialogo **Dependency Agent disinstallazione 9.9.0** fare clic su **Disinstalla** per procedere con la disinstallazione della versione precedente dal percorso specificato nella finestra di dialogo. 

7. Nella finestra di dialogo **Dependency Agent 9.9.0 Uninstall** lo stato della disinstallazione viene visualizzato e, al termine, viene visualizzata la pagina **completamento Dependency Agent disinstallazione** . Fare clic su **Finish** (Fine).

8. Nella finestra di dialogo **Dependency Agent installazione 9.9.1** viene visualizzato lo stato di avanzamento dell'installazione. Quando viene visualizzata la pagina **completamento Dependency Agent disinstallazione** , fare clic su **fine**. 

### <a name="from-the-command-line"></a>Dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire il comando indicato di seguito.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Il parametro `/RebootMode=manual` impedisce all'aggiornamento di riavviare automaticamente il computer se alcuni processi utilizzano file della versione precedente e hanno un blocco su di essi. 

3. Per verificare che l'aggiornamento sia stato completato correttamente, controllare la `install.log` per informazioni dettagliate sulla configurazione. La directory dei log è *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Aggiornare l'agente Linux 

L'aggiornamento da versioni precedenti di Dependency Agent in Linux è supportato ed eseguito seguendo lo stesso comando di una nuova installazione.

È possibile scaricare la versione più recente dell'agente Windows da [qui](https://aka.ms/dependencyagentlinux).

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire il comando seguente come`sh InstallDependencyAgent-Linux64.bin -s`radice. 

Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux la directory di log è */var/opt/microsoft/dependency-agent/log*. 

## <a name="next-steps"></a>Passaggi successivi

Se si vuole interrompere il monitoraggio delle macchine virtuali per un periodo di tempo o rimuovere completamente Monitoraggio di Azure per le macchine virtuali, vedere [disabilitare il monitoraggio delle macchine virtuali in monitoraggio di Azure per le macchine virtuali](vminsights-optout.md).