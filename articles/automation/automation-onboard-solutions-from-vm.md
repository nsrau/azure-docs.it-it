---
title: Abilitare Gestione aggiornamenti di Automazione di Azure da una macchina virtuale di Azure
description: Questo articolo illustra come abilitare Gestione aggiornamenti da una macchina virtuale di Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: cab114e50852f293a3d1caf5bdc9a341f75f2557
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743957"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Abilitare Gestione aggiornamenti da una macchina virtuale di Azure

Questo articolo descrive come usare una macchina virtuale di Azure per abilitare la funzionalità [Gestione aggiornamenti](automation-update-management.md) in altri computer. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Gestione aggiornamenti. 

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, veder [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Macchine virtuali** oppure cercare e selezionare **Macchine virtuali** nella pagina Home.

2. Selezionare la macchina virtuale per la quale si vuole abilitare Gestione aggiornamenti. Le macchine virtuali possono esistere in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione. Queste informazioni verranno usate

3. Nella pagina della macchina virtuale, in **Operazioni** selezionare **Gestione aggiornamenti**.

4. È necessario avere l'autorizzazione `Microsoft.OperationalInsights/workspaces/read` per determinare se la macchina virtuale è abilitata per un'area di lavoro. Per informazioni sulle autorizzazioni aggiuntive necessarie, vedere [Autorizzazioni necessarie per abilitare i computer](automation-role-based-access-control.md#feature-setup-permissions). Per informazioni su come abilitare più computer contemporaneamente, vedere [Abilitare Gestione aggiornamenti da un account di Automazione](automation-onboard-solutions-from-automation-account.md).

5. Scegliere l'area di lavoro Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare Gestione aggiornamenti. L'installazione richiede fino a 15 minuti. 

    ![Abilitare la gestione degli aggiornamenti](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Controllare la configurazione dell'ambito

Gestione aggiornamenti usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer nei quali abilitare la funzionalità. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della funzionalità a computer specifici. Per altre informazioni, vedere [Usare configurazioni dell'ambito per Gestione aggiornamenti](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le VM, vedere [Gestire aggiornamenti e patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per le configurazioni dell'ambito, vedere [Usare configurazioni dell'ambito per Gestione aggiornamenti](automation-scope-configurations-update-management.md).
* Se l'area di lavoro Log Analytics non è più necessaria, vedere le istruzioni riportate in [Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti](automation-unlink-workspace-update-management.md).
* Per eliminare VM da Gestione aggiornamenti, vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](automation-remove-vms-from-update-management.md).
* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi di Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere i problemi relativi all'agente di aggiornamento di Linux, vedere[Risolvere i problemi dell'agente di aggiornamento Linux](troubleshoot/update-agent-issues-linux.md).
