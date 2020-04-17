---
title: Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: f33f829b6cb86cb01c848e5fc48e1618a3e00a2c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537033"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding delle macchine virtuali. È possibile eseguire l'onboarding della soluzione da una macchina virtuale, [dall'account di Automazione](automation-onboard-solutions-from-automation-account.md), [dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md) o tramite un [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni da una macchina virtuale di Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-the-solutions"></a>Abilitare le soluzioni

In primo luogo, abilitare una o tutte e tre le soluzioni nella macchina virtuale:First, enable one or all three of the solutions on your VM:

1. Nel [portale di Azure](https://portal.azure.com)selezionare **Macchine virtuali** oppure cercare e selezionare **Macchine virtuali** nella home page.
2. Selezionare la macchina virtuale per cui si vuole abilitare una soluzione.
3. Nella pagina VM, in **Operazioni**, selezionare **Gestione aggiornamenti,** **Inventario**o **Rilevamento modifiche**. La macchina virtuale può esistere in qualsiasi area, indipendentemente dal percorso dell'account di automazione. Quando si esegue l'onboarding di una `Microsoft.OperationalInsights/workspaces/read` soluzione da una macchina virtuale, è necessario disporre dell'autorizzazione per determinare se la macchina virtuale è in boarded in un'area di lavoro. Per informazioni sulle autorizzazioni aggiuntive necessarie, vedere [Autorizzazioni necessarie per l'onboarding](automation-role-based-access-control.md#onboarding-permissions)dei computer . Per informazioni su come eseguire l'onboarding di più macchine virtuali contemporaneamente, vedere [Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario](automation-onboard-solutions-from-automation-account.md).

4. Selezionare l'area di lavoro Azure Log Analytics e l'account di automazione e quindi fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione Gestione aggiornamenti](media/automation-tutorial-update-management/manageupdates-update-enable.png)

5. Passare alle altre soluzioni e quindi selezionare **Abilita**. Gli elenchi a discesa dell'area di lavoro di Log Analytics e dell'account di automazione sono disabilitati perché queste soluzioni usano lo stesso'area di lavoro e lo stesso account di automazione della soluzione abilitata in precedenza.

> [!NOTE]
> Rilevamento delle modifiche e Inventario usano la stessa soluzione. Quando una di queste soluzioni viene abilitata, anche l'altra viene abilitata.

## <a name="scope-configuration"></a>Configurazione dell'ambito

Ogni soluzione usa una configurazione dell'ambito nell'area di lavoro per definire i computer di destinazione della soluzione. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della soluzione a computer specifici. Per accedere alle configurazioni dell'ambito:

1. Nell'account di automazione, in **Risorse correlate,** selezionare **Area di lavoro**. 
2. Nell'area di lavoro, in **Origini dati area di lavoro,** selezionare **Configurazioni ambito.**
3. Se l'area di lavoro selezionata non dispone già della soluzione Gestione aggiornamenti o Rilevamento modifiche, vengono create le configurazioni di ambito seguenti:If the selected workspace doesn't already have the Update Management or Change Tracking solution, the following scope configurations are created:

    * `MicrosoftDefaultScopeConfig-ChangeTracking`
    * `MicrosoftDefaultScopeConfig-Updates`

    Se l'area di lavoro selezionata contiene già la soluzione, la soluzione non viene ridistribuita e la configurazione dell'ambito non viene aggiunta.

4. Selezionare i puntini di sospensione (**...**) in una delle configurazioni e quindi fare clic su **Modifica**. 
5. Nel riquadro **Modifica configurazione dell'ambito** selezionare **Selezionare i gruppi di computer**. Il riquadro **Gruppi di computer** mostra le ricerche salvate usate per creare la configurazione dell'ambito.

## <a name="saved-searches"></a>Ricerche salvate

Quando un computer viene aggiunto alla soluzione Gestione aggiornamenti, Rilevamento modifiche o Inventario, il computer viene aggiunto a una delle due ricerche salvate nell'area di lavoro. Le ricerche salvate sono query che contengono i computer di destinazione per le soluzioni.

Passa all'area di lavoro. In **Generale** selezionare **Ricerche salvate**. Le due ricerche salvate usate da queste soluzioni sono indicate nella tabella seguente:

|Nome     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

Selezionare una delle ricerche salvate per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

![Ricerche salvate](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Le soluzioni seguenti sono dipendenti da un'area di lavoro Log Analytics:

* [Gestione degli aggiornamenti](automation-update-management.md)
* [Rilevamento modifiche](automation-change-tracking.md)
* [Avviare/arrestare le macchine virtuali durante le ore non lavorativeStart/Stop VMs during off-hours](automation-solution-vm-management.md)

Se si decide di non integrare più l'account di Automazione con un'area di lavoro di Log Analytics, è possibile scollegare l'account direttamente dal portale di Azure.If you decide you no longer wish to integrate your Automation account with a Log Analytics workspace, you can unlink your account directly from the Azure portal.  Prima di procedere, è necessario rimuovere le soluzioni menzionate in precedenza; in caso contrario non sarà possibile continuare con il processo. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di automazione e selezionare **Area di lavoro collegata** nella sezione **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Verrà richiesto di confermare l'operazione.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Pianificazioni di aggiornamento: ognuna di esse avrà nomi corrispondenti alle distribuzioni di aggiornamento create.

* Gruppi di lavoro ibridi creati per la soluzione: a ognuno verrà assegnato un nome simile a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8).

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* variables

In alternativa, è anche possibile scollegare l'area di lavoro dall'account di automazione dall'area di lavoro di Log Analytics.Alternative ly can also unlink your workspace from your Automation Account from your Log Analytics workspace. Nell'area di lavoro selezionare **Account di automazione** in **Risorse correlate**. Nella pagina Account di automazione selezionare **Scollega account**.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere una macchina virtuale per Gestione aggiornamenti:

* Nell'area di lavoro Log Analytics rimuovere la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates`. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.
* Rimuovere [l'agente di Log Analytics per Windows](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) o l'agente di Log Analytics per [Linux.](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

Continuare con le esercitazioni sulle soluzioni per informazioni su come usarle:

* [Esercitazione - Gestire gli aggiornamenti per la macchina virtualeTutorial - Manage updates for your VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)
