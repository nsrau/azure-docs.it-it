---
title: Informazioni su come eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario in Automazione di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/16/2018
ms.topic: conceptual
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 0f5d36dfbe614e35256231a91a9e15055e2e81cb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57843624"
---
# <a name="onboard-update-management-change-tracking-and-inventory-solutions"></a>Eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding di computer. Ad esempio, è possibile eseguire l'onboarding della soluzione [da una macchina virtuale](automation-onboard-solutions-from-vm.md), [dall'esplorazione di più computer](automation-onboard-solutions-from-browse.md), dall'account di Automazione o tramite [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni dall'account di Automazione.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com

## <a name="enable-solutions"></a>Abilitare soluzioni

Passare all'account di Automazione e selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE**.

Scegliere l'area di lavoro di Log Analitica e account di automazione e fare clic su **abilitare** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione Inventario](media/automation-onboard-solutions-from-automation-account/onboardsolutions.png)

Quando si abilitano soluzioni, sono supportati solo determinate aree geografiche per il collegamento a un'area di lavoro di Log Analitica e un Account di automazione.

La tabella seguente mostra i mapping supportati:

|**Area dell'area di lavoro di Log Analytics**|**Area di Automazione di Azure**|
|---|---|
|AustraliaSoutheast|AustraliaSoutheast|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS<sup>1</sup>|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|Europa occidentale|Europa occidentale|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP<sup>1</sup>|CentralUSEUAP|

<sup>1</sup> mapping EastUS2EUAP e Stati Uniti orientali per aree di lavoro di Log Analitica per gli account di automazione non sono un mapping di un'area a un'altra esatto, ma è il mapping corretto.

> [!NOTE]
> A causa della domanda, un'area non siano disponibile quando si crea l'area di lavoro di Log Analitica o Account di automazione.  In tal caso, assicurarsi di usare un'area nella tabella precedente che è possibile creare le risorse.

La soluzione di rilevamento delle modifiche e di inventario offre la possibilità di [tenere traccia delle modifiche](automation-vm-change-tracking.md) e dei dati di [inventario](automation-vm-inventory.md) nelle macchine virtuali. In questo passaggio si abilita la soluzione su una macchina virtuale.

Quando si riceve la notifica di completamento del caricamento della soluzione di rilevamento delle modifiche e di inventario, fare clic su **Gestione aggiornamenti** in **GESTIONE DELLA CONFIGURAZIONE**.

La soluzione di gestione degli aggiornamenti consente di gestire gli aggiornamenti e le patch per le macchine virtuali Windows di Azure. È possibile valutare in modo rapido lo stato degli aggiornamenti disponibili, pianificare l'installazione degli aggiornamenti richiesti ed esaminare i risultati della distribuzione per verificare che gli aggiornamenti siano stati applicati correttamente alla macchina virtuale. Questa azione ha abilitato la soluzione per la macchina virtuale.

Selezionare **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**. L'area di lavoro di Log Analitica selezionata è la stessa area di lavoro usato nel passaggio precedente. Fare clic su **Abilita** per caricare la soluzione di gestione degli aggiornamenti. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

![Eseguire l'onboarding della soluzione per gli aggiornamenti](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="scope-configuration"></a>Configurazione dell'ambito

Ogni soluzione usa una configurazione dell'ambito nell'area di lavoro per definire i computer di destinazione della soluzione. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della soluzione a computer specifici. Per accedere alle configurazioni dell'ambito, nell'account di Automazione in **RISORSE CORRELATE** selezionare **Area di lavoro**. Quindi nell'area di lavoro in **ORIGINI DATI DELL'AREA DI LAVORO** selezionare **Configurazioni ambito**.

Se l'area di lavoro selezionata non include ancora le soluzioni Gestione aggiornamenti o Rilevamento modifiche, vengono create le configurazioni di ambito seguenti:

* **MicrosoftDefaultScopeConfig-ChangeTracking**

* **MicrosoftDefaultScopeConfig-Updates**

Se l'area di lavoro selezionata contiene già la soluzione, questa non viene ridistribuita e la configurazione dell'ambito non viene aggiunta.

## <a name="saved-searches"></a>Ricerche salvate

Quando un computer viene aggiunto alle soluzioni Gestione aggiornamenti o Rilevamento modifiche e Inventario, viene aggiunto a una delle due ricerche salvate nell'area di lavoro. Queste ricerche salvate sono query che contengono i computer di destinazione per le soluzioni.

Passare all'account di Automazione e selezionare **Ricerche salvate** in **Generale**. Le due ricerche salvate usate da queste soluzioni sono indicate nella tabella seguente:

|NOME     |Categoria  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |
|MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

Selezionare una ricerca salvata per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

![Ricerche salvate](media/automation-onboard-solutions-from-automation-account/savedsearch.png)

## <a name="onboard-azure-vms"></a>Caricare le macchine virtuali di Azure

Dall'account di Automazione selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** oppure **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

Fare clic su **+ Aggiungi macchine virtuali di Azure** e selezionare una o più VM dall'elenco. Le macchine virtuali che non possono essere abilitate sono non disponibili e non selezionabili. In qualsiasi area, indipendentemente dal percorso dell'Account di automazione possono esistere macchine virtuali di Azure. Nella pagina **Abilita Gestione aggiornamenti** fare clic su **Abilita**. Le macchine virtuali selezionate vengono aggiunte alla ricerca salvata del gruppo di computer per la soluzione.

![Abilitare le macchine virtuali di Azure](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="onboard-a-non-azure-machine"></a>Eseguire l'onboarding di un computer non di Azure

I computer non in Azure devono essere aggiunti manualmente. Dall'account di Automazione selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** oppure **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

Fare clic su **Aggiungi computer non di Azure**. Verrà visualizzata una nuova finestra del browser con le [istruzioni su come installare e configurare Microsoft Monitoring Agent nel computer](../azure-monitor/platform/log-analytics-agent.md), in modo che possa iniziare a inviare report alla soluzione. In caso di onboarding di un computer attualmente gestito da System Center Operations Manager, non è richiesto un nuovo agente, dal momento che le informazioni dell'area di lavoro vengono inserite nell'agente esistente.

## <a name="onboard-machines-in-the-workspace"></a>Eseguire l'onboarding di computer nell'area di lavoro

I computer installati manualmente o i computer che inviano già report all'area di lavoro devono essere aggiunti ad Automazione di Azure per la soluzione da abilitare. Dall'account di Automazione selezionare **Inventario** o **Rilevamento modifiche** in **GESTIONE DELLA CONFIGURAZIONE** oppure **Gestione aggiornamenti** in **GESTIONE AGGIORNAMENTI**.

Selezionare **Gestisci computer**. Verrà visualizzata la pagina **Gestisci computer**. Questa pagina consente di abilitare la soluzione in un set selezionato di computer, in tutti i computer disponibili oppure di abilitare la soluzione per tutti i computer correnti e per tutti i computer futuri. Il pulsante **Gestisci computer** può essere disabilitato se si è scelto in precedenza l'opzione **Abilita in tutti i computer disponibili e futuri**.

![Ricerche salvate](media/automation-onboard-solutions-from-automation-account/managemachines.png)

### <a name="all-available-machines"></a>Tutti i computer disponibili

Per abilitare la soluzione per tutti i computer disponibili, selezionare **Abilita in tutti i computer disponibili**. Con questa azione viene disabilitato il controllo per aggiungere computer singolarmente. Questa attività aggiunge tutti i nomi dei computer che inviano report all'area di lavoro alla query di ricerca salvata nel gruppo di computer. Se l'opzione è selezionata, il pulsante **Gestisci computer** viene disabilitato.

### <a name="all-available-and-future-machines"></a>Tutti i computer disponibili e futuri

Per abilitare la soluzione per tutti i computer disponibili e per tutti i computer futuri, selezionare **Abilita in tutti i computer disponibili e futuri**. Questa opzione consente di eliminare le ricerche salvate e le configurazioni dell'ambito dall'area di lavoro. La soluzione verrà abilitata per tutti i computer di Azure e non di Azure che inviano report all'area di lavoro. Se selezionata, questa azione disabilita il pulsante **Gestisci computer** in modo permanente poiché non è più presente alcuna configurazione dell'ambito.

### <a name="selected-machines"></a>Computer selezionati

Per abilitare la soluzione per uno o più computer, selezionare **Abilita nei computer selezionati** e fare clic su **Aggiungi** accanto a ogni computer che si vuole aggiungere alla soluzione. Questa attività aggiunge i nomi dei computer selezionati alla query di ricerca salvata nel gruppo di computer per la soluzione.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Le soluzioni seguenti sono dipendenti da un'area di lavoro Log Analytics:

* [Gestione degli aggiornamenti](automation-update-management.md)
* [Rilevamento delle modifiche](automation-change-tracking.md)
* [Avviare/arrestare le VM durante gli orari di minore attività](automation-solution-vm-management.md)

Se si decide di che non si desidera più integrare l'account di automazione con un'area di lavoro di Log Analitica, è possibile scollegare l'account direttamente dal portale di Azure.  Prima di continuare, è necessario rimuovere le soluzioni menzionate in precedenza; in caso contrario non sarà possibile continuare con il processo. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile completare i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione e nella pagina Account di automazione selezionare **Area di lavoro collegata** nella sezione contrassegnata **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-onboard-solutions-from-automation-account/automation-unlink-workspace-blade.png).

   Verrà richiesto di confermare l'operazione.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Aggiornare le pianificazioni - Ogni elemento avrà un nome corrispondente alle distribuzioni di aggiornamenti create.

* Gruppi di ruoli di lavoro ibridi creati per la soluzione - Ogni elemento verrà denominato in modo analogo a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Se è stata usata la soluzione per avviare e arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* variables

## <a name="next-steps"></a>Passaggi successivi

Continuare con le esercitazioni sulle soluzioni per informazioni su come usarle.

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)
