---
title: Abilitare Rilevamento modifiche e inventario di Automazione di Azure da un runbook
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da un runbook.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 842b0a92ba4a2cb6b3ceb54675ef95f9c8275311
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210083"
---
# <a name="enable-change-tracking-and-inventory-from-a-runbook"></a>Abilitare Rilevamento modifiche e inventario da un runbook

Questo articolo descrive come usare un Runbook per abilitare [rilevamento modifiche e l'inventario](overview.md) per le macchine virtuali nell'ambiente. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario.

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

Questo metodo usa due manuali operativi:

* **Enable-MultipleSolution** : il Runbook principale che richiede informazioni di configurazione, esegue una query sulla macchina virtuale specificata ed esegue altri controlli di convalida e quindi richiama il Runbook **Enable-AutomationSolution** per configurare rilevamento modifiche e l'inventario per ogni macchina virtuale all'interno del gruppo di risorse specificato.
* **Enable-AutomationSolution** : Abilita rilevamento modifiche e l'inventario per una o più macchine virtuali specificate nel gruppo di risorse di destinazione. Verifica che i prerequisiti siano soddisfatti, verifica che l'estensione della macchina virtuale Log Analytics sia installata e installa se non viene trovata e aggiunge le macchine virtuali alla configurazione dell'ambito nell'area di lavoro Log Analytics specificata collegata all'account di automazione.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../automation-security-overview.md) per gestire i computer.
* [area di lavoro Log Analytics](../../azure-monitor/platform/design-logs-deployment.md)
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).
* Due asset di automazione, usati dal Runbook **Enable-AutomationSolution** . Questo runbook, se non esiste già nell'account di automazione, viene importato automaticamente dal Runbook **Enable-MultipleSolution** durante la prima esecuzione.
    * *LASolutionSubscriptionId*: ID sottoscrizione in cui si trova l'area di lavoro log Analytics.
    * *LASolutionWorkspaceId*: ID area di lavoro dell'area di lavoro log Analytics collegata all'account di automazione.

    Queste variabili vengono usate per configurare l'area di lavoro della VM caricata. Se non vengono specificati, lo script cerca prima di tutto la macchina virtuale caricata in Rilevamento modifiche e inventario nella propria sottoscrizione, seguita dalla sottoscrizione in cui si trova l'account di automazione, seguito da tutte le altre sottoscrizioni a cui l'account utente ha accesso. Se non è configurato correttamente, è possibile che i computer vengano caricati in un'area di lavoro di Log Analytics casuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

1. Nella portale di Azure passare a account di **automazione**. Nella pagina **account di automazione** selezionare l'account dall'elenco.

1. Nell'account di automazione selezionare **Inventory** o **rilevamento modifiche** in **Configuration Management**.

1. Selezionare l'area di lavoro Log Analytics, quindi fare clic su **Abilita**. Durante l'abilitazione dell'inventario o della Rilevamento modifiche, viene visualizzato un banner.

    ![Abilitare il rilevamento delle modifiche e l'inventario](media/enable-from-automation-account/enable-feature.png)

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

È necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare il modulo [AZ. OperationalInsights](/powershell/module/az.operationalinsights) per abilitare correttamente gestione aggiornamenti per le macchine virtuali con il Runbook.

1. Nell'account di Automazione selezionare **Moduli** in **Risorse condivise**.

2. Selezionare **Aggiorna moduli di Azure** per aggiornare i moduli di Azure alla versione più recente.

3. Fare clic su **Sì** per aggiornare tutti i moduli di Azure esistenti alla versione più recente.

    ![Aggiornare i moduli](media/enable-from-runbook/update-modules.png)

4. Tornare in **Moduli** in **Risorse condivise**.

5. Selezionare **Esplora raccolta** per aprire la raccolta di moduli.

6. Cercare `Az.OperationalInsights` e importare questo modulo nell'account di Automazione.

    ![Importare il modulo OperationalInsights](media/enable-from-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Selezionare la macchina virtuale di Azure da gestire

Se Rilevamento modifiche e inventario è abilitata, è possibile aggiungere una macchina virtuale di Azure per gestirla tramite tale funzionalità.

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

2. Fare clic su **Aggiungi macchine virtuali di Azure** per aggiungere la macchina virtuale.

3. Scegliere la macchina virtuale dall'elenco e quindi fare clic su **Abilita**. Questa azione abilita la funzionalità Rilevamento modifiche e inventario per la macchina virtuale.

   ![Abilitare Rilevamento modifiche e inventario per una macchina virtuale](media/enable-from-runbook/enable-azure-vm.png)

    > [!NOTE]
    > Se si prova ad abilitare un'altra funzionalità prima di completare la configurazione di Rilevamento modifiche e inventario, si riceve questo messaggio: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-change-tracking-and-inventory"></a>Importare un runbook per abilitare Rilevamento modifiche e inventario

1. Nell'account di Automazione selezionare **Runbook** in **Automazione processi**.

2. Selezionare **Esplora raccolta**.

3. Ricerca di **aggiornamenti e rilevamento modifiche**.

4. Selezionare il Runbook e fare clic su **Importa** nella pagina **Visualizza origine** .

5. Fare clic su **OK** per importare il runbook nell'account di Automazione.

   ![Importare il runbook per la configurazione](media/enable-from-runbook/import-from-gallery.png)

6. Nella pagina **Runbook** selezionare il Runbook **Enable-MultipleSolution** e quindi fare clic su **modifica**. Nell'editor di testo selezionare  **pubblica**.

7. Quando viene richiesto di confermare, fare clic su **Sì** per pubblicare il Runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario aver abilitato Rilevamento modifiche e inventario per una macchina virtuale di Azure. Per configurare una o più macchine virtuali nel gruppo di risorse di destinazione, è necessaria una macchina virtuale e un gruppo di risorse esistenti con la funzionalità abilitata.

1. Aprire il runbook **Enable-MultipleSolution**.

   ![Runbook di più soluzioni](media/enable-from-runbook/runbook-overview.png)

2. Fare clic sul pulsante Avvia e immettere i valori dei parametri nei campi seguenti:

   * **VMNAME**: nome di una macchina virtuale esistente da aggiungere a Rilevamento modifiche e inventario. Lasciare vuoto questo campo per aggiungere tutte le macchine virtuali nel gruppo di risorse.
   * **VMRESOURCEGROUP** : il nome del gruppo di risorse per la VM da abilitare.
   * **SUBSCRIPTIONID**: l'ID sottoscrizione della nuova VM da abilitare. Lasciare vuoto questo campo per usare la sottoscrizione dell'area di lavoro. Se si usa un ID sottoscrizione diverso, aggiungere l'account RunAs per l'account di Automazione come collaboratore per la sottoscrizione.
   * **ALREADYONBOARDEDVM**: il nome della VM già abilitata manualmente per gli aggiornamenti.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - Nome del gruppo di risorse a cui appartiene la macchina virtuale.
   * **SOLUTIONTYPE**: immettere **ChangeTracking**.

   ![Parametri Enable-MultipleSolution del runbook](media/enable-from-runbook/runbook-parameters.png)

3. Selezionare **OK** per avviare il processo di runbook.

4. Monitorare lo stato del processo del Runbook e gli eventuali errori dalla pagina **processi** .

## <a name="next-steps"></a>Passaggi successivi

* Per pianificare un runbook, vedere [Gestire le pianificazioni in Automazione di Azure](../shared-resources/schedules.md).

* Per informazioni dettagliate sull'uso della funzionalità, vedere [gestire rilevamento modifiche](manage-change-tracking.md) e [gestire l'inventario](manage-inventory-vms.md).

* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](../troubleshoot/change-tracking.md).


