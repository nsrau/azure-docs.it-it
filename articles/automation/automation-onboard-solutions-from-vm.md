---
title: Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure.
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: conceptual
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: f270b2ccea51e83bc6475051b8667bf73d7fd717
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36221513"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions-from-an-azure-virtual-machine"></a>Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario da una macchina virtuale di Azure

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding delle macchine virtuali. È possibile eseguire l'onboarding della soluzione da una macchina virtuale, [dall'account di Automazione](automation-onboard-solutions-from-automation-account.md), [dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md) o tramite un [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni da una macchina virtuale di Azure.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-the-solutions"></a>Abilitare le soluzioni

Passare a una macchina virtuale esistente. In **Operazioni** selezionare **Gestione degli aggiornamenti**, **Inventario** o **Rilevamento modifiche**.

Per abilitare la soluzione solo per la macchina virtuale, assicurarsi che sia selezionata l'opzione **Enable for this VM** (Abilita per questa macchina virtuale). Per eseguire l'onboarding di più macchine virtuali nella soluzione, selezionare **Enable for VMs in this subscription** (Abilita per le macchine virtuali in questa sottoscrizione) e quindi selezionare **Click to select machines to enable** (Fare clic per selezionare le macchine virtuali da abilitare). Per informazioni su come eseguire l'onboarding di più macchine virtuali contemporaneamente, vedere [Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario](automation-onboard-solutions-from-automation-account.md).

Selezionare l'area di lavoro di Azure Log Analytics e l'account di Automazione, quindi selezionare **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione Gestione aggiornamenti](media/automation-onboard-solutions-from-vm/onboard-solution.png)

Passare alle altre soluzioni e quindi selezionare **Abilita**. Gli elenchi a discesa per Log Analytics e l'account di Automazione sono disabilitati perché queste soluzioni usano la stessa area di lavoro e lo stesso account di Automazione della soluzione abilitata in precedenza.

> [!NOTE]
> **Rilevamento delle modifiche** e **Inventario** usano la stessa soluzione. Quando una di queste soluzioni viene abilitata, anche l'altra viene abilitata.

## <a name="scope-configuration"></a>Configurazione dell'ambito

Ogni soluzione usa una configurazione dell'ambito nell'area di lavoro per definire i computer di destinazione della soluzione. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della soluzione a computer specifici. Per accedere alle configurazioni dell'ambito, nell'account di Automazione in **RISORSE CORRELATE** selezionare **Area di lavoro**. Nell'area di lavoro in **ORIGINI DATI DELL'AREA DI LAVORO** selezionare **Configurazioni ambito**.

Se l'area di lavoro selezionata non include già le soluzioni Gestione aggiornamenti o Rilevamento modifiche, vengono create le configurazioni di ambito seguenti:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se l'area di lavoro selezionata contiene già la soluzione, la soluzione non viene ridistribuita e la configurazione dell'ambito non viene aggiunta.

Selezionare i puntini di sospensione (**...**) in una delle configurazioni e quindi selezionare **Modifica**. Nel riquadro **Modifica configurazione dell'ambito** selezionare **Selezionare i gruppi di computer**. Il riquadro **Gruppi di computer** mostra le ricerche salvate usate per creare la configurazione dell'ambito.

## <a name="saved-searches"></a>Ricerche salvate

Quando un computer viene aggiunto alle soluzioni Gestione aggiornamenti, Rilevamento modifiche o Inventario, viene aggiunto a una delle due ricerche salvate nell'area di lavoro. Le ricerche salvate sono query che contengono i computer di destinazione per le soluzioni.

Passa all'area di lavoro. In **Generale** selezionare **Ricerche salvate**. Le due ricerche salvate usate da queste soluzioni sono indicate nella tabella seguente:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

Selezionare una delle ricerche salvate per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

![Ricerche salvate](media/automation-onboard-solutions-from-vm/logsearch.png)

## <a name="next-steps"></a>Passaggi successivi

Continuare con le esercitazioni sulle soluzioni per informazioni su come usarle:

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)
* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)
* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)
