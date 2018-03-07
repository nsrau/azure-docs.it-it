---
title: Informazioni su come eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario in Automazione di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure
services: automation
keywords: 
author: georgewallace
ms.author: gwallace
ms.date: 02/28/2018
ms.topic: article
ms.service: automation
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 670a0c2a11ebfe09406233ab4b454b2e9c2ba0e0
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding, ad esempio [da una macchina virtuale](automation-onboard-solutions-from-vm.md), dall'account di Automazione o tramite [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni dall'account di Automazione.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com

## <a name="enable-solutions"></a>Abilitare soluzioni

Passare all'account di Automazione e selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**.

Scegliere l'area di lavoro di Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione Inventario](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

La soluzione di rilevamento delle modifiche e di inventario offre la possibilità di [tenere traccia delle modifiche](automation-vm-change-tracking.md) e dei dati di [inventario](automation-vm-inventory.md) nelle macchine virtuali. In questo passaggio si abilita la soluzione su una macchina virtuale.

Quando si riceve la notifica di completamento del caricamento della soluzione di rilevamento delle modifiche e di inventario, fare clic su **Gestione aggiornamenti** in **GESTIONE DELLA CONFIGURAZIONE**.

La soluzione di gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure. È possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale. Questa azione ha abilitato la soluzione per la macchina virtuale.

Selezionare **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**. L'area di lavoro di Log Analytics è la stessa usata nel passaggio precedente. Fare clic su **Abilita** per caricare la soluzione di gestione degli aggiornamenti. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione per gli aggiornamenti](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configurazione dell'ambito

Ogni soluzione usa una configurazione dell'ambito nell'area di lavoro per definire i computer di destinazione della soluzione. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della soluzione a computer specifici. Per accedere alle configurazioni dell'ambito, nell'account di Automazione in **RISORSE CORRELATE** selezionare **Area di lavoro**. Quindi nell'area di lavoro in **ORIGINI DATI DELL'AREA DI LAVORO** selezionare **Configurazioni ambito**.

Le due configurazioni dell'ambito create per impostazione predefinita sono **MicrosoftDefaultScopeConfig-ChangeTracking** e **MicrosoftDefaultScopeConfig-Updates**.

## <a name="saved-searches"></a>Ricerche salvate

Quando un computer viene aggiunto alle soluzioni Gestione aggiornamenti o Rilevamento modifiche e Inventario, viene aggiunto a una delle due ricerche salvate nell'area di lavoro. Queste ricerche salvate sono query che contengono i computer di destinazione per le soluzioni.

Passare all'account di Automazione e selezionare **Ricerche salvate** in **Generale**. Le due ricerche salvate usate da queste soluzioni sono indicate nella tabella seguente:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

Selezionare una ricerca salvata per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

![Ricerche salvate](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-an-azure-machine"></a>Eseguire l'onboarding di un computer di Azure

Dall'account di Automazione selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** oppure **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

Fare clic su **+ Aggiungi macchina virtuale di Azure** e selezionare una macchina virtuale dall'elenco. Nella pagina **Gestione aggiornamenti** fare clic su **Abilita**. La macchina virtuale corrente viene aggiunta alla ricerca salvata del gruppo di computer per la soluzione.

## <a name="onboard-a-non-azure-machine"></a>Eseguire l'onboarding di un computer non di Azure

Dall'account di Automazione selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** oppure **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

Fare clic su **Aggiungi computer non di Azure**. Verrà visualizzata una nuova finestra del browser con le istruzioni su come installare e configurare Microsoft Monitoring Agent nel computer, in modo che possa iniziare a inviare report alla soluzione. In caso di onboarding di un computer attualmente gestito da System Center Operations Manager, non è richiesto un nuovo agente, dal momento che le informazioni dell'area di lavoro vengono inserite nell'agente esistente.

## <a name="onboard-machines-in-the-workspace"></a>Eseguire l'onboarding di computer nell'area di lavoro

Dall'account di Automazione selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** oppure **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

Selezionare **Gestisci computer**. Verrà visualizzata la pagina **Gestisci computer**. Questa pagina consente di abilitare la soluzione in un set selezionato di computer, in tutti i computer disponibili oppure di abilitare la soluzione per tutti i computer correnti e per tutti i computer futuri.

![Ricerche salvate](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="selected-machines"></a>Computer selezionati

Per abilitare la soluzione per uno o più computer, selezionare **Abilita nei computer selezionati** e fare clic su **Aggiungi** accanto a ogni computer che si vuole aggiungere alla soluzione. Questa attività aggiunge i nomi dei computer selezionati alla query di ricerca salvata nel gruppo di computer per la soluzione.

### <a name="all-available-machines"></a>Tutti i computer disponibili

Per abilitare la soluzione per tutti i computer disponibili, selezionare **Abilita in tutti i computer disponibili**. In questo modo viene disabilitato il controllo per aggiungere computer singolarmente. Questa attività aggiunge tutti i nomi dei computer che inviano report all'area di lavoro alla query di ricerca salvata nel gruppo di computer.

### <a name="all-available-and-future-machines"></a>Tutti i computer disponibili e futuri

Per abilitare la soluzione per tutti i computer disponibili e per tutti i computer futuri, selezionare **Abilita in tutti i computer disponibili e futuri**. Questa opzione consente di eliminare le ricerche salvate e le configurazioni dell'ambito dall'area di lavoro. La soluzione verrà abilitata per tutti i computer di Azure e non di Azure che inviano report all'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Continuare con le esercitazioni sulle soluzioni per informazioni su come usarle.

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)