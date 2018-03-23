---
title: Caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure
description: Informazioni su come caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
manager: carmonm
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 03/16/2018
ms.custom: mvc
ms.openlocfilehash: cde701672034b3c0edc46f1439488bcf53704df2
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure

In questa esercitazione viene illustrato come caricare automaticamente le soluzioni per gli aggiornamenti, il rilevamento modifiche e l'inventario per le VM in Automazione di Azure:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure
> * Abilitare soluzioni
> * Installare e aggiornare i moduli
> * Importare il runbook di caricamento
> * Avviare il runbook

## <a name="prerequisites"></a>prerequisiti

Per completare l'esercitazione, è necessario quanto segue:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) da caricare.

## <a name="onboard-an-azure-vm"></a>Caricare una macchina virtuale di Azure

Ci sono diversi modi per eseguire l'onboarding, ad esempio [da una macchina virtuale](automation-onboard-solutions-from-vm.md), [dall'account di Automazione](automation-onboard-solutions-from-automation-account.md) o tramite runbook. Questa esercitazione descrive come abilitare Gestione aggiornamenti tramite un runbook. Per caricare automaticamente le macchine virtuali di Azure su larga scala, è necessario caricare una macchina virtuale esistente con la soluzione Rilevamento modifiche o Gestione aggiornamenti. In questo passaggio si carica una macchina virtuale con le funzioni di gestione degli aggiornamenti e il rilevamento delle modifiche.

### <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

La soluzione di rilevamento delle modifiche e di inventario offre la possibilità di [tenere traccia delle modifiche](automation-vm-change-tracking.md) e dei dati di [inventario](automation-vm-inventory.md) nelle macchine virtuali. In questo passaggio si abilita la soluzione su una macchina virtuale.

1. Nel menu a sinistra selezionare **Account di automazione**, quindi selezionare il proprio account di Automazione nell'elenco.
1. Selezionare **Inventario** in **GESTIONE DELLA CONFIGURAZIONE**.
1. Selezionare un'area di lavoro Log Analytics esistente o creare una nuova. Fare clic sul pulsante **Abilita**.

![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions/inventory-onboard.png)

Quando si riceve la notifica di completamento del caricamento della soluzione di rilevamento delle modifiche e di inventario, fare clic su **Gestione aggiornamenti** in **GESTIONE DELLA CONFIGURAZIONE**.

### <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

La soluzione di gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure. È possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale. In questo passaggio si abilita la soluzione per la macchina virtuale.

1. Con il proprio account di Automazione, selezionare **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.
1. L'area di lavoro di Log Analytics è la stessa usata nel passaggio precedente. Fare clic su **Abilita** per caricare la soluzione di gestione degli aggiornamenti.

![Caricare la soluzione per gli aggiornamenti](media/automation-onboard-solutions/update-onboard.png)

Durante l'installazione della soluzione di gestione degli aggiornamenti, viene visualizzato un banner blu. Quando la soluzione è abilitata, selezionare **Rilevamento modifiche** in **GESTIONE CONFIGURAZIONE** per andare al passaggio successivo.

### <a name="select-azure-vm-to-be-managed"></a>Selezionare la macchina virtuale di Azure da gestire

Ora che le soluzioni sono abilitate, è possibile aggiungere una macchina virtuale di Azure per caricare tali soluzioni.

1. Con il proprio account di Automazione, nella pagina **Rilevamento modifiche** selezionare **+ Aggiungi macchina virtuale di Azure** per aggiungere la macchina virtuale desiderata.

1. Selezionare la macchina virtuale dall'elenco e quindi selezionare **Abilita**. Questa azione abilita la soluzione di rilevamento delle modifiche e di inventario per la macchina virtuale.

   ![Abilitare la soluzione di aggiornamento per la macchina virtuale](media/automation-onboard-solutions/enable-change-tracking.png)

1. Quando si riceve la notifica di completamento del caricamento della macchina virtuale, con il proprio account di automazione selezionare **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

1. Selezionare **+ Aggiungi macchina virtuale di Azure** per aggiungere la macchina virtuale.

1. Selezionare la macchina virtuale dall'elenco e quindi selezionare **Abilita**. Questa azione abilita la soluzione di gestione degli aggiornamenti per la macchina virtuale.

   ![Abilitare la soluzione di aggiornamento per la macchina virtuale](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se non si attende il completamento dell'altra soluzione, quando si abilita la soluzione successiva viene visualizzato il messaggio seguente: *L'installazione di un'altra soluzione è già in corso in questa o in un'altra macchina virtuale. Al termine dell'installazione, il pulsante Abilita sarà abilitato e sarà possibile richiedere l'installazione della soluzione in questa macchina virtuale.*

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

È necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare `AzureRM.OperationalInsights` per automatizzare correttamente il caricamento di soluzioni.

## <a name="update-azure-modules"></a>Aggiornare i moduli di Azure

Con il proprio account di Automazione, selezionare **Moduli** in **RISORSE CONDIVISE**. Selezionare **Aggiorna moduli di Azure** per aggiornare i moduli di Azure alla versione più recente. Selezionare **Sì** quando viene richiesto di aggiornare tutti i moduli di Azure esistenti alla versione più recente.

![Aggiornare i moduli](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Installare il modulo AzureRM.OperationalInsights

Nella pagina **Moduli** selezionare **Esplora raccolta** per aprire la raccolta di moduli. Cercare AzureRM.OperationalInsights e importare questo modulo nell'account di Automazione.

![Importare il modulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importare il runbook di caricamento

1. Con il proprio account di Automazione selezionare **Runbook** in **AUTOMAZIONE PROCESSI**.
1. Selezionare **Esplora raccolta**.
1. Cercare **aggiornamento e rilevamento delle modifiche**, fare clic sul runbook e selezionare **Importa** nella pagina **Visualizza origine**. Selezionare **OK** per importare il runbook nell'account di Automazione.

  ![Importare il runbook di caricamento](media/automation-onboard-solutions/import-from-gallery.png)

1. Nella pagina **Runbook** selezionare **Modifica** e quindi selezionare **Pubblica**. Nella finestra di dialogo **Pubblica runbook** selezionare **Sì** per pubblicare il runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario avere caricato la soluzione per il rilevamento modifiche o gli aggiornamenti in una VM di Azure. Per i parametri sono necessari una macchina virtuale e un gruppo di risorse esistenti con la soluzione caricata.

1. Aprire il runbook Enable-MultipleSolution.

   ![Runbook di più soluzioni](media/automation-onboard-solutions/runbook-overview.png)

1. Fare clic sul pulsante Avvia e immettere i valori seguenti per i parametri.

   * **VMNAME** - Lasciare vuoto. Nome di una VM esistente da caricare nella soluzione per il rilevamento modifiche o gli aggiornamenti. Se si lascia vuoto questo campo, tutte le macchine virtuali nel gruppo di risorse vengono caricate.
   * **VMRESOURCEGROUP** - Nome del gruppo di risorse per le macchine virtuali da caricare.
   * **SUBSCRIPTIONID** - Lasciare vuoto. ID di sottoscrizione della nuova macchina virtuale da caricare. Se lasciato vuoto, si usa la sottoscrizione dell'area di lavoro. Quando viene specificato un ID sottoscrizione diverso, l'account RunAs per questo account di Automazione deve essere aggiunto come collaboratore anche per questa sottoscrizione.
   * **ALREADYONBOARDEDVM** - Nome della VM caricata manualmente nella soluzione per gli aggiornamenti o il rilevamento modifiche.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - Nome del gruppo di risorse a cui appartiene la macchina virtuale.
   * **SOLUTIONTYPE** - Immettere **Aggiornamenti** o **Rilevamento modifiche**

   ![Parametri Enable-MultipleSolution del runbook](media/automation-onboard-solutions/runbook-parameters.png)

1. Selezionare **OK** per avviare il processo di runbook.
1. Monitorare lo stato ed eventuali errori nella pagina del processo del runbook.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure manualmente.
> * Installare e aggiornare i moduli di Azure necessari.
> * Importare un runbook che carica le macchine virtuali di Azure.
> * Avviare il runbook che carica le macchine virtuali di Azure automaticamente.

Per altre informazioni sulla pianificazione di runbook, fare clic sul collegamento.

> [!div class="nextstepaction"]
> [Pianificazione di runbook](automation-schedules.md).
