---
title: Abilitare Gestione aggiornamenti di Automazione di Azure da un runbook
description: Questo articolo illustra come abilitare Gestione aggiornamenti da un runbook.
services: automation
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: mvc
ms.openlocfilehash: 870345d244a7f23011c900154e1c486e039a6d8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91854815"
---
# <a name="enable-update-management-from-a-runbook"></a>Abilitare Gestione aggiornamenti da un runbook

Questo articolo descrive come usare un runbook per abilitare la funzionalità [Gestione aggiornamenti](update-mgmt-overview.md) per le VM dell'ambiente. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente con Gestione aggiornamenti.

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

Questo metodo usa due manuali operativi:

* **Enable-MultipleSolution** : il Runbook primario che richiede informazioni di configurazione, esegue una query sulla macchina virtuale specificata ed esegue altri controlli di convalida e quindi richiama il Runbook **Enable-AutomationSolution** per configurare Gestione aggiornamenti per ogni macchina virtuale all'interno del gruppo di risorse specificato.
* **Enable-AutomationSolution** -Abilita gestione aggiornamenti per una o più macchine virtuali specificate nel gruppo di risorse di destinazione. Verifica che i prerequisiti siano soddisfatti, verifica che l'estensione della macchina virtuale Log Analytics sia installata e installa se non viene trovata e aggiunge le macchine virtuali alla configurazione dell'ambito nell'area di lavoro Log Analytics specificata collegata all'account di automazione.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../automation-security-overview.md) per gestire i computer.
* [area di lavoro Log Analytics](../../azure-monitor/platform/design-logs-deployment.md)
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).
* Due asset di automazione, usati dal Runbook **Enable-AutomationSolution** . Questo runbook, se non esiste già nell'account di automazione, viene importato automaticamente dal Runbook **Enable-MultipleSolution** durante la prima esecuzione.
    * *LASolutionSubscriptionId*: ID sottoscrizione in cui si trova l'area di lavoro log Analytics.
    * *LASolutionWorkspaceId*: ID area di lavoro dell'area di lavoro log Analytics collegata all'account di automazione.

    Queste variabili vengono usate per configurare l'area di lavoro della VM caricata. Se questi non vengono specificati, lo script cerca innanzitutto una macchina virtuale caricata in Gestione aggiornamenti nella propria sottoscrizione, seguita dalla sottoscrizione in cui si trova l'account di automazione, seguito da tutte le altre sottoscrizioni a cui l'account utente ha accesso. Se non è configurato correttamente, è possibile che i computer vengano caricati in un'area di lavoro di Log Analytics casuale.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

1. Nella portale di Azure passare a account di **automazione**. Nella pagina **account di automazione** selezionare l'account dall'elenco.

2. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

3. Selezionare l'area di lavoro Log Analytics, quindi fare clic su **Abilita**. Quando Gestione aggiornamenti viene abilitato, viene visualizzato un banner.

    ![Abilitare la gestione degli aggiornamenti](media/update-mgmt-enable-runbook/enable-update-management.png)

## <a name="install-and-update-modules"></a>Installare e aggiornare i moduli

È necessario eseguire l'aggiornamento ai moduli di Azure più recenti e importare il modulo [AZ. OperationalInsights](/powershell/module/az.operationalinsights) per abilitare correttamente gestione aggiornamenti per le macchine virtuali con il Runbook.

1. Nell'account di Automazione selezionare **Moduli** in **Risorse condivise**.

2. Selezionare **Aggiorna moduli di Azure** per aggiornare i moduli di Azure alla versione più recente.

3. Fare clic su **Sì** per aggiornare tutti i moduli di Azure esistenti alla versione più recente.

    ![Aggiornare i moduli](media/update-mgmt-enable-runbook/update-modules.png)

4. Tornare in **Moduli** in **Risorse condivise**.

5. Selezionare **Esplora raccolta** per aprire la raccolta di moduli.

6. Cercare `Az.OperationalInsights` e importare questo modulo nell'account di Automazione.

    ![Importare il modulo OperationalInsights](media/update-mgmt-enable-runbook/import-operational-insights-module.png)

## <a name="select-azure-vm-to-manage"></a>Selezionare la macchina virtuale di Azure da gestire

Con la funzionalità Gestione aggiornamenti abilitata, è possibile aggiungere una macchina virtuale di Azure per la ricezione degli aggiornamenti.

1. Dall'account di automazione selezionare **Gestione aggiornamenti** nella sezione **Gestione aggiornamenti**.

2. Selezionare **Aggiungi macchine virtuali di Azure** per aggiungere la macchina virtuale.

3. Scegliere la macchina virtuale dall'elenco e fare clic su **Abilita** per configurare la macchina virtuale per la gestione.

   ![Abilitare Gestione aggiornamenti per la macchina virtuale](media/update-mgmt-enable-runbook/enable-update-management-vm.png)

    > [!NOTE]
    > Se si prova ad abilitare un'altra funzionalità prima di completare la configurazione di Gestione aggiornamenti, si riceve questo messaggio: `Installation of another solution is in progress on this or a different virtual machine. When that installation completes the Enable button is enabled, and you can request installation of the solution on this virtual machine.`

## <a name="import-a-runbook-to-enable-update-management"></a>Importare un runbook per abilitare Gestione aggiornamenti

1. Nell'account di Automazione selezionare **Runbook** in **Automazione processi**.

2. Selezionare **Esplora raccolta**.

3. Ricerca di **aggiornamenti e rilevamento modifiche**.

4. Selezionare il Runbook e fare clic su **Importa** nella pagina **Visualizza origine** .

5. Fare clic su **OK** per importare il runbook nell'account di Automazione.

   ![Importare il runbook per la configurazione](media/update-mgmt-enable-runbook/import-from-gallery.png)

6. Nella pagina **Runbook** selezionare il Runbook **Enable-MultipleSolution** e quindi fare clic su **modifica**. Nell'editor di testo selezionare  **pubblica**.

7. Quando viene richiesto di confermare, fare clic su **Sì** per pubblicare il Runbook.

## <a name="start-the-runbook"></a>Avviare il runbook

Per avviare questo runbook, è necessario aver abilitato Gestione aggiornamenti per una macchina virtuale di Azure. Per configurare una o più macchine virtuali nel gruppo di risorse di destinazione, è necessaria una macchina virtuale e un gruppo di risorse esistenti con la funzionalità abilitata.

1. Aprire il runbook **Enable-MultipleSolution**.

   ![Runbook di più soluzioni](media/update-mgmt-enable-runbook/runbook-overview.png)

2. Fare clic sul pulsante Avvia e immettere i valori dei parametri nei campi seguenti:

   * **VMNAME**: il nome di una macchina virtuale esistente da aggiungere a Gestione aggiornamenti. Lasciare vuoto questo campo per aggiungere tutte le macchine virtuali nel gruppo di risorse.
   * **VMRESOURCEGROUP** : il nome del gruppo di risorse per la VM da abilitare.
   * **SUBSCRIPTIONID**: l'ID sottoscrizione della nuova VM da abilitare. Lasciare vuoto questo campo per usare la sottoscrizione dell'area di lavoro. Se si usa un ID sottoscrizione diverso, aggiungere l'account RunAs per l'account di Automazione come collaboratore per la sottoscrizione.
   * **ALREADYONBOARDEDVM**: il nome della VM già abilitata manualmente per gli aggiornamenti.
   * **ALREADYONBOARDEDVMRESOURCEGROUP** - Nome del gruppo di risorse a cui appartiene la macchina virtuale.
   * **SOLUTIONTYPE**: immettere **Updates**.

   ![Parametri Enable-MultipleSolution del runbook](media/update-mgmt-enable-runbook/runbook-parameters.png)

3. Selezionare **OK** per avviare il processo di runbook.

4. Monitorare lo stato del processo del Runbook e gli eventuali errori dalla pagina **processi** .

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le macchine virtuali, vedere [gestire gli aggiornamenti e le patch per le macchine virtuali](update-mgmt-manage-updates-for-vm.md).

* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](../troubleshoot/update-management.md).
