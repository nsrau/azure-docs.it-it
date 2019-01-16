---
title: Informazioni su come eseguire l'onboarding delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario per più VM in Automazione di Azure
description: Informazioni su come eseguire l'onboarding in una macchina virtuale di Azure delle soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario, che fanno parte di Automazione di Azure
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: f1607a7d795e3934881429feb18c711a75995e31
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/07/2019
ms.locfileid: "54062945"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>Abilitare le soluzioni Gestione aggiornamenti, Rilevamento modifiche e Inventario in più VM

Automazione di Azure fornisce soluzioni per gestire gli aggiornamenti della sicurezza del sistema operativo, tenere traccia delle modifiche e gestire l'inventario dei componenti installati nei computer. Esistono diversi modi per eseguire l'onboarding di computer. Ad esempio, è possibile eseguire l'onboarding della soluzione [da una macchina virtuale](automation-onboard-solutions-from-vm.md), dall'[account di Automazione](automation-onboard-solutions-from-automation-account.md), durante l'esplorazione delle macchine virtuali o tramite [runbook](automation-onboard-solutions.md). Questo articolo descrive il processo di onboarding di queste soluzioni durante l'esplorazione delle macchine virtuali in Azure.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com

## <a name="enable-solutions"></a>Abilitare soluzioni

Nel portale di Azure passare a **Macchine virtuali**.

Usando le caselle di controllo, selezionare le macchine virtuali che si vuole caricare con Rilevamento modifiche e Inventario o Gestione aggiornamenti. L'onboarding è disponibile per un massimo di tre diversi gruppi di risorse alla volta.

![Elenco di VM](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> Usare i controlli dei filtri per modificare l'elenco di macchine virtuali e quindi fare clic sulla casella di controllo in alto per selezionare tutte le macchine virtuali nell'elenco.

Dalla barra dei comandi fare clic su **Servizi** e selezionare **Rilevamento modifiche**, **Inventario** o **Gestione aggiornamenti**.

> [!NOTE]
> Per **Rilevamento modifiche** e **Inventario** viene usata la stessa soluzione. Quando un servizio è abilitato, viene abilitato anche l'altro.

L'immagine seguente è relativa a Gestione aggiornamenti. Rilevamento modifiche e Inventario hanno lo stesso layout e lo stesso comportamento.

L'elenco di macchine virtuali viene filtrato per visualizzare solo le macchine virtuali che sono nella stessa sottoscrizione e località. Se le macchine virtuali sono in più di tre gruppi di risorse, vengono selezionati i primi tre gruppi di risorse.

### <a name="resource-group-limit"></a> Limitazioni dell'onboarding

Il numero di gruppi di risorse che è possibile usare per l'onboarding è determinato dai [limiti di distribuzione di Resource Manager](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md). Le distribuzioni di Resource Manager, da non confondere con le distribuzioni degli aggiornamenti, sono limitate a 5 gruppi di risorse per distribuzione. Per assicurare l'integrità dell'onboarding, 2 di questi gruppi di risorse sono riservati alla configurazione dell'area di lavoro di Log Analytics, dell'account di Automazione e delle risorse correlate. Rimangono 3 gruppi di risorse selezionabili per la distribuzione.

Usare i controlli dei filtri per selezionare macchine virtuali da sottoscrizioni, località e gruppi di risorse diversi.

![Caricare la soluzione Gestione aggiornamenti](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Rivedere le scelte per l'area di lavoro di Log Analytics e l'account di Automazione. Un'area di lavoro e un account di Automazione esistenti sono selezionati per impostazione predefinita. Per usare un'area di lavoro di Log Analytics e un account di Automazione diversi, fare clic su **PERSONALIZZATO** per selezionarli dalla pagina **Configurazione personalizzata**. Quando si sceglie un'area di lavoro di Log Analytics, viene effettuato un controllo per determinare se è collegata a un account di Automazione. Se viene trovato un account di Automazione collegato, verrà visualizzata la schermata seguente. Al termine, fare clic su **OK**.

![Selezionare un'area di lavoro e un account](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

Se l'area di lavoro selezionata non è collegata a un account di Automazione, verrà visualizzata la schermata seguente. Selezionare un account di Automazione e al termine fare clic su **OK**.

![Nessuna area di lavoro](media/automation-onboard-solutions-from-browse/no-workspace.png)

Quando si abilitano soluzioni, sono supportate solo determinate aree per il collegamento a un'area di lavoro di Log Analytics e un account di Automazione.

La tabella seguente mostra i mapping supportati:

|**Area dell'area di lavoro di Log Analytics**|**Area di Automazione di Azure**|
|---|---|
|AustraliaSoutheast|AustraliaSoutheast|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS|WestCentralUS|
|Europa occidentale|Europa occidentale|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP|CentralUSEUAP|

Deselezionare la casella di controllo accanto alle macchine virtuali che non si vuole abilitare. Le macchine virtuali che non è possibile abilitare sono già deselezionate.

Fare clic su **Abilita** per abilitare la soluzione. Per l'abilitazione della soluzione sono necessari fino a 15 minuti.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

Le soluzioni seguenti sono dipendenti da un'area di lavoro di Log Analytics:

* [Gestione degli aggiornamenti](automation-update-management.md)
* [Rilevamento delle modifiche](automation-change-tracking.md)
* [Avviare/arrestare le VM durante gli orari di minore attività](automation-solution-vm-management.md)

Se si decide che non si vuole più integrare l'account di Automazione con Log Analytics, è possibile scollegare l'account direttamente dal portale di Azure. Prima di procedere, è necessario rimuovere le soluzioni menzionate in precedenza; in caso contrario non sarà possibile continuare con il processo. Vedere l'articolo relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

> [!NOTE]
> È possibile che alcune soluzioni, tra cui versioni precedenti della soluzione di monitoraggio di Azure SQL, abbiano creato asset di automazione e che debbano essere rimosse prima di scollegare l'area di lavoro.

1. Nel portale di Azure aprire l'account di Automazione e nella pagina Account di automazione selezionare **Area di lavoro collegata** nella sezione **Risorse correlate** a sinistra.

2. Nella pagina Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   Verrà richiesto di confermare l'operazione.

3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro di Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Aggiornare le pianificazioni - Ogni elemento avrà un nome corrispondente alle distribuzioni di aggiornamenti create.

* Gruppi di ruoli di lavoro ibridi creati per la soluzione - Ogni elemento verrà denominato in modo analogo a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM
* Avviare e arrestare i runbook delle VM
* variables

## <a name="troubleshooting"></a>risoluzione dei problemi

Durante l'onboarding di più computer, per alcuni computer potrebbe essere visualizzato **Cannot enable** (Abilitazione non consentita). Esistono diversi motivi per cui alcuni computer non possono essere abilitati. Le sezioni seguenti indicano i possibili motivi dello stato **Cannot enable** (Abilitazione non consentita) in una VM durante un tentativo di onboarding.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>La VM invia report a un'area di lavoro diversa: "\<workspaceName\>".  Modificare la configurazione per usarla per l'abilitazione

**Causa**: questo errore indica che la macchina che si sta provando a caricare segnala i dati a un'altra area di lavoro.

**Soluzione**: fare clic su **Usa come configurazione** per modificare l'account di Automazione e l'area di lavoro di Log Analytics di destinazione.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>La VM invia report a un'area di lavoro che non è disponibile in questa sottoscrizione

**Causa**: l'area di lavoro a cui la macchina virtuale segnala i dati:

* È in una sottoscrizione diversa
* Non esiste più
* È in un gruppo di risorse per cui non si hanno autorizzazioni di accesso

**Soluzione**: trovare l'account di Automazione associato all'area di lavoro a cui la macchina virtuale segnala i dati e caricare la macchina virtuale modificando la configurazione dell'ambito.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>La versione o la distribuzione del sistema operativo della macchina virtuale non è supportata

**Causa:** la soluzione non è supportata per tutte le distribuzioni Linux o tutte le versioni di Windows.

**Soluzione:** vedere l'[elenco di client supportati](automation-update-management.md#clients) per la soluzione.

### <a name="classic-vms-cannot-be-enabled"></a>Le VM classiche non possono essere abilitate

**Causa**: le macchine virtuali che usano il modello di distribuzione classica non sono supportate.

**Soluzione**: eseguire la migrazione della macchina virtuale al modello di distribuzione Resource Manager. Per altre informazioni in proposito, vedere [Eseguire la migrazione di risorse del modello di distribuzione classica](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

### <a name="vm-is-stopped-deallocated"></a>La macchina virtuale viene arrestata (deallocata).

**Causa**: lo stato della macchina virtuale non è **In esecuzione**.

**Soluzione**: per caricare una macchina virtuale in una soluzione la macchina virtuale deve essere in esecuzione. Fare clic sul collegamento inline **Avvia macchina virtuale** per avviare la VM senza uscire dalla pagina.

## <a name="next-steps"></a>Passaggi successivi

Ora che la soluzione è abilitata per le macchine virtuali, vedere l'articolo Panoramica di Gestione aggiornamenti per informazioni su come visualizzare la valutazione degli aggiornamenti per i computer.

> [!div class="nextstepaction"]
> [Gestione aggiornamenti - Visualizzare la valutazione degli aggiornamenti](./automation-update-management.md#viewing-update-assessments)

Esercitazioni aggiuntive sulle soluzioni e su come usarle:

* [Esercitazione - Gestire gli aggiornamenti per la VM](automation-tutorial-update-management.md)

* [Esercitazione - Identificare il software in una VM](automation-tutorial-installed-software.md)

* [Esercitazione - Risolvere i problemi delle modifiche di una VM](automation-tutorial-troubleshoot-changes.md)