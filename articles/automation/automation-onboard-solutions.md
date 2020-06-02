---
title: Abilitare Gestione aggiornamenti di Automazione di Azure da un runbook
description: Questo articolo illustra come abilitare Gestione aggiornamenti da un runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 39ebdb6937b03d72365e9d3785af9571ebb66186
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836057"
---
# <a name="enable-update-management-from-a-runbook"></a>Abilitare Gestione aggiornamenti da un runbook

Questo articolo descrive come usare un runbook per abilitare la funzionalità [Gestione aggiornamenti](automation-update-management.md) per le VM dell'ambiente. Per abilitare le VM di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Gestione aggiornamenti. 

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, veder [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Selezionare l'area di lavoro Log Analytics, quindi fare clic su **Abilita**. Durante l'abilitazione di Gestione aggiornamenti viene visualizzato un banner blu. 

    ![Abilitare la gestione degli aggiornamenti](media/automation-onboard-solutions/update-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selezionare la macchina virtuale di Azure da gestire

Con la funzionalità Gestione aggiornamenti abilitata, è possibile aggiungere una macchina virtuale di Azure per la ricezione degli aggiornamenti.

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Selezionare **Aggiungi macchine virtuali di Azure** per aggiungere la macchina virtuale.

3. Scegliere la macchina virtuale nell'elenco e fare clic su **Abilita** per configurarla per gli aggiornamenti. 

   ![Abilitare Gestione aggiornamenti per la macchina virtuale](media/automation-onboard-solutions/enable-update.png)

    > [!NOTE]
    > Se si prova ad abilitare un'altra funzionalità prima di completare la configurazione di Gestione aggiornamenti, si riceve questo messaggio: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

Per abilitare correttamente Gestione aggiornamenti per le VM, è necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare il modulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0).

1. Nell'account di Automazione selezionare **Moduli** in **Risorse condivise**. 
2. Selezionare **Aggiorna moduli di Azure** per aggiornare i moduli di Azure alla versione più recente. 
3. Fare clic su **Sì** per aggiornare tutti i moduli di Azure esistenti alla versione più recente.

    ![Aggiornare i moduli](media/automation-onboard-solutions/update-modules.png)

4. Tornare in **Moduli** in **Risorse condivise**. 
5. Selezionare **Esplora raccolta** per aprire la raccolta di moduli. 
6. Cercare `Az.OperationalInsights` e importare questo modulo nell'account di Automazione.

    ![Importare il modulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-update-management"></a>Importare un runbook per abilitare Gestione aggiornamenti

1. Nell'account di Automazione selezionare **Runbook** in **Automazione processi**.
2. Selezionare **Esplora raccolta**.
3. Cercare `update and change tracking`.
4. Selezionare il runbook e fare clic su **Importa** nella pagina Visualizza origine. 
5. Fare clic su **OK** per importare il runbook nell'account di Automazione.

   ![Importare il runbook per la configurazione](media/automation-onboard-solutions/import-from-gallery.png)

6. Nella pagina Runbook fare clic su **Modifica** e quindi selezionare **Pubblica**. 
7. Nel riquadro Pubblica runbook fare clic su **Sì** per pubblicare il runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario aver abilitato Gestione aggiornamenti per una macchina virtuale di Azure. È necessario avere una VM e un gruppo di risorse con la funzionalità abilitata per i parametri.

1. Aprire il runbook **Enable-MultipleSolution**.

   ![Runbook di più soluzioni](media/automation-onboard-solutions/runbook-overview.png)

2. Fare clic sul pulsante Avvia e immettere i valori dei parametri nei campi seguenti:

   * **VMNAME**: il nome di una macchina virtuale esistente da aggiungere a Gestione aggiornamenti. Lasciare vuoto questo campo per aggiungere tutte le macchine virtuali nel gruppo di risorse.
   * **VMRESOURCEGROUP** : il nome del gruppo di risorse per la VM da abilitare.
   * **SUBSCRIPTIONID**: l'ID sottoscrizione della nuova VM da abilitare. Lasciare vuoto questo campo per usare la sottoscrizione dell'area di lavoro. Se si usa un ID sottoscrizione diverso, aggiungere l'account RunAs per l'account di Automazione come collaboratore per la sottoscrizione.
   * **ALREADYONBOARDEDVM**: il nome della VM già abilitata manualmente per gli aggiornamenti.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - Nome del gruppo di risorse a cui appartiene la macchina virtuale.
   * **SOLUTIONTYPE**: immettere **Updates**.

   ![Parametri Enable-MultipleSolution del runbook](media/automation-onboard-solutions/runbook-parameters.png)

3. Selezionare **OK** per avviare il processo di runbook.
4. Monitorare lo stato ed eventuali errori nella pagina del processo del runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per pianificare un runbook, vedere [Gestire le pianificazioni in Automazione di Azure](shared-resources/schedules.md).
* Per usare Gestione aggiornamenti per le VM, vedere [Gestire aggiornamenti e patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per le configurazioni dell'ambito, vedere [Usare configurazioni dell'ambito](automation-scope-configurations-update-management.md).
* Se l'area di lavoro Log Analytics non è più necessaria, vedere le istruzioni riportate in [Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti](automation-unlink-workspace-update-management.md).
* Per eliminare VM da Gestione aggiornamenti, vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](automation-remove-vms-from-update-management.md).
* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi di Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere i problemi relativi all'agente di aggiornamento di Linux, vedere[Risolvere i problemi dell'agente di aggiornamento Linux](troubleshoot/update-agent-issues-linux.md).