---
title: Abilitare Rilevamento modifiche e inventario di Automazione di Azure da un runbook
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da un runbook.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 014442dee1be23a189e22a505abf86050601b2aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826743"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Abilitare Rilevamento modifiche e inventario da un runbook

Questo articolo descrive come usare un runbook per abilitare la funzionalità [Rilevamento modifiche e inventario](change-tracking.md) per le macchine virtuali dell'ambiente. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario. 

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere il [ mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario 

1. Nel portale di Azure selezionare **Account di Automazione** e quindi selezionare il proprio account di Automazione nell'elenco.
1. Selezionare **Inventario** in **Gestione configurazione**.
1. Selezionare un'area di lavoro Log Analytics esistente oppure crearne una nuova. 
1. Fare clic su **Abilita**.

    ![Abilitare il rilevamento delle modifiche e l'inventario](media/automation-enable-changes-from-runbook/inventory-onboard.png)

## <a name="select-azure-vm-to-manage"></a>Selezionare la macchina virtuale di Azure da gestire

Se Rilevamento modifiche e inventario è abilitata, è possibile aggiungere una macchina virtuale di Azure per gestirla tramite tale funzionalità.

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

2. Fare clic su **Aggiungi macchine virtuali di Azure** per aggiungere la macchina virtuale.

3. Scegliere la macchina virtuale dall'elenco e quindi fare clic su **Abilita**. Questa azione abilita la funzionalità Rilevamento modifiche e inventario per la macchina virtuale.

   ![Abilitare Rilevamento modifiche e inventario per una macchina virtuale](media/automation-enable-changes-from-runbook/enable-change-tracking.png)

    > [!NOTE]
    > Se si prova ad abilitare un'altra funzionalità prima di completare la configurazione di Rilevamento modifiche e inventario, si riceve questo messaggio: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

Per abilitare correttamente Rilevamento modifiche e inventario per la macchina virtuale, è necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare il modulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0).

1. Nell'account di Automazione selezionare **Moduli** in **Risorse condivise**. 
2. Selezionare **Aggiorna moduli di Azure** per aggiornare i moduli di Azure alla versione più recente. 
3. Fare clic su **Sì** per aggiornare tutti i moduli di Azure esistenti alla versione più recente.

    ![Aggiornare i moduli](media/automation-enable-changes-from-runbook/update-modules.png)

4. Tornare in **Moduli** in **Risorse condivise**. 
5. Selezionare **Esplora raccolta** per aprire la raccolta di moduli. 
6. Cercare Az.OperationalInsights e importare questo modulo nell'account di Automazione.

    ![Importare il modulo OperationalInsights](media/automation-enable-changes-from-runbook/import-operational-insights-module.png)

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importare un runbook per abilitare Rilevamento modifiche e inventario

1. Nell'account di Automazione selezionare **Runbook** in **Automazione processi**.
2. Selezionare **Esplora raccolta**.
3. Cercare `update and change tracking`.
4. Selezionare il runbook e fare clic su **Importa** nella pagina Visualizza origine. 
5. Fare clic su **OK** per importare il runbook nell'account di Automazione.

   ![Importare il runbook per la configurazione](media/automation-enable-changes-from-runbook/import-from-gallery.png)

6. Nella pagina Runbook fare clic su **Modifica** e quindi selezionare **Pubblica**. 
7. Nel riquadro Pubblica runbook fare clic su **Sì** per pubblicare il runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario aver abilitato Rilevamento modifiche e inventario per una macchina virtuale di Azure. È necessario avere una macchina virtuale e un gruppo di risorse con la funzionalità abilitata per i parametri.

1. Aprire il runbook **Enable-MultipleSolution**.

   ![Runbook di più soluzioni](media/automation-enable-changes-from-runbook/runbook-overview.png)

1. Fare clic sul pulsante Avvia e immettere i valori dei parametri nei campi seguenti:

   * **VMNAME**: nome di una macchina virtuale esistente da aggiungere a Rilevamento modifiche e inventario. Lasciare vuoto questo campo per aggiungere tutte le macchine virtuali nel gruppo di risorse.
   * **VMRESOURCEGROUP**: nome del gruppo di risorse per la macchina virtuale da abilitare.
   * **SUBSCRIPTIONID**: ID sottoscrizione della nuova macchina virtuale da abilitare. Lasciare vuoto questo campo per usare la sottoscrizione dell'area di lavoro. Se si usa un ID sottoscrizione diverso, aggiungere l'account RunAs per l'account di Automazione come collaboratore per la sottoscrizione.
   * **ALREADYONBOARDEDVM**: nome della macchina virtuale già abilitata manualmente per le modifiche.
   * **ALREADYONBOARDEDVMRESOURCEGROUP**: nome del gruppo di risorse a cui appartiene la macchina virtuale.
   * **SOLUTIONTYPE**: immettere **ChangeTracking**.

   ![Parametri Enable-MultipleSolution del runbook](media/automation-enable-changes-from-runbook/runbook-parameters.png)

1. Selezionare **OK** per avviare il processo di runbook.
1. Monitorare lo stato ed eventuali errori nella pagina del processo del runbook.

## <a name="next-steps"></a>Passaggi successivi

* Per pianificare un runbook, vedere [Gestire le pianificazioni in Automazione di Azure](shared-resources/schedules.md).
* Per informazioni dettagliate sull'uso della funzionalità, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per informazioni sulle configurazioni dell'ambito, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).
* Per informazioni su come usare la funzionalità per identificare il software installato nell'ambiente, vedere [Individuare il software installato nelle VM](automation-tutorial-installed-software.md).
* Se non si vuole integrare l'account di Automazione con un'area di lavoro Log Analytics quando si abilita la funzionalità, vedere [Scollegare l'area di lavoro dall'account di Automazione](automation-unlink-workspace-change-tracking.md).
* Al termine della distribuzione delle modifiche nelle macchine virtuali, è possibile rimuoverle come descritto in [Rimuovere macchine virtuali da Rilevamento modifiche e inventario](automation-remove-vms-from-change-tracking.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).