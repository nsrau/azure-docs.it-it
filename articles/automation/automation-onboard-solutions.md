---
title: Eseguire l'onboarding delle soluzioni per gli aggiornamenti, il rilevamento delle modifiche e l'inventario in Automazione di Azure
description: Informazioni su come caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure.
services: automation
ms.topic: tutorial
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 721157c333e381799ef08930c667c51a51a4fd6a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457621"
---
# <a name="onboard-update-change-tracking-and-inventory-solutions-to-azure-automation"></a>Eseguire l'onboarding delle soluzioni per gli aggiornamenti, il rilevamento delle modifiche e l'inventario in Automazione di Azure

In questa esercitazione viene illustrato come caricare automaticamente le soluzioni per gli aggiornamenti, il rilevamento modifiche e l'inventario per le VM in Automazione di Azure:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure
> * Abilitare soluzioni
> * Installare e aggiornare i moduli
> * Importare il runbook di caricamento
> * Avviare il runbook

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per aggiornare i moduli dell'account di Automazione alla versione più recente, vedere [Come aggiornare i moduli Azure PowerShell in Automazione di Azure](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario quanto segue:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="onboard-an-azure-vm"></a>Caricare una macchina virtuale di Azure

Esistono diversi modi per eseguire l'onboarding delle macchine virtuali. Ad esempio, è possibile eseguire l'onboarding della soluzione [da una macchina virtuale](automation-onboard-solutions-from-vm.md), [dall'esplorazione di più macchine virtuali](automation-onboard-solutions-from-browse.md), dall'[account di Automazione](automation-onboard-solutions-from-automation-account.md) o tramite runbook. Questa esercitazione descrive come abilitare Gestione aggiornamenti tramite un runbook. Per caricare automaticamente le macchine virtuali di Azure su larga scala, è necessario caricare una macchina virtuale esistente con la soluzione Rilevamento modifiche o Gestione aggiornamenti. In questo passaggio si carica una macchina virtuale con le funzioni di gestione degli aggiornamenti e il rilevamento delle modifiche.

### <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

Le soluzioni Rilevamento modifiche e Inventario consentono di [rilevare le modifiche](automation-vm-change-tracking.md) ed effettuare l'[inventario](automation-vm-inventory.md) nelle macchine virtuali. In questo passaggio verranno abilitate le soluzioni in una macchina virtuale.

1. Nel portale di Azure selezionare **Account di automazione**, quindi selezionare il proprio account di Automazione nell'elenco.
1. Selezionare **Inventario** in **Gestione configurazione**.
1. Selezionare un'area di lavoro Log Analytics esistente oppure crearne una nuova. 
1. Fare clic su **Abilita**.

    ![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions/inventory-onboard.png)

### <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

La soluzione di gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure. È possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale. In questo passaggio si abilita la soluzione per la macchina virtuale.

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** nella sezione **Gestione aggiornamenti**.
1. L'area di lavoro Log Analytics è la stessa usata nel passaggio precedente. Fare clic su **Abilita** per caricare la soluzione di gestione degli aggiornamenti. Durante l'installazione della soluzione di gestione degli aggiornamenti, viene visualizzato un banner blu. 

    ![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions/update-onboard.png)

### <a name="select-azure-vm-to-be-managed"></a>Selezionare la macchina virtuale di Azure da gestire

Ora che le soluzioni sono abilitate, è possibile aggiungere una macchina virtuale di Azure per caricare tali soluzioni.

1. Nell'account di Automazione selezionare **Rilevamento modifiche** in **Gestione configurazione**. 
2. Nella pagina Rilevamento modifiche fare clic su **Aggiungi macchine virtuali di Azure** per aggiungere la macchina virtuale.

3. Selezionare la macchina virtuale dall'elenco e quindi fare clic su **Abilita**. Questa azione abilita le soluzioni Rilevamento modifiche e Inventario per la macchina virtuale.

   ![Abilitare la soluzione di aggiornamento per la macchina virtuale](media/automation-onboard-solutions/enable-change-tracking.png)

4. Quando si riceve la notifica di completamento delll'onboarding della macchina virtuale selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

5. Selezionare **Aggiungi macchine virtuali di Azure** per aggiungere la macchina virtuale.

6. Selezionare la macchina virtuale dall'elenco e quindi selezionare **Abilita**. Questa azione abilita la soluzione Gestione aggiornamenti per la macchina virtuale.

   ![Abilitare la soluzione di aggiornamento per la macchina virtuale](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se non si attende il completamento dell'altra soluzione, quando si abilita la soluzione successiva viene visualizzato il messaggio: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

Per automatizzare l'onboarding della soluzione, è necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare il modulo [Az.OperationalInsights](https://docs.microsoft.com/powershell/module/az.operationalinsights/?view=azps-3.7.0).

## <a name="update-azure-modules"></a>Aggiornare i moduli di Azure

1. Nell'account di Automazione selezionare **Moduli** in **Risorse condivise**. 
2. Selezionare **Aggiorna moduli di Azure** per aggiornare i moduli di Azure alla versione più recente. 
3. Fare clic su **Sì** per aggiornare tutti i moduli di Azure esistenti alla versione più recente.

![Aggiornare i moduli](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azoperationalinsights-module"></a>Installare il modulo Az.OperationalInsights

1. Nell'account di Automazione selezionare **Moduli** in **Risorse condivise**. 
2. Selezionare **Esplora raccolta** per aprire la raccolta di moduli. 
3. Cercare Az.OperationalInsights e importare questo modulo nell'account di Automazione.

![Importare il modulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importare il runbook di caricamento

1. Nell'account di Automazione selezionare **Runbook** in **Automazione processi**.
1. Selezionare **Esplora raccolta**.
1. Cercare `update and change tracking`.
3. Selezionare il runbook e fare clic su **Importa** nella pagina Visualizza origine. 
4. Fare clic su **OK** per importare il runbook nell'account di Automazione.

   ![Importare il runbook di caricamento](media/automation-onboard-solutions/import-from-gallery.png)

6. Nella pagina Runbook fare clic su **Modifica** e quindi selezionare **Pubblica**. 
7. Nel riquadro Pubblica runbook fare clic su **Sì** per pubblicare il runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario avere eseguito l'onboarding delle soluzioni per il rilevamento modifiche o gli aggiornamenti in una macchina virtuale di Azure. Per i parametri sono necessari una macchina virtuale e un gruppo di risorse esistenti con la soluzione caricata.

1. Aprire il runbook **Enable-MultipleSolution**.

   ![Runbook di più soluzioni](media/automation-onboard-solutions/runbook-overview.png)

1. Fare clic sul pulsante Avvia e immettere i valori seguenti per i parametri.

   * **VMNAME** - Lasciare vuoto. Nome di una VM esistente da caricare nella soluzione per il rilevamento modifiche o gli aggiornamenti. Se si lascia vuoto questo campo, tutte le macchine virtuali nel gruppo di risorse vengono caricate.
   * **VMRESOURCEGROUP** - Nome del gruppo di risorse per le macchine virtuali da caricare.
   * **SUBSCRIPTIONID** - Lasciare vuoto. ID di sottoscrizione della nuova macchina virtuale da caricare. Se lasciato vuoto, si usa la sottoscrizione dell'area di lavoro. Quando viene specificato un ID sottoscrizione diverso, l'account RunAs per questo account di Automazione deve essere aggiunto come collaboratore anche per questa sottoscrizione.
   * **ALREADYONBOARDEDVM** - Nome della VM caricata manualmente nella soluzione per gli aggiornamenti o il rilevamento modifiche.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - Nome del gruppo di risorse a cui appartiene la macchina virtuale.
   * **SOLUTIONTYPE** - Immettere **Aggiornamenti** o **Rilevamento modifiche**.

   ![Parametri Enable-MultipleSolution del runbook](media/automation-onboard-solutions/runbook-parameters.png)

1. Selezionare **OK** per avviare il processo di runbook.
1. Monitorare lo stato ed eventuali errori nella pagina del processo del runbook.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere una macchina virtuale per Gestione aggiornamenti:

1. Nell'area di lavoro Log Analytics rimuovere la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates`. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.
2. Rimuovere l'[agente di Log Analytics per Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) o l'[agente di Log Analytics per Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure manualmente.
> * Installare e aggiornare i moduli di Azure necessari.
> * Importare un runbook che carica le macchine virtuali di Azure.
> * Avviare il runbook che carica le macchine virtuali di Azure automaticamente.

Per altre informazioni sulla pianificazione di runbook, fare clic sul collegamento.

> [!div class="nextstepaction"]
> [Pianificazione di runbook](automation-schedules.md).
