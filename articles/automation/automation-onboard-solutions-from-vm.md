---
title: Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 621b429f5dc3a6b6620e4d41ad46763e1d4fa226
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299528"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding delle macchine virtuali. È possibile eseguire l'onboarding della soluzione da una macchina virtuale, [dall'account di Automazione](automation-onboard-solutions-from-automation-account.md), [dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md) o tramite un [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni da una macchina virtuale di Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-the-solutions"></a>Abilitare le soluzioni

Prima di tutto, abilitare una o tutte e tre le soluzioni nella macchina virtuale:

1. Nel [portale di Azure](https://portal.azure.com), nel riquadro a sinistra selezionare **macchine virtuali** o cercare e selezionare **macchine virtuali** dalla **Home** page.
2. Selezionare la macchina virtuale per cui si vuole abilitare una soluzione.
3. Nella pagina VM, in **operazioni**, selezionare **Gestione aggiornamenti**, **inventario**o **rilevamento modifiche**. La macchina virtuale può esistere in qualsiasi area indipendentemente dalla posizione dell'account di automazione. Quando si carica una soluzione da una macchina virtuale, è necessario avere l'autorizzazione `Microsoft.OperationalInsights/workspaces/read` per determinare se la macchina virtuale è caricata in un'area di lavoro. Per informazioni sulle autorizzazioni aggiuntive necessarie, vedere [autorizzazioni necessarie per l'onboarding dei computer](automation-role-based-access-control.md#onboarding).

Per informazioni su come eseguire l'onboarding di più macchine virtuali contemporaneamente, vedere [Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario](automation-onboard-solutions-from-automation-account.md).

Selezionare l'area di lavoro Azure Log Analytics e l'account di Automazione, quindi selezionare **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione Gestione aggiornamenti](media/automation-tutorial-update-management/manageupdates-update-enable.png)

Passare alle altre soluzioni e quindi selezionare **Abilita**. Gli elenchi a discesa Log Analytics area di lavoro e account di automazione sono disabilitati perché queste soluzioni usano la stessa area di lavoro e l'account di automazione della soluzione precedentemente abilitata.

> [!NOTE]
> **Rilevamento delle modifiche** e **Inventario** usano la stessa soluzione. Quando una di queste soluzioni viene abilitata, anche l'altra viene abilitata.

## <a name="scope-configuration"></a>Configurazione dell'ambito

Ogni soluzione usa una configurazione dell'ambito nell'area di lavoro per definire i computer di destinazione della soluzione. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della soluzione a computer specifici. Per accedere alle configurazioni dell'ambito, nell'account di automazione in **risorse correlate**selezionare **area di lavoro**. Nell'area di lavoro in **origini dati dell'area di lavoro**selezionare **configurazioni ambito**.

Se l'area di lavoro selezionata non include già le soluzioni Gestione aggiornamenti o Rilevamento modifiche, vengono create le configurazioni di ambito seguenti:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se l'area di lavoro selezionata contiene già la soluzione, la soluzione non viene ridistribuita e la configurazione dell'ambito non viene aggiunta.

Selezionare i puntini di sospensione ( **...** ) in una delle configurazioni e quindi selezionare **Modifica**. Nel riquadro **Modifica configurazione dell'ambito** selezionare **Selezionare i gruppi di computer**. Il riquadro **Gruppi di computer** mostra le ricerche salvate usate per creare la configurazione dell'ambito.

## <a name="saved-searches"></a>Ricerche salvate

Quando un computer viene aggiunto alle soluzioni Gestione aggiornamenti, Rilevamento modifiche o Inventario, viene aggiunto a una delle due ricerche salvate nell'area di lavoro. Le ricerche salvate sono query che contengono i computer di destinazione per le soluzioni.

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
* [Rilevamento delle modifiche](automation-change-tracking.md)
* [Avviare/arrestare le VM durante gli orari di minore attività](automation-solution-vm-management.md)

Se si decide di non voler più integrare l'account di automazione con un'area di lavoro di Log Analytics, è possibile scollegare l'account direttamente dall'portale di Azure.  Prima di procedere, è necessario rimuovere le soluzioni menzionate in precedenza; in caso contrario non sarà possibile continuare con il processo. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione e nella pagina Account di automazione selezionare **Area di lavoro collegata** nella sezione **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)** .

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-onboard-solutions-from-vm/automation-unlink-workspace-blade.png).

   Verrà richiesto di confermare l'operazione.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Pianificazioni degli aggiornamenti: ciascuna di esse avrà nomi corrispondenti alle distribuzioni di aggiornamenti create.

* Gruppi di lavoro ibridi creati per la soluzione. ognuno verrà denominato in modo analogo a machine1. contoso. com_9ceb8108-26C9-4051-B6B3-227600d715c8).

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* variables

In alternativa, è anche possibile scollegare l'area di lavoro dall'account di automazione dall'area di lavoro Log Analytics. Nell'area di lavoro selezionare **account di automazione** in **risorse correlate**. Nella pagina account di automazione selezionare **Scollega account**.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere una macchina virtuale per Gestione aggiornamenti:

* Nell'area di lavoro Log Analytics rimuovere la macchina virtuale dalla ricerca salvata per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates`. Le ricerche salvate sono disponibili in **Generale** nell'area di lavoro.
* Rimuovere [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) o l'[agente di Log Analytics per Linux](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

Continuare con le esercitazioni sulle soluzioni per informazioni su come usarle:

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)
