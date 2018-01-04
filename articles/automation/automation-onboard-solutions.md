---
title: Caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure | Microsoft Docs
description: Informazioni su come caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure.
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: edae1156-2dc7-4dab-9e5c-bf253d3971d0
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2017
ms.author: eamono
ms.openlocfilehash: 25ad5e04c96aab9dddd39cdb4d33bd8411817e8e
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="onboard-update-and-change-tracking-solutions-to-azure-automation"></a>Caricare le soluzioni per il rilevamento modifiche e gli aggiornamenti in Automazione di Azure

In questa esercitazione viene illustrato come caricare automaticamente le soluzioni per gli aggiornamenti, il rilevamento modifiche e l'inventario per le VM in Automazione di Azure:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure
> * Abilitare soluzioni
> * Installare e aggiornare i moduli
> * Importare il runbook di onboarding
> * Avviare il runbook

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, siano soddisfatti i seguenti:

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Oggetto [macchina virtuale](../virtual-machines/windows/quick-create-portal.md) per l'onboarding.

## <a name="onboard-an-azure-vm"></a>Caricare una macchina virtuale di Azure

Per caricare le macchine virtuali Azure automaticamente, una macchina virtuale esistente deve essere caricato con il rilevamento delle modifiche o di una soluzione di gestione di aggiornamento. In questo passaggio si caricare una macchina virtuale con la gestione degli aggiornamenti e il rilevamento delle modifiche.

### <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e inventario

La soluzione di rilevamento delle modifiche e inventario offre la possibilità di [tenere traccia delle modifiche](automation-vm-change-tracking.md) e [inventario](automation-vm-inventory.md) nelle macchine virtuali. In questo passaggio, si abilita la soluzione in una macchina virtuale.

1. Nel menu a sinistra, selezionare **gli account di automazione**, quindi selezionare l'account di automazione nell'elenco.
1. Selezionare **inventario** in **la gestione della configurazione**.
1. Selezionare un'area di lavoro Log Analytics esistente o creare una nuova. Fare clic su di **abilitare** pulsante.

![Soluzione integrata di aggiornamento](media/automation-onboard-solutions/inventory-onboard.png)

Quando viene completato il rilevamento e l'inventario soluzione onboarding notifica di modifica, fare clic su **gestione aggiornamenti** in **CONFIGURATION MANAGEMENT**.

### <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

La soluzione di gestione degli aggiornamenti consente di gestire gli aggiornamenti e patch per le macchine virtuali Windows Azure. È possibile valutare lo stato degli aggiornamenti disponibili, pianificare l'installazione di aggiornamenti necessari, e controlla i risultati di distribuzione per verificare gli aggiornamenti vengono applicati correttamente alla macchina virtuale. In questo passaggio si abilita la soluzione per la macchina virtuale.

1. Selezionare l'Account di automazione, **la gestione degli aggiornamenti** in **gestione aggiornamenti**.
1. Il Log analitica dell'area di lavoro selezionata è la stessa area di lavoro utilizzata nel passaggio precedente. Fare clic su **abilitare** caricare la soluzione di gestione degli aggiornamenti.

![Soluzione integrata di aggiornamento](media/automation-onboard-solutions/update-onboard.png)

Durante l'installazione della soluzione di gestione di aggiornamento, viene visualizzato un banner di blu. Quando è abilitata la soluzione selezionare **il rilevamento delle modifiche** in **CONFIGURATION MANAGEMENT** per passare al passaggio successivo.

### <a name="select-azure-vm-to-be-managed"></a>Selezionare la macchina virtuale Azure da gestire

Ora che le soluzioni sono abilitate, è possibile aggiungere una macchina virtuale di Azure per caricare tali soluzioni.

1. Dall'Account di automazione, nel **il rilevamento delle modifiche** selezionare **+ Aggiungi una macchina virtuale di Azure** per aggiungere la macchina virtuale.

1. Selezionare la macchina virtuale dall'elenco e selezionare **abilitare**. Questa azione consente la modifica registrazione e la soluzione di inventario per la macchina virtuale.

   ![Abilitare la soluzione di aggiornamento per la macchina virtuale](media/automation-onboard-solutions/enable-change-tracking.png)

1. Quando viene completata la notifica di caricamento della macchina virtuale, selezionare l'Account di automazione **la gestione degli aggiornamenti** in **gestione aggiornamenti**.

1. Selezionare **+ Aggiungi una macchina virtuale di Azure** per aggiungere la macchina virtuale.

1. Selezionare la macchina virtuale dall'elenco e selezionare **abilitare**. Questa azione abilita la soluzione di gestione degli aggiornamenti per la macchina virtuale.

   ![Abilitare la soluzione di aggiornamento per la macchina virtuale](media/automation-onboard-solutions/enable-update.png)

> [!NOTE]
> Se non si attende l'altra soluzione completare, quando si abilita la soluzione successiva viene visualizzato un messaggio che informa: *installazione di un'altra soluzione è in corso su questa o un'altra macchina virtuale. Al termine dell'installazione è abilitato sul pulsante Abilita e si può richiedere l'installazione della soluzione in questa macchina virtuale.*

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

È necessario aggiornare i moduli di Azure più recenti e importare `AzureRM.OperationalInsights` automatizzare correttamente il caricamento di soluzioni.

## <a name="update-azure-modules"></a>Aggiornare i moduli di Azure

Selezionare l'Account di automazione, **moduli** in **risorse CONDIVISE**. Selezionare **aggiornare i moduli di Azure** per aggiornare i moduli di Azure per la versione più recente. Selezionare **Sì** quando viene richiesto di aggiornare tutti i moduli di Azure esistenti per la versione più recente.

![Aggiornare i moduli](media/automation-onboard-solutions/update-modules.png)

### <a name="install-azurermoperationalinsights-module"></a>Installare il modulo AzureRM.OperationalInsights

Dal **moduli** selezionare **Sfoglia raccolta** per aprire la raccolta di moduli. Cercare AzureRM.OperationalInsights e importare questo modulo nell'account di Automazione.

![Importare il modulo OperationalInsights](media/automation-onboard-solutions/import-operational-insights-module.png)

## <a name="import-the-onboarding-runbook"></a>Importare il runbook di onboarding

1. Selezionare l'Account di automazione in **runbook** sotto il **automazione dei processi**.
1. Selezionare **Sfoglia raccolta**.
1. Cercare **aggiornamento e rilevamento delle modifiche**, selezionare il runbook e scegliere **importazione** sul **Visualizza origine** pagina. Selezionare **OK** per importare il runbook in account di automazione.

  ![Importare il runbook di onboarding](media/automation-onboard-solutions/import-from-gallery.png)

1. Nel **Runbook** selezionare **modifica**, quindi selezionare **pubblica**. Nel **Runbook pubblica** finestra di dialogo Seleziona **Sì** a pubblicare il runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario avere caricato la soluzione per il rilevamento modifiche o gli aggiornamenti in una VM di Azure. Per i parametri sono necessari una macchina virtuale e un gruppo di risorse esistenti con la soluzione caricata.

1. Aprire il runbook Enable-MultipleSolution.

   ![Più runbook di soluzione](media/automation-onboard-solutions/runbook-overview.png)

1. Fare clic sul pulsante Avvia e immettere i valori seguenti per i parametri.

   * **VMNAME** -lasciare vuoto. Nome di una VM esistente da caricare nella soluzione per il rilevamento modifiche o gli aggiornamenti. Per lasciare vuoto questo valore, tutte le macchine virtuali nel gruppo di risorse sono state caricate.
   * **VMRESOURCEGROUP** -il nome del gruppo di risorse per le macchine virtuali siano state caricate.
   * **SUBSCRIPTIONID** -lasciare vuoto. L'ID di sottoscrizione della nuova macchina virtuale per essere caricate. Se lasciato vuoto, viene utilizzata la sottoscrizione dell'area di lavoro. Quando viene specificato un ID sottoscrizione diverso, l'account RunAs per questo account di Automazione deve essere aggiunto come collaboratore anche per questa sottoscrizione.
   * **ALREADYONBOARDEDVM** -il nome della macchina virtuale che è stato manualmente caricate per il rilevamento delle modifiche o aggiornamenti soluzione.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** -il nome del gruppo di risorse che la macchina virtuale è membro.
   * **SOLUTIONTYPE** -immettere **aggiornamenti** o **ChangeTracking**

   ![Parametri del runbook Abilita MultipleSolution](media/automation-onboard-solutions/runbook-parameters.png)

1. Selezionare **OK** per avviare il processo del runbook.
1. Monitorare lo stato ed eventuali errori nella pagina del processo del runbook.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Caricare una macchina virtuale di Azure manualmente.
> * Installare e aggiornare i moduli di Azure necessari.
> * Importare un runbook che carica le macchine virtuali di Azure.
> * Avviare il runbook che carica le macchine virtuali di Azure automaticamente.

Fare clic sul collegamento per ulteriori informazioni sulla pianificazione di runbook.

> [!div class="nextstepaction"]
> [Pianificazione runbook](automation-schedules.md).
